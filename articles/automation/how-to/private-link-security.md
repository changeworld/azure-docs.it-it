---
title: Usare il collegamento privato di Azure per connettere in modo sicuro le reti ad automazione di Azure
description: Usare il collegamento privato di Azure per connettere in modo sicuro le reti ad automazione di Azure
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: f3c9197faaae89e0ffb238f987ee66dafea8abdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579795"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Usare il collegamento privato di Azure per connettere in modo sicuro le reti ad automazione di Azure

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della VNet, in modo da portare il servizio di automazione nella VNet. Il traffico di rete tra i computer nella VNet e l'account di automazione attraversa la VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

Ad esempio, si dispone di un VNet in cui è stato disabilitato l'accesso a Internet in uscita. Tuttavia, si vuole accedere al proprio account di automazione privatamente e usare le funzionalità di automazione come webhook, configurazione dello stato e processi Runbook nei ruoli di lavoro ibridi per Runbook. Inoltre, si vuole che gli utenti abbiano accesso all'account di automazione solo tramite il VNET.  La distribuzione dell'endpoint privato consente di ottenere questi obiettivi.

Questo articolo illustra quando usare e come configurare un endpoint privato con l'account di automazione.

![Panoramica concettuale del collegamento privato per automazione di Azure](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Il supporto dei collegamenti privati con automazione di Azure è disponibile solo nei cloud commerciali di Azure e in Azure per enti pubblici statunitensi.

## <a name="advantages"></a>Vantaggi

Con il collegamento privato è possibile:

- Connettersi privatamente ad automazione di Azure senza aprire alcun accesso alla rete pubblica.
- Connettersi privatamente a monitoraggio di Azure Log Analytics area di lavoro senza aprire alcun accesso alla rete pubblica.

    >[!NOTE]
    >Un endpoint privato separato per l'area di lavoro Log Analytics è necessario se l'account di automazione è collegato a un'area di lavoro Log Analytics per l'invio dei dati del processo e quando sono state abilitate funzionalità quali Gestione aggiornamenti, Rilevamento modifiche e inventario, configurazione stato o Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Per altre informazioni sul collegamento privato per monitoraggio di Azure, vedere [usare il collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](../../azure-monitor/logs/private-link-security.md).

- Assicurarsi che l'accesso ai dati di automazione avvenga solo tramite reti private autorizzate.
- Prevenire i dati exfiltration dalle reti private definendo la risorsa di automazione di Azure che si connette tramite l'endpoint privato.
- Connettere in modo sicuro la rete locale privata ad automazione di Azure usando ExpressRoute e il collegamento privato.
- Mantieni tutto il traffico all'interno della rete backbone Microsoft Azure.

Per altre informazioni, vedere [Vantaggi principali del collegamento privato](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Limitazioni

- Nell'implementazione corrente del collegamento privato, i processi cloud degli account di automazione non possono accedere alle risorse di Azure protette tramite endpoint privato. Ad esempio Azure Key Vault, SQL di Azure, l'account di archiviazione di Azure e così via. Per aggirare questo problema, usare invece un ruolo di [lavoro ibrido per Runbook](../automation-hybrid-runbook-worker.md) .
- È necessario usare la versione più recente dell' [agente di log Analytics](../../azure-monitor/agents/log-analytics-agent.md) per Windows o Linux.
- Il [Gateway log Analytics](../../azure-monitor/agents/gateway.md) non supporta il collegamento privato.

## <a name="how-it-works"></a>Funzionamento

Il collegamento privato di automazione di Azure connette uno o più endpoint privati (e quindi le reti virtuali in cui sono contenuti) alla risorsa dell'account di automazione. Questi endpoint sono computer che usano webhook per avviare un Runbook, i computer che ospitano il ruolo di lavoro ibrido per Runbook e i nodi DSC (Desired state Configuration).

Dopo aver creato gli endpoint privati per l'automazione, viene eseguito il mapping di ognuno degli URL di automazione con accesso pubblico a un endpoint privato nella VNet. L'utente o un computer può contattare direttamente gli URL di automazione.

### <a name="webhook-scenario"></a>Scenario webhook

È possibile avviare manuali operativi eseguendo un POST sull'URL del webhook. Ad esempio, l'URL è simile al seguente: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Scenario di lavoro ibrido per Runbook

La funzionalità user Hybrid Runbook Worker di automazione di Azure consente di eseguire manuali operativi direttamente nel computer Azure o non Azure, inclusi i server registrati con i server abilitati per Azure Arc. Dal computer o dal server che ospita il ruolo, è possibile eseguire manuali operativi direttamente su di esso e sulle risorse nell'ambiente per gestire tali risorse locali.

Un endpoint JRDS viene usato dal ruolo di lavoro ibrido per avviare/arrestare manuali operativi, scaricare manuali operativi per il ruolo di lavoro e inviare di nuovo il flusso del log del processo al servizio di automazione.Dopo aver abilitato l'endpoint JRDS, l'URL avrà un aspetto simile al seguente: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Ciò garantisce che l'esecuzione del Runbook nel ruolo di lavoro ibrido connesso alla rete virtuale di Azure sia in grado di eseguire processi senza dover aprire una connessione in uscita a Internet.  

> [!NOTE]
>Con l'implementazione corrente di collegamenti privati per automazione di Azure, supporta solo i processi in esecuzione nel ruolo di lavoro ibrido per Runbook connesso a una rete virtuale di Azure e non supporta i processi cloud.

## <a name="hybrid-worker-scenario-for-update-management"></a>Scenario di lavoro ibrido per Gestione aggiornamenti  

Il ruolo di lavoro ibrido per Runbook di sistema supporta un set di manuali operativi nascosti usato dalla funzionalità Gestione aggiornamenti progettati per installare gli aggiornamenti specificati dall'utente nei computer Windows e Linux. Quando Gestione aggiornamenti di automazione di Azure è abilitata, tutti i computer connessi all'area di lavoro Log Analytics vengono automaticamente configurati come ruolo di lavoro ibrido per Runbook di sistema.

Per comprendere & configurare Gestione aggiornamenti esaminare [Gestione aggiornamenti](../update-management/overview.md). La funzionalità Gestione aggiornamenti presenta una dipendenza da un'area di lavoro Log Analytics e pertanto richiede il collegamento dell'area di lavoro con un account di automazione. Un'area di lavoro Log Analytics archivia i dati raccolti dalla soluzione e ospita le ricerche nei log e le visualizzazioni.

Se si desidera che i computer configurati per la gestione degli aggiornamenti si connettano a automazione & Log Analytics area di lavoro in modo sicuro su canale di collegamento privato, è necessario abilitare il collegamento privato per l'area di lavoro Log Analytics collegata all'account di automazione configurato con collegamento privato.

È possibile controllare il modo in cui è possibile raggiungere un'area di lavoro Log Analytics dall'esterno degli ambiti dei collegamenti privati attenendosi alla procedura descritta in [configurare log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics). Se si imposta **Consenti l'accesso alla rete pubblica per l’inserimento** su **No**, i computer esterni agli ambiti connessi non possono caricare dati in questa area di lavoro. Se si imposta **Consenti l'accesso alla rete pubblica per le query** su **No**, i computer esterni agli ambiti non possono accedere ai dati in questa area di lavoro.

Usare la sottorisorsa di destinazione **DSCAndHybridWorker** per abilitare il collegamento privato per i ruoli di lavoro ibridi del sistema & utente.

> [!NOTE]
> I computer ospitati all'esterno di Azure gestiti da Gestione aggiornamenti e connessi alla VNet di Azure tramite peering privato ExpressRoute, tunnel VPN e reti virtuali con peering usando endpoint privati supportano il collegamento privato.

### <a name="state-configuration-agentsvc-scenario"></a>Scenario di configurazione dello stato (agentsvc)

La configurazione dello stato fornisce un servizio di gestione della configurazione di Azure che consente di scrivere, gestire e compilare configurazioni di PowerShell DSC (Desired state Configuration) per i nodi in qualsiasi data center locale o cloud.

L'agente nel computer esegue la registrazione con il servizio DSC, quindi usa l'endpoint del servizio per eseguire il pull della configurazione DSC. L'endpoint del servizio Agent ha un aspetto simile al seguente: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

L'URL per l'endpoint pubblico & privato sarebbe lo stesso, ma verrebbe mappato a un indirizzo IP privato quando è abilitato il collegamento privato.

## <a name="planning-based-on-your-network"></a>Pianificazione basata sulla rete

Prima di configurare la risorsa dell'account di automazione, prendere in considerazione i requisiti di isolamento rete. Valutare l'accesso delle reti virtuali alla rete Internet pubblica e le restrizioni di accesso all'account di automazione (inclusa la configurazione di un ambito del gruppo di collegamento privato nei log di monitoraggio di Azure se integrato con l'account di automazione). Includere anche una revisione dei [record DNS](./automation-region-dns-records.md) del servizio di automazione come parte del piano per garantire che le funzionalità supportate funzionino senza problemi.

### <a name="connect-to-a-private-endpoint"></a>Connettersi a un endpoint privato

Creare un endpoint privato per connettere la rete. È possibile crearlo nel [portale di Azure centro collegamenti privati](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Una volta applicate le modifiche apportate a publicNetworkAccess e al collegamento privato, possono essere necessari fino a 35 minuti per rendere effettive le modifiche.

In questa sezione verrà creato un endpoint privato per l'account di automazione.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa > rete > centro collegamenti privati**.

2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

3. In **creare una macchina virtuale-nozioni di base** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome | Immettere il *PrivateEndpoint*. |
    | Region | Selezionare **YourRegion**. |
    |||

4. Selezionare **Avanti: Risorsa**.

5. In **Crea una risorsa endpoint privato** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. Automation/automationAccounts**. |
    | Risorsa |Seleziona *myAutomationAccount*|
    |Sottorisorsa di destinazione |Selezionare *webhook* o *DSCAndHybridWorker* a seconda dello scenario.|
    |||

6. Selezionare **Avanti: Configurazione**.

7. In **Crea un endpoint privato-configurazione** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Select *(nuovo) privatelink. Azure-Automation.NET* |
    |||

8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

9. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Nel **centro collegamenti privati** selezionare **endpoint privati** per visualizzare la risorsa di collegamento privato.

![Collegamento privato risorse di automazione](./media/private-link-security/private-link-automation-resource.png)

Selezionare la risorsa per visualizzare tutti i dettagli. In questo modo viene creato un nuovo endpoint privato per l'account di automazione e viene assegnato un indirizzo IP privato dalla rete virtuale. Lo **stato della connessione** viene visualizzato come **approvato**.

Analogamente, viene creato un nome di dominio completo (FQDN) univoco per la configurazione dello stato (agentsvc) e per il runtime del processo di lavoro ibrido per Runbook (jrds). A ognuno di essi viene assegnato un indirizzo IP separato dalla VNet e lo **stato della connessione** viene visualizzato come **approvato**.

Se il consumer del servizio dispone delle autorizzazioni RBAC di Azure per la risorsa di automazione, può scegliere il metodo di approvazione automatica. In questo caso, quando la richiesta raggiunge la risorsa del provider di automazione, non è richiesta alcuna azione da parte del provider di servizi e la connessione viene approvata automaticamente.

## <a name="set-public-network-access-flags"></a>Impostare i flag di accesso alla rete pubblica

È possibile configurare un account di automazione per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza della rete. Se si vuole limitare l'accesso all'account di automazione solo dall'interno della VNet e non consentire l'accesso da Internet pubblico, è possibile impostare la `publicNetworkAccess` proprietà su `$false` .

Quando l'impostazione **accesso alla rete pubblica** è impostata su `$false` , sono consentite solo le connessioni tramite endpoint privati e tutte le connessioni tramite endpoint pubblici vengono negate con un messaggio di errore non autorizzato e lo stato HTTP 401.

Lo script di PowerShell seguente mostra come `Get` e `Set` la proprietà di **accesso alla rete pubblica** a livello di account di automazione:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

È anche possibile controllare la proprietà di accesso alla rete pubblica dal portale di Azure. Dall'account di automazione selezionare **isolamento rete** nel riquadro a sinistra nella sezione **Impostazioni account** . Quando l'impostazione accesso alla rete pubblica è impostata su **No**, sono consentite solo le connessioni sugli endpoint privati e tutte le connessioni sugli endpoint pubblici vengono negate.

![Impostazione di accesso alla rete pubblica](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Configurazione del DNS

Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. È necessario rivedere e aggiornare la configurazione DNS per la connessione tramite l'endpoint privato.

L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa Collegamento privato.

Per configurare le impostazioni DNS per gli endpoint privati si può procedere nei modi seguenti:

* Usare il file host (consigliato solo per i test). È possibile usare il file host in una macchina virtuale per eseguire prima l'override usando DNS per la risoluzione dei nomi. La voce DNS dovrebbe essere simile all'esempio seguente: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Usare una [zona DNS privata](../../dns/private-dns-privatednszone.md). È possibile usare le zone DNS private per sostituire la risoluzione DNS per un determinato endpoint privato. Una zona DNS privato può essere collegata alla rete virtuale per risolvere domini specifici. Per consentire all'agente nella macchina virtuale di comunicare tramite l'endpoint privato, creare un record DNS privato come `privatelink.azure-automation.net` . Aggiungere un nuovo mapping *del record DNS a all'* indirizzo IP dell'endpoint privato.

* Usare il server di trasmissione DNS (facoltativo). È possibile usare il server di trasmissione DNS per sostituire la risoluzione DNS per una particolare risorsa di collegamento privato. Se il [server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, si può creare una regola di invio DNS in modo da usare una zona DNS privato per semplificare la configurazione per tutte le risorse Collegamento privato.

Per altre informazioni, vedere [Configurazione DNS dell'endpoint privato di Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](../../private-link/private-endpoint-overview.md).