---
title: Connettere macchine virtuali ibride ad Azure su larga scala
description: Questo articolo illustra come connettere i computer ad Azure usando i server abilitati per Azure ARC usando un'entità servizio.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175941"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connettere macchine virtuali ibride ad Azure su larga scala

È possibile abilitare i server abilitati per Azure Arc per più computer Windows o Linux nell'ambiente in uso con diverse opzioni flessibili, a seconda dei requisiti. Usando lo script modello fornito è possibile automatizzare tutti i passaggi dell'installazione, inclusa la creazione della connessione ad Azure Arc. Tuttavia, è necessario eseguire questo script in modo interattivo con un account con autorizzazioni elevate, nel computer di destinazione e in Azure.

Per connettere i computer ai server abilitati per Azure Arc, è possibile usare un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory invece di usare l'identità con privilegi per connettere in modo [interattivo il computer](onboard-portal.md). Un'entità servizio è un'identità di gestione limitata speciale a cui viene concessa solo l'autorizzazione minima necessaria per connettere computer ad Azure tramite il comando `azcmagent`. Questa soluzione è più sicura rispetto all'uso di un account con autorizzazioni elevate, come un Amministratore tenant, e segue le procedure consigliate per la sicurezza del controllo di accesso. L'entità servizio viene usata solo durante l'onboarding, non viene usata per altri scopi.  

I metodi di installazione per installare e configurare l'agente di Connected Machine richiedono che il metodo automatizzato usato disponga delle autorizzazioni di amministratore per i computer. In Linux, tramite l'account radice, e in Windows, come membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti. Per informazioni sulle aree supportate e altre considerazioni correlate, vedere [aree di Azure supportate](overview.md#supported-regions). Vedere anche la [Guida alla pianificazione su scala](plan-at-scale-deployment.md) per comprendere i criteri di progettazione e distribuzione, nonché le raccomandazioni di gestione e monitoraggio.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Creare un'entità servizio per l'onboarding su larga scala

È possibile usare [Azure PowerShell](/powershell/azure/install-az-ps) per creare un'entità servizio con il cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Oppure è possibile seguire la procedura descritta in [Creare un'entità servizio usando il portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) per completare questa attività.

> [!NOTE]
> Prima di creare un'entità servizio, l'account deve essere un membro del ruolo **proprietario** o **amministratore accesso utenti** nella sottoscrizione che si vuole usare per l'onboarding. Se non si dispone di autorizzazioni sufficienti per configurare le assegnazioni di ruolo, è possibile che l'entità servizio sia stata creata, ma non sarà in grado di eseguire l'onboarding dei computer.
>

Per creare l'entità servizio usando PowerShell, seguire questa procedura.

1. Eseguire il comando seguente. È necessario archiviare l'output del cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) in una variabile, altrimenti non sarà possibile recuperare la password necessaria in un passaggio successivo.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Eseguire il comando seguente per recuperare la password archiviata nella variabile `$sp`:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Nell'output, individuare il valore della password nel campo **password** e copiarlo. Individuare inoltre il valore nel campo **ApplicationId** e copiarlo. Salvarli in una posizione sicura per usarli in seguito. Se si dimentica o si perde la password dell'entità servizio, è possibile reimpostarla usando il cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

I valori delle proprietà seguenti vengono usati con i parametri passati al `azcmagent`:

* Il valore dalla proprietà **ApplicationId** viene usata per il valore del parametro `--service-principal-id`
* Il valore dalla proprietà **password** viene usato per il parametro `--service-principal-secret` per la connessione dell'agente.

> [!NOTE]
> Usare la proprietà **ApplicationId** dell'entità servizio e non la proprietà **Id**.
>

Il ruolo di **Onboarding di Azure Connected Machine** contiene solo le autorizzazioni necessarie per eseguire l'onboarding di un computer. È possibile assegnare l'autorizzazione dell'entità servizio per consentire al suo ambito di includere un gruppo di risorse o una sottoscrizione. Per aggiungere l'assegnazione di ruolo, vedere [assegnare ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md) o [assegnare i ruoli di Azure usando l'interfaccia](../../role-based-access-control/role-assignments-cli.md)della riga di comando di Azure

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generare lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

1. In alto a sinistra nella pagina **Server - Azure Arc** selezionare **Aggiungi**.

1. Nella pagina **Selezione metodo** selezionare il riquadro **Aggiungi più server** e quindi selezionare **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale. La località può essere uguale o diversa da quella del gruppo di risorse.

1. Nella pagina **Prerequisiti** esaminare le informazioni e quindi selezionare **Avanti: Dettagli risorsa**.

1. Nella pagina **Dettagli risorsa** specificare quanto segue:

    1. Nell'elenco a discesa **Gruppo di risorse** selezionare il gruppo di risorse da cui verrà gestito il computer.
    1. Nell'elenco a discesa **Area** selezionare l'area di Azure in cui archiviare i metadati dei server.
    1. Nell'elenco a discesa **sistema operativo** selezionare il sistema operativo in cui è configurato lo script per l'esecuzione.
    1. Se il computer comunica tramite un server proxy per connettersi a Internet, specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer userà per le comunicazioni. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`.
    1. Selezionare **Avanti: autenticazione**.

1. Nella pagina **autenticazione** selezionare **Arc-for-server** nell'elenco a discesa **entità servizio** .  Quindi selezionare, **Next: Tags**.

1. Nella pagina **Tag** esaminare l'impostazione predefinita e suggerita di **Tag di località fisica** e immettere un valore oppure specificare una o più opzioni per **Tag personalizzati** in base ai propri standard.

1. Selezionare **Avanti: Scarica ed esegui script**.

1. Nella pagina **Scarica ed esegui script** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

Per Windows, viene richiesto di salvare `OnboardingScript.ps1` e per Linux `OnboardingScript.sh` nel computer.

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

Prendendo il modello di script creato in precedenza, è possibile installare e configurare l'agente del computer connesso su più computer ibridi Linux e Windows usando lo strumento di automazione preferito per le organizzazioni. Lo script esegue passaggi analoghi descritti in [connettere macchine ibride ad Azure dall'articolo portale di Azure](onboard-portal.md) . La differenza si trova nel passaggio finale, in cui viene stabilita la connessione ad Azure ARC usando il `azcmagent` comando usando l'entità servizio.

Le impostazioni seguenti consentono di configurare il comando `azcmagent` da usare per l'entità servizio.

* `service-principal-id` : Identificatore univoco (GUID) che rappresenta l'ID applicazione dell'entità servizio.
* `service-principal-secret` | Password dell'entità servizio.
* `tenant-id` : identificatore univoco (GUID), che rappresenta l'istanza dedicata di Azure AD.
* `subscription-id` : ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si desiderano collocare i computer.
* `resource-group` : nome del gruppo di risorse in cui si desidera che appartengano i computer connessi.
* `location` : vedere le [aree di Azure supportate](overview.md#supported-regions). La località può essere uguale o diversa da quella del gruppo di risorse.
* `resource-name` : (*facoltativo*) usato per la rappresentazione delle risorse di Azure del computer locale. Se non si specifica questo valore, viene usato il nome host del computer.

Per altre informazioni sullo strumento della riga di comando `azcmagent`, vedere la pagina relativa al [riferimento Azcmagent](./manage-agent.md).

>[!NOTE]
>Lo script di Windows PowerShell supporta solo l'esecuzione da una versione a 64 bit di Windows PowerShell.
>

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificare che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/vm/vminsights-enable-policy.md)e molto altro ancora.

- Altre informazioni sull'[agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo con Monitoraggio di Azure per le macchine virtuali, gestirli usando manuali operativi di automazione o funzionalità come Gestione aggiornamenti oppure usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).
