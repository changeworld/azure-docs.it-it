---
title: Gestire un account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire l'account RunAs con PowerShell o nel portale di Azure.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f170fc948f136f4f46634e7ae2645ed2eb357afa
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096460"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gestire un account RunAs di Automazione di Azure

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse nel modello di distribuzione Azure Resource Manager o di Azure classico usando manuali operativi di automazione e altre funzionalità di automazione. Questo articolo fornisce indicazioni su come gestire un account RunAs o un account RunAs classico.

Per altre informazioni sull'autenticazione dell'account di automazione di Azure e le linee guida relative agli scenari di automazione dei processi, vedere [Panoramica dell'autenticazione degli account di automazione](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Rinnovare un certificato autofirmato

Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. Prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. È possibile rinnovarlo in qualsiasi momento prima della scadenza.

Quando si rinnova il certificato autofirmato, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

>[!NOTE]
>Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminare e creare nuovamente il certificato autofirmato.

>[!NOTE]
>Se l'account RunAs è stato configurato per l'uso di un certificato rilasciato dall'autorità di certificazione globale (enterprise) e si usa questa opzione per rinnovare il certificato autofirmato, il certificato globale (enterprise) viene sostituito da un certificato autofirmato.

Usare la procedura seguente per rinnovare il certificato autofirmato.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'account di Automazione selezionare **Account RunAs** nella sezione di impostazioni dell'account.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Riquadro delle proprietà dell'account di automazione.":::

1. Nella pagina proprietà **account RunAs** selezionare **account RunAs** o **account RunAs classico** a seconda dell'account di cui è necessario rinnovare il certificato.

1. Nella pagina **Proprietà** per l'account selezionato selezionare **Rinnova certificato**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Rinnovare il certificato per l'account RunAs.":::

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Concedere le autorizzazioni dell'account RunAs in altre sottoscrizioni

Automazione di Azure supporta l'uso di un singolo account di automazione da una sottoscrizione e l'esecuzione di manuali operativi su Azure Resource Manager risorse tra più sottoscrizioni. Questa configurazione non supporta il modello di distribuzione classica di Azure.

L'entità servizio dell'account RunAs viene assegnata al ruolo [collaboratore](../role-based-access-control/built-in-roles.md#contributor) nell'altra sottoscrizione o a autorizzazioni più restrittive. Per altre informazioni, vedere [controllo degli accessi in base al ruolo](automation-role-based-access-control.md) in automazione di Azure. Per assegnare l'account RunAs al ruolo nell'altra sottoscrizione, l'account utente che esegue questa attività deve essere membro del ruolo **proprietario** di tale sottoscrizione.

> [!NOTE]
> Questa configurazione supporta solo più sottoscrizioni di un'organizzazione che usano un tenant di Azure AD comune.

Prima di concedere le autorizzazioni per l'account RunAs, è necessario prima prendere nota del nome visualizzato dell'entità servizio da assegnare.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Dall'account di Automazione selezionare **Account RunAs** in **impostazioni account**.
1. Selezionare **Account RunAs di Azure**.
1. Copiare o prendere nota del valore per **nome visualizzato** nella pagina **account RunAs di Azure** .

Per informazioni dettagliate su come aggiungere assegnazioni di ruolo, vedere gli articoli seguenti a seconda del metodo che si vuole usare.

* [Assegnare i ruoli di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md)
* [Assegnare i ruoli di Azure usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Assegnare i ruoli di Azure usando l'interfaccia della riga di comando](../role-based-access-control/role-assignments-cli.md)
* [Assegnare i ruoli di Azure usando l'API REST](..//role-based-access-control/role-assignments-rest.md)

Dopo aver assegnato l'account RunAs al ruolo, nella Runbook specificare `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` per impostare il contesto della sottoscrizione da usare. Per ulteriori informazioni, vedere [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Limitare le autorizzazioni dell'account RunAs

Per controllare la destinazione di Automazione rispetto alle risorse in Azure, è possibile eseguire lo script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Questo script modifica l'entità servizio dell'account RunAs esistente per creare e usare una definizione del ruolo personalizzata. Il ruolo dispone delle autorizzazioni per tutte le risorse eccetto [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Dopo aver eseguito lo script **Update-AutomationRunAsAccountRoleAssignments.ps1**, i runbook che accedono a Key Vault con gli account RunAs non funzionano più. Prima di eseguire lo script, è necessario rivedere i runbook nell'account per le chiamate ad Azure Key Vault. Per consentire l'accesso a Key Vault dai runbook di Automazione di Azure, è necessario [aggiungere l'account RunAs alle autorizzazioni di Key Vault](#add-permissions-to-key-vault).

Se è necessario limitare ulteriormente le operazioni che l'entità servizio RunAs può eseguire, è possibile aggiungere altri tipi di risorse all' `NotActions` elemento della definizione di ruolo personalizzata. L'esempio seguente limita l'accesso a `Microsoft.Compute/*`. Se si aggiunge questo tipo di risorsa a `NotActions` per la definizione del ruolo, il ruolo non potrà accedere alle risorse di calcolo. Per altre informazioni sulle definizioni dei ruoli, vedere [Informazioni sulle definizioni dei ruoli per le risorse di Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

È possibile determinare se l'entità servizio usata dall'account RunAs ha assegnato il ruolo **collaboratore** o uno personalizzato.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare all'account di Automazione selezionare **Account RunAs** nella sezione di impostazioni dell'account.
1. Selezionare **Account RunAs di Azure**.
1. Selezionare **Ruolo** per individuare la definizione del ruolo usata.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verificare il ruolo account RunAs." lightbox="media/manage-runas-account/verify-role-expanded.png":::

È anche possibile determinare la definizione del ruolo usata dagli account RunAs per più sottoscrizioni o account di Automazione. A tale scopo, usare lo script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) nella PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni a Key Vault

È possibile consentire ad Automazione di Azure di verificare se Key Vault e l'entità servizio dell'account RunAs usano una definizione del ruolo personalizzata. È necessario:

* Concedere le autorizzazioni a Key Vault.
* Impostare i criteri di accesso.

È possibile utilizzare lo script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) nel PowerShell Gallery per concedere le autorizzazioni dell'account RunAs a Key Vault. Per ulteriori informazioni sull'impostazione delle autorizzazioni per Key Vault, vedere [assegnare un criterio di accesso key Vault](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Risolvere i problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per l'account RunAs o l'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tra le istanze di configurazione errata possibili ci sono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà dell'Account RunAs.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Configurazione dell'account RunAs incompleta.":::

Quando si seleziona l'account RunAs, il riquadro delle proprietà dell'account mastra il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile [eliminare](delete-run-as-account.md) e [ricreare](create-run-as-account.md) l'account RunAs.

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
* Per creare o ricreare un account RunAs, vedere [creare un account RunAs](create-run-as-account.md).
* Se non è più necessario usare un account RunAs, vedere [eliminare un account RunAs](delete-run-as-account.md).