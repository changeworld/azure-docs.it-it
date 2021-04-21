---
title: Configurare il controllo degli accessi in base al ruolo per l'account Azure Cosmos DB con Azure AD
description: Informazioni su come configurare il controllo degli accessi in base al ruolo con Azure Active Directory per l'account Azure Cosmos DB ruolo
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 9de41835e33d50a670a44089cb10d44cc57e92a7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818704"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Configurare il controllo degli accessi in base al ruolo Azure Active Directory per l'account Azure Cosmos DB (anteprima)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB controllo degli accessi in base al ruolo è attualmente in anteprima. Questa versione di anteprima viene fornita senza un Contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Questo articolo riguarda il controllo degli accessi in base al ruolo per le operazioni del piano dati in Azure Cosmos DB. Se si usano operazioni del piano di gestione, vedere l'articolo [Controllo degli](role-based-access-control.md) accessi in base al ruolo applicato alle operazioni del piano di gestione.

Azure Cosmos DB un sistema di controllo degli accessi in base al ruolo predefinito che consente di:

- Autenticare le richieste di dati con Azure Active Directory (Azure AD).
- Autorizzare le richieste di dati con un modello di autorizzazione con granularità fine e basata sui ruoli.

## <a name="concepts"></a>Concetti

Il Azure Cosmos DB controllo degli accessi in base al ruolo del piano dati si basa sui concetti comunemente disponibili in altri sistemi di controllo degli accessi in base al ruolo di Azure, ad esempio [il controllo degli accessi in](../role-based-access-control/overview.md)base al ruolo di Azure:

- Il [modello di autorizzazione](#permission-model) è costituito da un set di **azioni**. ognuna di queste azioni viene mappata a una o più operazioni di database. Alcuni esempi di azioni includono la lettura di un elemento, la scrittura di un elemento o l'esecuzione di una query.
- Azure Cosmos DB utenti creano **[definizioni di ruolo](#role-definitions)** contenenti un elenco di azioni consentite.
- Le definizioni di ruolo vengono assegnate a identità Azure AD tramite **[assegnazioni di ruolo](#role-assignments)**. Un'assegnazione di ruolo definisce anche l'ambito a cui si applica la definizione del ruolo. Attualmente, tre ambiti sono:
    - Un Azure Cosmos DB account,
    - Un database Azure Cosmos DB,
    - Oggetto Azure Cosmos DB contenitore.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Concetti relativi al controllo degli accessi in base al ruolo":::

> [!NOTE]
> Il Azure Cosmos DB controllo degli accessi in base al ruolo non espone attualmente alcuna definizione di ruolo incorporata.

## <a name="permission-model"></a><a id="permission-model"></a> Modello di autorizzazione

> [!IMPORTANT]
> Questo modello di autorizzazione riguarda solo le operazioni di database che consentono di leggere e scrivere dati. Non copre **alcun** tipo di operazioni di gestione, ad esempio la creazione di contenitori o la modifica della velocità effettiva. Ciò significa che non è **possibile usare alcun SDK Azure Cosmos DB piano** dati per autenticare le operazioni di gestione con un'identità AAD. È invece necessario usare il controllo degli accessi in [base al ruolo di Azure](role-based-access-control.md) tramite:
> - [Modelli di Gestione risorse di Azure](manage-with-templates.md)
> - [Azure PowerShell script](manage-with-powershell.md),
> - [Script dell'interfaccia della riga di comando di Azure,](manage-with-cli.md)
> - Librerie di gestione di Azure disponibili in
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

La tabella seguente elenca tutte le azioni esposte dal modello di autorizzazione.

| Nome | Operazioni di database corrispondenti |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Legge i metadati dell'account. Per [informazioni dettagliate, vedere](#metadata-requests) Richieste di metadati. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Creare un nuovo elemento. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Leggere un singolo elemento in base all'ID e alla chiave di partizione (lettura da punto). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Sostituire un elemento esistente. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" un elemento, ovvero crearlo se non esiste o sostituirlo se esiste. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Eliminare un elemento. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Eseguire una [query SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Leggere dal feed di [modifiche del contenitore.](read-change-feed.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Eseguire [un'stored procedure](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Gestire [i conflitti](conflict-resolution-policies.md) per gli account con più aree di scrittura, ovvero elencare ed eliminare elementi dal feed dei conflitti. |

I caratteri jolly sono supportati sia a *livello di contenitori* *che di* elementi:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Richieste di metadati

Quando si Azure Cosmos DB SDK, questi SDK elaggono richieste di metadati di sola lettura durante l'inizializzazione e per gestire richieste di dati specifiche. Queste richieste di metadati recuperano vari dettagli di configurazione, ad esempio: 

- Configurazione globale dell'account, che include le aree di Azure in cui è disponibile l'account.
- Chiave di partizione dei contenitori o dei relativi criteri di indicizzazione.
- Elenco di partizioni fisiche che costituiscono un contenitore e i relativi indirizzi.

Non *recuperano* i dati archiviati nell'account.

Per garantire la massima trasparenza del modello di autorizzazione, queste richieste di metadati sono coperte in modo esplicito `Microsoft.DocumentDB/databaseAccounts/readMetadata` dall'azione. Questa azione deve essere consentita in ogni situazione in cui l'account Azure Cosmos DB è accessibile tramite uno dei Azure Cosmos DB SDK. Può essere assegnato (tramite un'assegnazione di ruolo) a qualsiasi livello della gerarchia Azure Cosmos DB,ovvero account, database o contenitore.

Le richieste di metadati effettive consentite `Microsoft.DocumentDB/databaseAccounts/readMetadata` dall'azione dipendono dall'ambito a cui è assegnata l'azione:

| Ambito | Richieste consentite dall'azione |
|---|---|
| Account | - Elenco dei database nell'account<br>- Per ogni database nell'account, le azioni consentite nell'ambito del database |
| Database | - Lettura dei metadati del database<br>- Elenco dei contenitori nel database<br>- Per ogni contenitore nel database, le azioni consentite nell'ambito del contenitore |
| Contenitore | - Lettura dei metadati del contenitore<br>- Elenco delle partizioni fisiche nel contenitore<br>- Risoluzione dell'indirizzo di ogni partizione fisica |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Creare definizioni di ruolo

Quando si crea una definizione di ruolo, è necessario specificare:

- Nome dell'account Azure Cosmos DB utente.
- Gruppo di risorse contenente l'account.
- Tipo della definizione del ruolo. è `CustomRole` attualmente supportato solo .
- Nome della definizione del ruolo.
- Elenco di [azioni](#permission-model) che il ruolo deve consentire.
- Uno o più ambiti a cui è possibile assegnare la definizione del ruolo. Gli ambiti supportati sono:
    - `/` (a livello di account),
    - `/dbs/<database-name>` (a livello di database),
    - `/dbs/<database-name>/colls/<container-name>` (a livello di contenitore).

> [!NOTE]
> Le operazioni descritte di seguito sono attualmente disponibili in:
> - Azure PowerShell: [Az.CosmosDB versione 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfaccia della riga di comando di Azure: estensione ['cosmosdb-preview' versione 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Creare un ruolo denominato *MyReadOnlyRole che* contenga solo azioni di lettura:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Creare un ruolo denominato *MyReadWriteRole* che contiene tutte le azioni:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Elencare le definizioni di ruolo create per recuperare i relativi ID:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Creare un ruolo denominato *MyReadOnlyRole che* contenga solo azioni di lettura:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Creare un ruolo denominato *MyReadWriteRole* contenente tutte le azioni:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Elencare le definizioni di ruolo create per recuperare i relativi ID:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Creare assegnazioni di ruolo

Dopo aver creato le definizioni dei ruoli, è possibile associarle alle identità di AAD. Quando si crea un'assegnazione di ruolo, è necessario specificare:

- Nome dell'account Azure Cosmos DB locale.
- Gruppo di risorse contenente l'account.
- ID della definizione del ruolo da assegnare.
- ID entità dell'identità a cui deve essere assegnata la definizione del ruolo.
- Ambito dell'assegnazione di ruolo. Gli ambiti supportati sono:
    - `/` (a livello di account)
    - `/dbs/<database-name>` (a livello di database)
    - `/dbs/<database-name>/colls/<container-name>` (a livello di contenitore)

  L'ambito deve corrispondere o essere un ambito secondario di uno degli ambiti assegnabili della definizione del ruolo.

> [!NOTE]
> Se si vuole creare un'assegnazione di ruolo per un'entità servizio, assicurarsi di usare il relativo **ID** oggetto, come illustrato nella sezione **Applicazioni** aziendali del pannello Azure Active Directory **portale.**

> [!NOTE]
> Le operazioni descritte di seguito sono attualmente disponibili in:
> - Azure PowerShell: [Az.CosmosDB versione 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfaccia della riga di comando di Azure: [estensione "cosmosdb-preview" versione 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Assegnare un ruolo a un'identità:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Assegnare un ruolo a un'identità:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inizializzare l'SDK con Azure AD

Per usare il controllo Azure Cosmos DB controllo degli accessi in base al ruolo nell'applicazione, è necessario aggiornare la modalità di inizializzazione dell'SDK Azure Cosmos DB. Anziché passare la chiave primaria dell'account, è necessario passare un'istanza di una `TokenCredential` classe . Questa istanza fornisce all'SDK Azure Cosmos DB il contesto necessario per recuperare un token AAD per conto dell'identità che si vuole usare.

Il modo in cui si crea `TokenCredential` un'istanza es supera l'ambito di questo articolo. Esistono molti modi per creare un'istanza di questo tipo a seconda del tipo di identità di AAD che si vuole usare (entità utente, entità servizio, gruppo e così via). Soprattutto, `TokenCredential` l'istanza deve risolversi nell'identità (ID entità) a cui sono stati assegnati i ruoli. È possibile trovare esempi di creazione di una `TokenCredential` classe:

- [In .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [In Java](/java/api/overview/azure/identity-readme#credential-classes)
- [In JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)
- Nell'API REST

Gli esempi seguenti usano un'entità servizio con `ClientSecretCredential` un'istanza di .

### <a name="in-net"></a>In .NET

Il Azure Cosmos DB controllo degli accessi in base al ruolo è attualmente supportato nella `preview` versione [di .NET SDK V3.](sql-api-sdk-dotnet-standard.md)

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>In Java

Il Azure Cosmos DB controllo degli accessi in base al ruolo è attualmente supportato in [Java SDK V4.](sql-api-sdk-java-v4.md)

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>In JavaScript

Il Azure Cosmos DB controllo degli accessi in base al ruolo è attualmente supportato in [JavaScript SDK V3.](sql-api-sdk-node.md)

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

### <a name="in-rest-api"></a>Nell'API REST

Il Azure Cosmos DB controllo degli accessi in base al ruolo è attualmente supportato con la versione 2021-03-15 dell'API REST. Quando si costruisce [l'intestazione di](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)autorizzazione , impostare il parametro **di** tipo su **aad** e la firma hash **(sig)** sul **token oauth,** come illustrato nell'esempio seguente:

`type=aad&ver=1.0&sig=<token-from-oauth>`

## <a name="auditing-data-requests"></a>Controllo delle richieste di dati

Quando si usa il controllo Azure Cosmos DB controllo degli accessi in base al ruolo, i [log](cosmosdb-monitor-resource-logs.md) di diagnostica vengono aumentati con informazioni sull'identità e sull'autorizzazione per ogni operazione sui dati. In questo modo è possibile eseguire un controllo dettagliato e recuperare l'identità AAD usata per ogni richiesta di dati inviata all'account Azure Cosmos DB dati.

Queste informazioni aggiuntive vengono visualizzate nella categoria di log **DataPlaneRequests** e sono costituite da due colonne aggiuntive:

- `aadPrincipalId_g` mostra l'ID entità dell'identità di AAD usata per autenticare la richiesta.
- `aadAppliedRoleAssignmentId_g` mostra [l'assegnazione di](#role-assignments) ruolo rispettata durante l'autorizzazione della richiesta.

## <a name="limits"></a>Limiti

- È possibile creare fino a 100 definizioni di ruolo e 2.000 assegnazioni di ruolo per Azure Cosmos DB account.
- È possibile assegnare definizioni di ruolo solo Azure AD identità appartenenti allo stesso tenant Azure AD del proprio account Azure Cosmos DB locale.
- Azure AD la risoluzione dei gruppi non è attualmente supportata per le identità che appartengono a più di 200 gruppi.
- Il Azure AD token viene attualmente passato come intestazione con ogni singola richiesta inviata al servizio Azure Cosmos DB, aumentando le dimensioni complessive del payload.
- L'accesso ai dati Azure AD tramite [Azure Cosmos DB Explorer](data-explorer.md) non è ancora supportato. L'Azure Cosmos DB Explorer richiede ancora che l'utente abbia accesso alla chiave primaria dell'account per il momento.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Quali API di Azure Cosmos DB sono supportate dal controllo degli accessi in base al ruolo?

Attualmente è supportata solo l'API SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>È possibile gestire le definizioni e le assegnazioni di ruolo nel portale di Azure?

Il supporto per la gestione dei ruoli non è ancora disponibile nel portale di Azure.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Quali SDK in Azure Cosmos DB'API SQL supportano il controllo degli accessi in base al ruolo?

Gli SDK [.NET V3](sql-api-sdk-dotnet-standard.md) [e Java V4](sql-api-sdk-java-v4.md) sono attualmente supportati.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Il token di Azure AD viene aggiornato automaticamente dagli SDK di Azure Cosmos DB quando scade?

Sì.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>È possibile disabilitare l'uso della chiave primaria per l'account quando si usa il controllo degli accessi in base al ruolo?

La disabilitazione della chiave primaria per l'account non è attualmente possibile.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere una panoramica dell'[accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md).
- Altre informazioni sul [controllo degli accessi in base al ruolo Azure Cosmos DB gestione.](role-based-access-control.md)