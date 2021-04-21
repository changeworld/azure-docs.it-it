---
title: Risolvere i problemi relativi al controllo degli accessi in base al ruolo di
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo di Azure.
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771714"
---
# <a name="troubleshoot-azure-rbac"></a>Risolvere i problemi relativi al controllo degli accessi in base al ruolo di

Questo articolo risponde ad alcune domande comuni sul controllo degli accessi in base al ruolo di Azure, in modo da sapere cosa aspettarsi quando si usano i ruoli e risolvere i problemi di accesso.

## <a name="azure-role-assignments-limit"></a>Limite delle assegnazioni di ruolo di Azure

Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione. Questo limite include le assegnazioni di ruolo negli ambiti di sottoscrizione, gruppo di risorse e risorsa. Se viene visualizzato il messaggio di errore "Non è possibile creare altre assegnazioni di ruolo (codice: RoleAssignmentLimitExceeded)" quando si tenta di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo nella sottoscrizione.

> [!NOTE]
> Il limite di assegnazioni di ruolo **2000** per sottoscrizione è fisso e non può essere aumentato.

Se si sta per raggiungere questo limite, ecco alcuni modi per ridurre il numero di assegnazioni di ruolo:

- Aggiungere utenti ai gruppi e assegnare ruoli ai gruppi. 
- Combinare più ruoli predefiniti con un ruolo personalizzato. 
- Eseguire assegnazioni di ruolo comuni in un ambito superiore, ad esempio una sottoscrizione o un gruppo di gestione.
- Se si dispone di Azure AD Premium P2, rendere idonee le assegnazioni di ruolo [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) invece di assegnarle in modo permanente. 
- Aggiungere un'ulteriore sottoscrizione. 

Per ottenere il numero di assegnazioni di ruolo, è possibile visualizzare il grafico nella pagina Controllo di accesso [(IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) nel portale di Azure. È anche possibile usare i comandi Azure PowerShell seguenti:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemi relativi alle assegnazioni di ruolo di Azure

- Se non è possibile assegnare un ruolo nel portale di Azure nel controllo di accesso **(IAM)** perché l'opzione Aggiungi assegnazione di ruolo è disabilitata o perché viene visualizzato l'errore di autorizzazioni "Il client con ID oggetto non ha l'autorizzazione per eseguire l'azione", verificare di aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione proprietario o amministratore di accesso utente nell'ambito che si sta tentando di assegnare al  >   `Microsoft.Authorization/roleAssignments/write` ruolo. [](built-in-roles.md#owner) [](built-in-roles.md#user-access-administrator)
- Se si usa un'entità servizio per assegnare ruoli, è possibile che venga visualizzato l'errore "Privilegi insufficienti per completare l'operazione". Si supponga, ad esempio, di avere un'entità servizio a cui è stato assegnato il ruolo Proprietario e di provare a creare l'assegnazione di ruolo seguente come entità servizio tramite l'interfaccia della riga di comando di Azure:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Se viene visualizzato l'errore "Privilegi insufficienti per completare l'operazione", è probabile che l'interfaccia della riga di comando di Azure tenti di cercare l'identità dell'assegnatare in Azure AD e che l'entità servizio non possa leggere Azure AD per impostazione predefinita.

    Esistono due modi per risolvere potenzialmente questo errore. Il primo modo è assegnare il ruolo [con](../active-directory/roles/permissions-reference.md#directory-readers) autorizzazioni di lettura nella directory all'entità servizio in modo che possa leggere i dati nella directory.

    Il secondo modo per risolvere questo errore è creare l'assegnazione di ruolo usando il `--assignee-object-id` parametro anziché `--assignee` . Usando , `--assignee-object-id` l'interfaccia della riga di comando di Azure ignora Azure AD ricerca. Sarà necessario ottenere l'ID oggetto dell'utente, del gruppo o dell'applicazione a cui si vuole assegnare il ruolo. Per altre informazioni, vedere Assegnare ruoli di [Azure usando l'interfaccia della riga di comando di Azure.](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Se si crea una nuova entità servizio e si prova ad assegnare subito un ruolo a tale entità, in alcuni casi l'assegnazione di ruolo può avere esito negativo.

    Per far fronte a questo scenario, è necessario impostare la proprietà `principalType` su `ServicePrincipal` quando si crea l'assegnazione di ruolo. È anche necessario impostare `apiVersion` l'oggetto dell'assegnazione di ruolo su `2018-09-01-preview` o versione successiva. Per altre informazioni, vedere [Assegnare](role-assignments-rest.md#new-service-principal) ruoli di Azure a una nuova entità servizio usando l'API REST o [Assegnare](role-assignments-template.md#new-service-principal) ruoli di Azure a una nuova entità servizio usando i modelli Azure Resource Manager servizio

- Se si tenta di rimuovere l'ultima assegnazione di ruolo Proprietario per una sottoscrizione, è possibile che venga visualizzato l'errore "Impossibile eliminare l'ultima assegnazione di amministratore controllo degli accessi in base al ruolo". La rimozione dell'ultima assegnazione di ruolo Proprietario per una sottoscrizione non è supportata per evitare l'orfano della sottoscrizione. Se si vuole annullare la sottoscrizione, vedere Annullare [la sottoscrizione di Azure.](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Se sono necessari passaggi per la creazione di un ruolo personalizzato, vedere le esercitazioni sui ruoli personalizzati [usando](custom-roles-portal.md)portale di Azure , [Azure PowerShell](tutorial-custom-role-powershell.md)o l'interfaccia della riga di [comando di Azure.](tutorial-custom-role-cli.md)
- Se non è possibile aggiornare un ruolo personalizzato esistente, verificare di aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione , ad esempio Proprietario o Amministratore `Microsoft.Authorization/roleDefinition/write` [Accesso utenti](built-in-roles.md#user-access-administrator). [](built-in-roles.md#owner)
- Se non è possibile eliminare un ruolo personalizzato e ricevere il messaggio di errore "Sono presenti assegnazioni di ruolo esistenti che fanno riferimento a ruolo (codice: RoleDefinitionHasAssignments)", esistono assegnazioni di ruolo che usano ancora il ruolo personalizzato. Rimuovere le assegnazioni di ruolo e provare di nuovo a eliminare il ruolo personalizzato.
- Se viene visualizzato il messaggio di errore "Limite di definizioni del ruolo superato. Non è possibile creare altre definizioni di ruolo (codice: RoleDefinitionLimitExceeded)" quando si tenta di creare un nuovo ruolo personalizzato, eliminare tutti i ruoli personalizzati che non vengono usati. Azure supporta fino a **5000 ruoli** personalizzati in una directory. Per Azure Germania e Azure China (21Vianet), il limite è di 2000 ruoli personalizzati.
- Se viene visualizzato un errore simile a "Il client ha l'autorizzazione per eseguire l'azione 'Microsoft.Authorization/roleDefinitions/write' nell'ambito '/subscriptions/{subscriptionid}', [](role-definitions.md#assignablescopes) ma la sottoscrizione collegata non è stata trovata" quando si tenta di aggiornare un ruolo personalizzato, verificare se uno o più ambiti assegnabili sono stati eliminati nella directory. Se l'ambito è stato eliminato, creare un ticket di supporto perché attualmente non è disponibile alcuna soluzione self-service.

## <a name="custom-roles-and-management-groups"></a>Ruoli personalizzati e gruppi di gestione

- È possibile definire un solo gruppo di gestione in `AssignableScopes` un ruolo personalizzato. L'aggiunta di un gruppo di gestione a `AssignableScopes` è attualmente in fase di anteprima.
- I ruoli personalizzati con `DataActions` non possono essere assegnati nell'ambito del gruppo di gestione.
- Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione del ruolo.
- Per altre informazioni sui ruoli personalizzati e sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Trasferimento di una sottoscrizione in una directory diversa

- Se sono necessari passaggi per trasferire una sottoscrizione in una directory Azure AD diversa, vedere Trasferire una sottoscrizione di Azure in una directory Azure AD [diversa.](transfer-subscription.md)
- Se si trasferisce una sottoscrizione in una directory Azure AD  diversa, tutte le assegnazioni di ruolo vengono eliminate in modo permanente dalla directory Azure AD di origine e non ne viene eseguita la migrazione alla directory Azure AD di destinazione. È necessario creare nuovamente le assegnazioni di ruolo nella directory di destinazione. È anche necessario ricreare manualmente le identità gestite per le risorse di Azure. Per altre informazioni, vedere [Domande frequenti e problemi noti relativi alle identità gestite.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Se si è un amministratore globale di Azure AD e non si ha accesso a una sottoscrizione dopo che è [](elevate-access-global-admin.md) stata trasferita tra le directory, usare l'interruttore Gestione accesso per le risorse di **Azure** per elevare temporaneamente l'accesso per ottenere l'accesso alla sottoscrizione.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemi con gli amministratori del servizio o i coamministratori

- Se si verificano problemi con l'amministratore del servizio o con i co-amministratori, vedere Aggiungere o modificare gli amministratori della sottoscrizione di [Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e i ruoli di amministratore della sottoscrizione classica, i ruoli di Azure e i ruoli Azure AD [sottoscrizione.](rbac-and-directory-admin-roles.md)

## <a name="access-denied-or-permission-errors"></a>Errori di accesso negato o di autorizzazione

- Se si riceve l'errore relativo alle autorizzazioni "Il client con ID oggetto non è autorizzato a eseguire l'azione sull'ambito (codice: AuthorizationFailed)" quando si cerca di creare una risorsa, verificare di aver effettuato l'accesso con un utente a cui è assegnato un ruolo con autorizzazione di scrittura alla risorsa nell'ambito selezionato. Ad esempio, per gestire le macchine virtuali in un gruppo di risorse, è necessario disporre del ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) nel gruppo di risorse (o nell'ambito padre). Per un elenco delle autorizzazioni per ogni ruolo predefinito, vedere [Ruoli predefiniti di Azure.](built-in-roles.md)
- Se viene visualizzato l'errore delle autorizzazioni "Non si è autorizzati a creare una richiesta di supporto" quando si tenta di creare o aggiornare un ticket di supporto, verificare di aver eseguito l'accesso con un utente a cui è assegnato un ruolo con l'autorizzazione , ad esempio Collaboratore richiesta di `Microsoft.Support/supportTickets/write` [supporto](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Spostare le risorse con assegnazioni di ruolo

Se si sposta una risorsa a cui è assegnato un ruolo di Azure direttamente alla risorsa (o a una risorsa figlio), l'assegnazione di ruolo non viene spostata e diventa orfana. Dopo lo spostamento, è necessario creare nuovamente l'assegnazione di ruolo. Alla fine, l'assegnazione di ruolo orfana verrà rimossa automaticamente, ma è consigliabile rimuovere l'assegnazione di ruolo prima di spostare la risorsa.

Per informazioni su come spostare le risorse, vedere [Spostare le risorse in un nuovo gruppo di risorse o in una nuova sottoscrizione.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="role-assignments-with-identity-not-found"></a>Assegnazioni di ruolo con identità non trovate

Nell'elenco delle assegnazioni di ruolo per il portale di Azure è possibile notare che l'entità di sicurezza (utente, gruppo,  entità servizio o identità gestita) è elencata come Identità non trovata con un tipo Sconosciuto. 

![Identità non trovata elencata nelle assegnazioni di ruolo di Azure](./media/troubleshooting/unknown-security-principal.png)

L'identità potrebbe non essere stata trovata per due motivi:

- Di recente è stato invitato un utente durante la creazione di un'assegnazione di ruolo
- È stata eliminata un'entità di sicurezza con un'assegnazione di ruolo

Se di recente è stato invitato un utente durante la creazione di un'assegnazione di ruolo, questa entità di sicurezza potrebbe essere ancora in corso nel processo di replica tra aree diverse. In tal caso, attendere alcuni istanti e aggiornare l'elenco delle assegnazioni di ruolo.

Tuttavia, se questa entità di sicurezza non è un utente invitato di recente, potrebbe essere un'entità di sicurezza eliminata. Se si assegna un ruolo a un'entità di sicurezza e successivamente si elimina tale entità  di sicurezza senza prima rimuovere l'assegnazione di ruolo, l'entità di sicurezza verrà elencata come Identità non trovata e di tipo **Sconosciuto.**

Se si elenca questa assegnazione di ruolo usando Azure PowerShell, è possibile che venga visualizzato un oggetto vuoto e `DisplayName` un oggetto impostato su `ObjectType` **Sconosciuto.** Ad esempio, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) restituisce un'assegnazione di ruolo simile all'output seguente:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Analogamente, se si elenca questa assegnazione di ruolo usando l'interfaccia della riga di comando di Azure, potrebbe essere visualizzato un oggetto `principalName` vuoto. Ad esempio, [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) restituisce un'assegnazione di ruolo simile all'output seguente:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Non è un problema lasciare queste assegnazioni di ruolo in cui l'entità di sicurezza è stata eliminata. Se si desidera, è possibile rimuovere queste assegnazioni di ruolo usando passaggi simili ad altre assegnazioni di ruolo. Per informazioni su come rimuovere le assegnazioni di ruolo, vedere [Rimuovere le assegnazioni di ruolo di Azure.](role-assignments-remove.md)

In PowerShell, se si tenta di rimuovere le assegnazioni di ruolo usando l'ID oggetto e il nome della definizione del ruolo e più di un'assegnazione di ruolo corrisponde ai parametri, verrà visualizzato il messaggio di errore: "Le informazioni fornite non sono mappate a un'assegnazione di ruolo". L'output seguente mostra un esempio del messaggio di errore:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se viene visualizzato questo messaggio di errore, assicurarsi di specificare anche i `-Scope` parametri `-ResourceGroupName` o .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Le modifiche alle assegnazioni di ruolo non vengono rilevate

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Quando si assegnano ruoli o si rimuovono le assegnazioni di ruolo, l'applicazione delle modifiche può richiedere fino a 30 minuti. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

Se si aggiunge o si rimuove un'assegnazione di ruolo nell'ambito del gruppo di gestione e il ruolo ha , l'accesso al piano dati potrebbe non essere `DataActions` aggiornato per diverse ore. Si applica solo all'ambito del gruppo di gestione e al piano dati.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Le funzionalità di gestione seguenti richiedono **l'accesso** in scrittura a un'app Web (Collaboratore o Proprietario) e non sono disponibili in nessuno scenario di sola lettura.

* Comandi (quali avvio, interruzione e così via)
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log delle risorse
* Console (prompt dei comandi)
* Distribuzioni attive e recenti, per la distribuzione continua del Git locale
* Spesa prevista
* Test Web
* Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per l'app Web.

## <a name="web-app-resources-that-require-write-access"></a>Risorse dell'app Web che richiedono l'accesso in scrittura

La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/troubleshooting/website-resource-model.png)

Quindi, se si concede l'accesso solo all'app Web, la maggior parte delle funzionalità del pannello del sito Web nel portale di Azure viene disabilitata.

Questi elementi richiedono accesso **in scrittura** al **piano di servizio App** che corrisponde al sito Web:  

* Visualizzazione del piano tariffario dell'app Web, che può essere Gratuito o Standard  
* Configurazione di scalabilità, ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica  
* Quote, ad esempio archiviazione, larghezza di banda e CPU  

Gli elementi seguenti richiedono accesso **in scrittura** all'intero **gruppo di risorse** che contiene il sito Web:  

* Certificati e associazioni TLS/SSL (i certificati TLS/SSL possono essere condivisi tra siti nello stesso gruppo di risorse e nella stessa posizione geografica)  
* Regole di avviso  
* Impostazioni di scalabilità automatica  
* Componenti di Application Insights  
* Test Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funzionalità delle macchine virtuali che richiedono l'accesso in scrittura

Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le macchine virtuali sono correlate a nomi di dominio, reti virtuali, account di archiviazione e regole di avviso.

Gli elementi seguenti richiedono accesso **in scrittura** alla **macchina virtuale**:

* Endpoint  
* Indirizzi IP  
* Dischi  
* Estensioni  

Gli elementi seguenti richiedono accesso **in scrittura** sia alla **macchina virtuale** che al **gruppo di risorse**, così come al nome di dominio a cui appartiene:  

* Set di disponibilità  
* Set con carico bilanciato  
* Regole di avviso  

Se non è possibile accedere a nessuno di questi riquadri, richiedere all'amministratore l'accesso come Collaboratore al gruppo di risorse.

## <a name="azure-functions-and-write-access"></a>Funzioni di Azure e accesso in scrittura

Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio, se a un utente viene assegnato il [ruolo Lettore,](built-in-roles.md#reader) non sarà possibile visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)**.

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione. Per accedere a queste funzionalità, è necessario il [ruolo Collaboratore.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi per gli utenti guest](role-assignments-external-users.md#troubleshoot)
- [Assegnare ruoli di Azure usando il portale di Azure](role-assignments-portal.md)
- [Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo di Azure](change-history-report.md)
