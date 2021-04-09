---
title: Usare la libreria .NET per la gestione di Batch per gestire le risorse di account
description: Creare, eliminare e modificare le risorse dell'account Batch tramite la libreria Batch Management .NET.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896732"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gestire le quote e gli account Batch con la libreria client di gestione Batch per .NET

È possibile ridurre il sovraccarico di manutenzione nelle applicazioni Azure Batch usando la libreria [.NET per la gestione di Batch](/dotnet/api/overview/azure/batch) per automatizzare le operazioni di creazione ed eliminazione di account Batch, gestione delle chiavi e individuazione delle quote.

- **Creare ed eliminare account Batch** in qualsiasi area. Se, ad esempio, un fornitore di software indipendente (ISV) offre un servizio per cui a ogni cliente viene assegnato un account Batch separato per la fatturazione, è possibile aggiungere funzionalità di creazione ed eliminazione di account al portale per i clienti.
- **Recuperare e rigenerare chiavi di account** a livello di programmazione per qualsiasi account Batch. Questa operazione consente di conformarsi ai criteri di sicurezza che impongono rollover periodici o la scadenza delle chiavi dell'account. In presenza di numerosi account Batch in diverse aree di Azure, l'automazione del processo di rollover permette di aumentare l'efficienza della soluzione.
- **Controllare le quote degli account** e determinare senza errori i limiti dei singoli account Batch. Il controllo delle quote degli account prima di avviare processi, creare pool o aggiungere nodi di calcolo permette di stabilire attivamente dove e quando creare risorse di calcolo. È possibile determinare quali account richiedono un aumento della quota prima dell'allocazione di risorse aggiuntive in tali account.
- **Combina le funzionalità di altri servizi di Azure** per un'esperienza di gestione completa usando batch Management .net, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)e il [Azure Resource Manager](../azure-resource-manager/management/overview.md) insieme nella stessa applicazione. Usando queste funzionalità e le relative API è possibile eliminare i problemi di autenticazione, consentire la creazione e l'eliminazione di gruppi di risorse e fornire le funzionalità descritte in precedenza per una soluzione di gestione end-to-end.

> [!NOTE]
> Sebbene questo articolo sia incentrato sulla gestione a livello di codice degli account, delle chiavi e delle quote di batch, è anche possibile eseguire molte di queste attività [usando il portale di Azure](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Creare ed eliminare account Batch

Una delle principali funzionalità dell'API di gestione di batch consiste nel creare ed eliminare [account batch](accounts.md) in un'area di Azure. A questo scopo, usare [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) e [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync) o le relative controparti sincrone.

Il frammento di codice seguente crea un account, ottiene l'account appena creato dal servizio Batch e quindi lo elimina. In questo e in altri frammenti di codice in questo articolo `batchManagementClient` è un'istanza completamente inizializzata di [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Le applicazioni che usano la libreria Batch Management .NET e la relativa classe BatchManagementClient necessitano di accesso come amministratore del servizio o coamministratore alla sottoscrizione a cui appartiene l'account Batch da gestire. Per altre informazioni, consultare la sezione Azure Active Directory e l'esempio di codice [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement).

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperare e rigenerare chiavi di account

Ottenere chiavi di account primarie e secondarie da qualsiasi account batch all'interno della sottoscrizione usando [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Le chiavi possono essere rigenerate con [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> È possibile creare un flusso di lavoro di connessione ottimizzato per le applicazioni di gestione. Per prima cosa, ottenere una chiave account per l'account batch che si vuole gestire con [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Usare quindi questa chiave durante l'inizializzazione della classe [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) della libreria .NET di Batch usata durante l'inizializzazione di [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificare le quote di sottoscrizioni di Azure e account Batch

Le sottoscrizioni di Azure e i singoli servizi di Azure come Batch hanno tutti quote predefinite che limitano il numero di determinate entità al loro interno. Per informazioni sulle quote predefinite per le sottoscrizioni di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md). Per le quote predefinite del servizio Batch, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md). Tramite la libreria Batch Management .NET è possibile controllare queste quote nelle applicazioni. In questo modo è possibile prendere decisioni relative all'allocazione prima di aggiungere account o risorse di calcolo come pool e nodi di calcolo.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificare le quote dell'account Batch di una sottoscrizione di Azure

Prima di creare un account Batch in un'area, è possibile verificare se la sottoscrizione di Azure permette di aggiungere un account in quell'area.

Nel frammento di codice riportato di seguito viene usato prima di tutto **ListAsync** per ottenere una raccolta di tutti gli account batch all'interno di una sottoscrizione. Una volta ottenuta questa raccolta, è possibile determinare il numero di account nell'area di destinazione. Si usa quindi **GetQuotasAsync** per ottenere la quota dell'account batch e determinare il numero di account che è possibile creare in tale area.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Nel frammento di codice precedente, `creds` è un'istanza di **TokenCredentials**. Per un esempio relativo alla creazione di questo oggetto, vedere l'esempio di codice [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) in GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificare le quote di risorse di calcolo in un account Batch

Prima di aumentare le risorse di calcolo nella soluzione Batch, è possibile verificare che le risorse da allocare non superino le quote dell'account. Nel frammento di codice riportato di seguito si procede alla stampa delle informazioni sulle quote per l'account Batch denominato `mybatchaccount`. Nell'applicazione è possibile usare queste informazioni per stabilire se l'account può gestire le risorse aggiuntive da creare.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Sebbene esistano quote predefinite per le sottoscrizioni e i servizi di Azure, molti di questi limiti possono essere generati [richiedendo un aumento della quota nel portale di Azure](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Usare Azure AD con la gestione .NET per Batch

La libreria .NET per la gestione di Batch è un client del provider di risorse di Azure e viene usata in combinazione con [Azure Resource Manager](../azure-resource-manager/management/overview.md) per gestire le risorse dell'account a livello di programmazione. Azure AD deve autenticare le richieste effettuate tramite un client del provider di risorse di Azure, inclusa la libreria .NET per la gestione di Batch, e tramite Azure Resource Manager. Per informazioni sull'uso di Azure AD con la libreria della gestione .NET per Batch, vedere [Usare Azure Active Directory per autenticare le soluzioni Batch](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Progetto di esempio su GitHub

Il progetto di esempio [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) in GitHub permette di vedere .NET per la gestione di Batch in azione. L'applicazione di esempio AccountManagement illustra le operazioni seguenti:

1. Acquisire un token di sicurezza da Azure AD con [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Se l'utente non ha già eseguito l'accesso, gli viene richiesto di fornire le credenziali di Azure.
2. Usando il token di sicurezza ottenuto da Azure AD, creare un oggetto [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) per richiedere ad Azure un elenco di sottoscrizioni associate all'account. L'utente può selezionare una sottoscrizione dall'elenco se contiene più di una sottoscrizione.
3. Ottenere le credenziali associate alla sottoscrizione selezionata.
4. Creare un oggetto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) usando le credenziali.
5. Usare l'oggetto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) per creare un gruppo di risorse.
6. Usare l'oggetto [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) per eseguire una serie di operazioni sull'account Batch:
   - Creare un account Batch nel nuovo gruppo di risorse.
   - Ottenere l'account appena creato dal servizio Batch.
   - Stampare le chiavi dell'account per il nuovo account.
   - Rigenerare una nuova chiave primaria per l'account.
   - Stampare le informazioni sulla quota per l'account.
   - Stampare le informazioni sulla quota per la sottoscrizione.
   - Stampare tutti gli account all'interno della sottoscrizione.
   - Eliminare l'account appena creato.
7. Eliminare il gruppo di risorse.

Per eseguire correttamente l'applicazione di esempio, è innanzitutto necessario registrarla con il tenant di Azure AD nel portale di Azure e concedere le autorizzazioni all'API di Azure Resource Manager. Seguire la procedura indicata in [Autenticare le soluzioni di gestione Batch con Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive consentono di eseguire un'applicazione di esempio che usa il servizio batch per eseguire un carico di lavoro in più nodi di calcolo, usando archiviazione di Azure per la gestione temporanea e il recupero dei file del carico di lavoro. git pus
