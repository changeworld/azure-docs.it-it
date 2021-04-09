---
title: Informazioni su come proteggere l'accesso ai dati in Azure Cosmos DB
description: Informazioni sui concetti relativi al controllo di accesso in Azure Cosmos DB, incluse chiavi primarie, chiavi di sola lettura, utenti e autorizzazioni.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/11/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a16ecd2ee6ed939b2afd0e51e9cf531e419c8af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656398"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Proteggere l'accesso ai dati in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Questo articolo fornisce una panoramica del controllo di accesso ai dati in Azure Cosmos DB.

Azure Cosmos DB offre tre modi per controllare l'accesso ai dati.

| Tipo di controllo di accesso | Caratteristiche |
|---|---|
| [Chiavi primarie](#primary-keys) | Segreto condiviso che consente qualsiasi operazione di gestione o di dati. Si tratta di varianti di lettura/scrittura e di sola lettura. |
| [Controllo degli accessi in base al ruolo](#rbac) (anteprima) | Modello di autorizzazione con granularità fine e basato sui ruoli che usa le identità Azure Active Directory (AAD) per l'autenticazione. |
| [Token delle risorse](#resource-tokens)| Modello di autorizzazione con granularità fine in base a autorizzazioni e utenti Azure Cosmos DB nativi. |

## <a name="primary-keys"></a><a id="primary-keys"></a> Chiavi primarie

Le chiavi primarie forniscono l'accesso a tutte le risorse amministrative per l'account del database. Ogni account è costituito da due chiavi primarie: una chiave primaria e una chiave secondaria. Lo scopo di due chiavi è consentire di rigenerare o eseguire il rollup delle chiavi, garantendo l'accesso continuo all'account e ai dati. Per altre informazioni sulle chiavi primarie, vedere l'articolo sulla [sicurezza del database](database-security.md#primary-keys) .

### <a name="key-rotation"></a><a id="key-rotation"></a> Rotazione delle chiavi

Il processo di rotazione della chiave primaria è semplice. 

1. Passare alla portale di Azure per recuperare la chiave secondaria.
2. Sostituire la chiave primaria con la chiave secondaria nell'applicazione. Assicurarsi che tutti i client Cosmos DB in tutte le distribuzioni vengano riavviati immediatamente e inizino a usare la chiave aggiornata.
3. Ruotare la chiave primaria nel portale di Azure.
4. Verificare che la nuova chiave primaria funzioni su tutte le risorse. Il processo di rotazione delle chiavi può richiedere da meno di un minuto all'ora, a seconda delle dimensioni dell'account Cosmos DB.
5. Sostituire la chiave secondaria con la nuova chiave primaria.

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Rotazione delle chiavi primarie nell'portale di Azure-dimostrazione della sicurezza del database NoSQL" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>Esempio di codice per l'uso di una chiave primaria

Nell'esempio di codice seguente viene illustrato come usare un endpoint dell'account Cosmos DB e una chiave primaria per creare un'istanza di un DocumentClient e creare un database:

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

Nell'esempio di codice seguente viene illustrato come utilizzare l'endpoint dell'account Azure Cosmos DB e la chiave primaria per creare un'istanza di un `CosmosClient` oggetto:

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="role-based-access-control-preview"></a><a id="rbac"></a> Controllo degli accessi in base al ruolo (anteprima)

Azure Cosmos DB espone un sistema di controllo degli accessi in base al ruolo (RBAC) incorporato che consente di:

- Autenticare le richieste di dati con un'identità Azure Active Directory (AAD).
- Autorizzare le richieste di dati con un modello di autorizzazione con granularità fine e basato sui ruoli.

Azure Cosmos DB RBAC è il metodo di controllo degli accessi ideale nelle situazioni in cui:

- Non si vuole usare un segreto condiviso come la chiave primaria e si preferisce affidarsi a un meccanismo di autenticazione basato su token,
- Si vuole usare Azure AD identità per autenticare le richieste,
- È necessario un modello di autorizzazione con granularità fine per limitare in modo restrittivo le operazioni del database che le identità sono autorizzate a eseguire,
- Si desidera materializzare i criteri di controllo degli accessi come "ruoli" che è possibile assegnare a più identità.

Per ulteriori informazioni sui Azure Cosmos DB RBAC, vedere [configurare il controllo degli accessi in base al ruolo per l'account Azure Cosmos DB](how-to-setup-rbac.md) .

## <a name="resource-tokens"></a><a id="resource-tokens"></a> Token delle risorse

I token delle risorse consentono di accedere alle risorse dell'applicazione all'interno di un database. Token delle risorse:

- Consentono di accedere a contenitori, chiavi di partizioni, documenti, allegati, stored procedure, trigger e funzioni definite dall'utente.
- Vengono creati quando a un [utente](#users) vengono concesse [autorizzazioni](#permissions) per una risorsa specifica.
- Vengono ricreati quando si interviene su una risorsa di autorizzazione tramite chiamata POST, GET o PUT.
- Usano un token di risorsa hash costruito specificamente per l'utente, la risorsa e l'autorizzazione.
- Hanno un limite di tempo con un periodo di validità personalizzabile. L'intervallo di tempo valido predefinito è un'ora. La durata del token può essere tuttavia specificata in modo esplicito, fino a un massimo di cinque ore.
- Fornire un'alternativa sicura alla consegna della chiave primaria.
- Consentono ai client di leggere, scrivere ed eliminare risorse nell'account Cosmos DB in base alle autorizzazioni concesse.

È possibile usare un token di risorsa (creando Cosmos DB utenti e autorizzazioni) quando si vuole fornire l'accesso alle risorse nell'account Cosmos DB a un client che non può essere considerato attendibile con la chiave primaria.  

Cosmos DB token di risorsa rappresentano un'alternativa sicura che consente ai client di leggere, scrivere ed eliminare risorse nell'account Cosmos DB in base alle autorizzazioni concesse e senza bisogno di una chiave primaria o di sola lettura.

Di seguito è riportato un tipico schema progettuale in cui i token delle risorse possono essere richiesti, generati e forniti ai client:

1. Un servizio di livello intermedio viene configurato per gestire un'applicazione per dispositivi mobili per condividere le foto dell'utente.
2. Il servizio di livello intermedio dispone della chiave primaria dell'account Cosmos DB.
3. L'app per le foto viene installata nei dispositivi mobili dell'utente finale.
4. All'accesso, l'app per le foto stabilisce l'identità dell'utente con il servizio di livello intermedio. Questo meccanismo per stabilire l'identità dipende completamente dall'applicazione.
5. Una volta stabilita l'identità, il servizio di livello intermedio richiede le autorizzazioni in base all'identità.
6. Il servizio di livello intermedio invia di nuovo un token delle risorse all'app per il telefono.
7. L'app per il telefono può continuare a usare il token delle risorse per accedere direttamente alle risorse di Cosmos DB con le autorizzazioni definite dal token delle risorse e per l'intervallo consentito dal token delle risorse.
8. Quando il token delle risorse scade, le richieste successive ricevono un'eccezione 401 Non autorizzato.  A questo punto, l'app per il telefono ristabilisce l'identità e richiede un nuovo token delle risorse.

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Flusso di lavoro dei token delle risorse di Azure Cosmos DB" border="false":::

La generazione e la gestione dei token delle risorse vengono gestite dalle librerie client Cosmos DB Native; Tuttavia, se si usa REST è necessario creare le intestazioni di richiesta/autenticazione. Per altre informazioni sulla creazione di intestazioni di autenticazione per REST, vedere [controllo di accesso sulle risorse di Cosmos DB](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) o il codice sorgente per [.net SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) o [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts).

Per un esempio di servizio di livello intermedio usato per generare o negoziare i token delle risorse, vedere l'app [ResourceTokenBroker](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

### <a name="users"></a>Utenti<a id="users"></a>

Azure Cosmos DB utenti sono associati a un database Cosmos.  Ogni database può contenere zero o più utenti di Cosmos DB. Nell'esempio di codice seguente viene illustrato come creare un Cosmos DB utente utilizzando [Azure Cosmos DB .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Ogni utente Cosmos DB dispone di un metodo ReadAsync () che può essere utilizzato per recuperare l'elenco di [autorizzazioni](#permissions) associate all'utente.

### <a name="permissions"></a>Autorizzazioni<a id="permissions"></a>

Una risorsa di autorizzazione è associata a un utente e assegnata al contenitore, oltre al livello di chiave di partizione. Ogni utente può contenere zero o più autorizzazioni. Una risorsa di autorizzazione consente di accedere a un token di sicurezza che l'utente deve eseguire quando tenta di accedere a un contenitore o a dati specifici in una chiave di partizione specifica. Sono disponibili due livelli di accesso che possono essere forniti da una risorsa di autorizzazione:

- Tutto: l'utente ha l'autorizzazione completa per la risorsa.
- Lettura: l'utente può solo leggere i contenuti della risorsa, ma non può eseguire operazioni di scrittura, aggiornamento o eliminazione sulla risorsa.

> [!NOTE]
> Per eseguire le stored procedure, l'utente deve disporre dell'autorizzazione all per il contenitore in cui verrà eseguita la stored procedure.

Se si abilitano i [log di diagnostica sulle richieste del piano dati](cosmosdb-monitor-resource-logs.md), vengono registrate le due proprietà seguenti corrispondenti all'autorizzazione:

* **resourceTokenPermissionId** : questa proprietà indica l'ID di autorizzazione del token di risorsa specificato. 

* **resourceTokenPermissionMode** : questa proprietà indica la modalità di autorizzazione impostata durante la creazione del token della risorsa. La modalità di autorizzazione può avere valori quali "All" o "Read".

#### <a name="code-sample-to-create-permission"></a>Esempio di codice per la creazione dell'autorizzazione

L'esempio di codice seguente illustra come creare una risorsa di autorizzazione, leggere il token delle risorse della risorsa di autorizzazione e associare le autorizzazioni all'[utente](#users) creato in precedenza.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

#### <a name="code-sample-to-read-permission-for-user"></a>Esempio di codice per l'autorizzazione di lettura per l'utente

Il frammento di codice seguente illustra come recuperare l'autorizzazione associata all'utente creato in precedenza e creare un'istanza di un nuovo CosmosClient per conto dell'utente, con l'ambito di una singola chiave di partizione.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="differences-between-rbac-and-resource-tokens"></a>Differenze tra i token RBAC e le risorse

| Oggetto | Controllo degli accessi in base al ruolo | Token delle risorse |
|--|--|--|
| Authentication  | Con Azure Active Directory (Azure AD). | In base agli utenti di Azure Cosmos DB nativi<br>L'integrazione di token di risorsa con Azure AD richiede un lavoro aggiuntivo per colmare le identità Azure AD e Azure Cosmos DB gli utenti. |
| Autorizzazione | Basato sui ruoli: le definizioni dei ruoli consentono di eseguire il mapping delle azioni consentite e possono essere assegnate a più identità. | Basata sulle autorizzazioni: per ogni utente Azure Cosmos DB è necessario assegnare le autorizzazioni di accesso ai dati. |
| Ambito del token | Un token AAD trasporta l'identità del richiedente. Questa identità viene confrontata con tutte le definizioni di ruolo assegnate per eseguire l'autorizzazione. | Un token di risorsa trasporta l'autorizzazione concessa a un utente Azure Cosmos DB specifico in una risorsa Azure Cosmos DB specifica. Le richieste di autorizzazione su risorse diverse possono richiedere token diversi. |
| Aggiornamento del token | Il token AAD viene aggiornato automaticamente da Azure Cosmos DB SDK alla scadenza. | L'aggiornamento del token di risorsa non è supportato. Quando un token di risorsa scade, è necessario rilasciarne uno nuovo. |

## <a name="add-users-and-assign-roles"></a>Aggiungere utenti e assegnare ruoli

Per aggiungere l'accesso in lettura dell'account Azure Cosmos DB al proprio account utente, è necessario che il proprietario di una sottoscrizione esegua la procedura seguente nel portale di Azure.

1. Aprire il portale di Azure e selezionare l'account Azure Cosmos DB.
2. Fare clic sulla scheda **Controllo di accesso (IAM)** e quindi su **+ Aggiungi assegnazione di ruolo**.
3. Nel riquadro **Aggiungi assegnazione di ruolo**, nella casella **Ruolo**, selezionare **Ruolo Lettore dell'account Cosmos DB**.
4. Nella **casella assegna accesso a** selezionare **Azure ad utente, gruppo o applicazione**.
5. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso.  È possibile eseguire ricerche nella directory in base al nome visualizzato, all'indirizzo di posta elettronica o all'identificatore dell'oggetto.
    L'applicazione, il gruppo o l'utente selezionato viene visualizzato nell'elenco dei membri selezionati.
6. Fare clic su **Salva**.

L'entità può ora leggere le risorse di Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Eliminare o esportare i dati utente

Come servizio di database, Azure Cosmos DB consente di cercare, selezionare, modificare ed eliminare tutti i dati presenti nel database o nei contenitori. È tuttavia responsabilità dell'utente utilizzare le API fornite e definire la logica necessaria per trovare e cancellare eventuali dati personali, se necessario. Ogni API multimodello (SQL, MongoDB, Gremlin, Cassandra, tabella) fornisce SDK di linguaggio diversi che contengono metodi per la ricerca e l'eliminazione di dati in base a predicati personalizzati. È inoltre possibile abilitare la funzionalità [time-to live (TTL)](time-to-live.md) per eliminare automaticamente i dati dopo un periodo specificato, senza incorrere in costi aggiuntivi.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla sicurezza di Cosmos database, vedere [Cosmos DB sicurezza del database](database-security.md).
- Per informazioni su come costruire i token di autorizzazione di Azure Cosmos DB, vedere [Access Control on Azure Cosmos DB Resources](/rest/api/cosmos-db/access-control-on-cosmosdb-resources) (Controllo di accesso per le risorse di Azure Cosmos DB).
- Esempi di gestione degli utenti con utenti e autorizzazioni, [esempi di gestione utenti di .NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
