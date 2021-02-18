---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 8905c3e4dfa1053646ede5c0b62149844e21ee7d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617247"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. 

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Tipo**: la notifica o l'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Name**: un identificatore univoco all'interno del gruppo di azioni.
* **Dettagli**: i dettagli corrispondenti che variano in base al *tipo*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.

1. Selezionare **avvisi**, quindi fare clic su **Gestisci azioni**.

    ![Pulsante Gestisci azioni](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi pertinenti nell'esperienza della procedura guidata.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Configurare le impostazioni del gruppo di azioni di base

In **Dettagli progetto**:

Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui viene salvato il gruppo di azioni.

In **Dettagli istanza**:

1. Immettere un **nome** per il gruppo di azioni.

1. Immettere un **nome visualizzato**. Il nome visualizzato viene usato al posto di un nome completo del gruppo di azioni quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Configurare le notifiche

1. Fare clic sul pulsante **Avanti: notifiche >** per passare alla scheda **notifiche** oppure selezionare la scheda **notifiche** nella parte superiore della schermata.

1. Consente di definire un elenco di notifiche da inviare quando viene attivato un avviso. Fornire quanto segue per ogni notifica:

    a. **Tipo di notifica**: selezionare il tipo di notifica che si vuole inviare. Le opzioni disponibili sono:
      * Invia un messaggio di posta elettronica al ruolo Azure Resource Manager-invia un messaggio di posta elettronica agli utenti assegnati a determinati ruoli ARM di livello sottoscrizione.
      * Posta elettronica/SMS/push/Voice: inviare questi tipi di notifiche a destinatari specifici.
    
    b. **Nome**: immettere un nome univoco per la notifica.

    c. **Dettagli**: in base al tipo di notifica selezionato, immettere un indirizzo di posta elettronica, un numero di telefono e così via.
    
    d. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](./alerts-common-schema.md), che fornisce il vantaggio di avere un singolo payload degli avvisi estendibile e unificato per tutti i servizi di avviso in Monitoraggio di Azure.

    ![Scheda notifiche](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Configurare le azioni

1. Fare clic sul pulsante **Avanti: azioni >** per passare alla scheda **azioni** oppure selezionare la scheda **azioni** nella parte superiore della schermata.

1. Definire un elenco di azioni da attivare quando viene attivato un avviso. Per ogni azione, specificare quanto segue:

    a. **Tipo di azione**: selezionare Runbook di automazione, funzione di Azure, ITSM, app per la logica, webhook sicuro, webhook.
    
    b. **Nome**: immettere un nome univoco per l'azione.

    c. **Dettagli**: in base al tipo di azione, immettere un URI del webhook, un'app di Azure, una connessione ITSM o Runbook di automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    d. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](./alerts-common-schema.md), che fornisce il vantaggio di avere un singolo payload degli avvisi estendibile e unificato per tutti i servizi di avviso in Monitoraggio di Azure.
    
    ![Scheda azioni](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Creare il gruppo di azione

1. Volendo è possibile esplorare le impostazioni di **Tag**. Ciò consente di associare le coppie chiave/valore al gruppo di azioni per la categorizzazione ed è una funzionalità disponibile per qualsiasi risorsa di Azure.

    ![Scheda Tag](./media/action-groups/action-group-4-tags.png)
    
1. Fare clic su **Rivedi e crea** per rivedere le impostazioni. Verrà eseguita una convalida rapida degli input per assicurarsi che tutti i campi obbligatori siano selezionati. Se sono presenti problemi, verranno segnalati qui. Dopo aver esaminato le impostazioni, fare clic su **Crea** per eseguire il provisioning del gruppo di azioni.
    
    ![Scheda verifica + crea](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Quando si configura un'azione per inviare una notifica a un utente tramite posta elettronica o SMS, viene ricevuta una conferma che indica che sono stati aggiunti al gruppo di azioni.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azioni, è possibile visualizzare **Gruppi di azioni** selezionando **Gestisci azioni** dalla pagina di destinazione **Avvisi** nel riquadro **Monitoraggio**. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Vedere [Limiti del servizio di sottoscrizione per il monitoraggio](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) per i limiti numerici per ognuno degli elementi seguenti.  

### <a name="automation-runbook"></a>Runbook di Automazione
Vedere i [Limiti del servizio per la sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sui payload di Runbook.

Un Gruppo di azioni può contenere un numero limitato di azioni di tipo Runbook. 

### <a name="azure-app-push-notifications"></a>Notifiche push dell'app Azure
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app Azure.

### <a name="email"></a>Email
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager
Inviare il messaggio di posta elettronica ai membri del ruolo della sottoscrizione. Il messaggio di posta elettronica verrà inviato solo ai membri **utente di Azure AD** del ruolo. Il messaggio di posta elettronica non verrà inviato a gruppi di Azure AD o entità servizio.

Un messaggio di posta elettronica di notifica viene inviato solo all'indirizzo di *posta elettronica primario* .

Se non si ricevono notifiche sul messaggio di *posta elettronica principale*, è possibile provare a seguire questa procedura:

1. In portale di Azure passare a *Active Directory*.
2. Fare clic su tutti gli utenti (nel riquadro sinistro), viene visualizzato l'elenco di utenti (nel riquadro destro).
3. Selezionare l'utente per il quale si desidera esaminare le informazioni di *posta elettronica primarie* .

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Esempio su come verificare il profilo utente."border="true":::

4. In profilo utente in informazioni di contatto se la scheda "posta elettronica" è vuota, fare clic sul pulsante *modifica* nella parte superiore e aggiungere il *messaggio di posta elettronica principale* e premere il pulsante *Salva* nella parte superiore.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Esempio su come aggiungere un messaggio di posta elettronica primario."border="true":::

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

### <a name="function"></a>Funzione
Chiama un endpoint di trigger HTTP esistente in [Funzioni di Azure](../../azure-functions/functions-get-started.md).

Un Gruppo di azioni può contenere un numero limitato di funzioni.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](../platform/itsmc-overview.md).

Un Gruppo di azioni può contenere un numero limitato di azioni di gestione dei servizi IT. 

### <a name="logic-app"></a>App per la logica
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app per la logica.

### <a name="secure-webhook"></a>Webhook protetto

> [!NOTE]
> L'uso dell'azione webhook richiede che l'endpoint del webhook di destinazione non richieda i dettagli dell'avviso per funzionare correttamente o che sia in grado di analizzare le informazioni sul contesto dell'avviso fornite come parte dell'operazione POST. Se l'endpoint del webhook non è in grado di gestire le informazioni sul contesto dell'avviso in modo autonomo, è possibile usare una soluzione come un' [azione dell'app](./action-groups-logic-app.md) per la logica per una manipolazione personalizzata delle informazioni sul contesto degli avvisi in modo che corrispondano al formato dati previsto del webhook.
> L'utente deve essere il **proprietario** dell'entità servizio webhook per assicurarsi che la sicurezza non venga violata. Poiché un cliente di Azure può accedere a tutti gli ID oggetto tramite il portale, senza controllare il proprietario, chiunque può aggiungere il webhook sicuro al proprio gruppo di azioni per la notifica di avviso di monitoraggio di Azure che viola la sicurezza.

L'azione Webhook dei gruppi di azione consente di sfruttare Azure Active Directory per proteggere la connessione tra il gruppo di azioni e l'API Web protetta (endpoint webhook). Di seguito è descritto il flusso di lavoro generale per sfruttare questa funzionalità. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

1. Creare un'applicazione Azure AD per l'API Web protetta. Vedere [API Web protetta: registrazione dell'app](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configurare l'API protetta affinché venga [chiamata da un'app daemon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Abilitare i gruppi di azioni per l'uso dell'applicazione Azure AD.

    > [!NOTE]
    > Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](../../active-directory/roles/permissions-reference.md#available-roles).
    
    - Modificare la chiamata a Connect-AzureAD dello script di PowerShell per usare l'ID tenant di Azure AD.
    - Modificare la variabile dello script di PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione Azure AD.
    - Eseguire lo script modificato.
    
3. Configurare l'azione del Webhook protetto del Gruppo di azioni.
    - Copiare il valore $myApp.ObjectId dallo script e immetterlo nel campo ID oggetto applicazione nella definizione dell'azione Webhook.
    
    ![Usare l'azione Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script di PowerShell per il Webhook protetto

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md) e sul [comportamento degli avvisi SMS](./alerts-sms-behavior.md). 

Un Gruppo di azioni può contenere un numero limitato di azioni SMS.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, il servizio SMS non è supportato per il paese/regione.  Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di SMS di terze parti con supporto per il paese/regione.  

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

**Elenco dei paesi in cui è supportata la notifica SMS**

| Prefisso internazionale | Nome paese |
|:---|:---|
| 61 | Australia |
| 43 | Austria |
| 32 | Belgio |
| 55 | Brasile |
| 1 |Canada |
| 56 | Cile |
| 86 | Cina |
| 420 | Repubblica Ceca |
| 45 | Danimarca |
| 372 | Estonia |
| 358 | Finlandia |
| 33 | Francia |
| 49 | Germania |
| 852 | RAS di Hong Kong |
| 91 | India |
| 353 | Irlanda |
| 972 | Israele |
| 39 | Italia |
| 81 | Giappone |
| 352 | Lussemburgo |
| 60 | Malaysia |
| 52 | Messico |
| 31 | Paesi Bassi |
| 64 | Nuova Zelanda |
| 47 | Norvegia |
| 351 | Portogallo |
| 1 | Portorico |
| 40 | Romania |
| 65 | Singapore |
| 27 | Sudafrica |
| 82 | Corea del Sud |
| 34 | Spagna |
| 41 | Svizzera |
| 886 | Taiwan |
| 44 | Regno Unito |
| 1 | Stati Uniti |

### <a name="voice"></a>Chiamata vocale
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

Un Gruppo di azioni può contenere un numero limitato di azioni Voce.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, le chiamate vocali non sono supportate per il paese/regione. Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di chiamate vocali di terze parti con supporto per il paese/regione.  
> Solo il codice paese attualmente supportato in portale di Azure gruppo di azione per la notifica vocale è + 1 (Stati Uniti). 

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook

> [!NOTE]
> L'uso dell'azione webhook richiede che l'endpoint del webhook di destinazione non richieda i dettagli dell'avviso per funzionare correttamente o che sia in grado di analizzare le informazioni sul contesto dell'avviso fornite come parte dell'operazione POST. Se l'endpoint del webhook non è in grado di gestire le informazioni sul contesto dell'avviso in modo autonomo, è possibile usare una soluzione come un' [azione dell'app](./action-groups-logic-app.md) per la logica per una manipolazione personalizzata delle informazioni sul contesto degli avvisi in modo che corrispondano al formato dati previsto del webhook.

I webhook vengono elaborati usando le regole seguenti
- Una chiamata al webhook viene tentata un massimo di 3 volte.
- La chiamata verrà ritentata se non viene ricevuta una risposta entro il periodo di timeout o viene restituito uno dei codici di stato HTTP seguenti: 408, 429, 503 o 504.
- La prima chiamata attenderà 10 secondi per una risposta.
- Il secondo e il terzo tentativo attendono 30 secondi per una risposta.
- Dopo che i 3 tentativi di chiamare il webhook non sono riusciti, nessun gruppo di azione chiamerà l'endpoint per 15 minuti.

Vedere [gli indirizzi IP del gruppo di azioni per gli](../app/ip-addresses.md) intervalli di indirizzi IP di origine.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](./alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](./activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](../platform/itsmc-overview.md).
* Altre informazioni sulla [limitazione della frequenza](./alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](../platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md).
