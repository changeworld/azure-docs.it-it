---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110891"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).
- La versione più recente [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso.
- Ottenere la versione più recente di [.NET Identity SDK](/dotnet/api/azure.identity).
- Ottenere la versione più recente di [.NET Management SDK](../../concepts/sdk-options.md).

## <a name="installing-the-sdk"></a>Installazione dell'SDK

Per prima cosa, includere Communication Services Management SDK nel progetto C#:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>ID sottoscrizione

È necessario conoscere l'ID della sottoscrizione di Azure. Queste informazioni possono essere acquisite dal portale:

1.  Accedere all'account Azure
2.  Selezionare Sottoscrizioni nella barra laterale sinistra
3.  Selezionare la sottoscrizione necessaria
4.  Fare clic su Panoramica
5.  Selezionare l'ID sottoscrizione

In questo argomento di avvio rapido si presuppone che l'ID sottoscrizione sia stato archiviato in una variabile di ambiente denominata `AZURE_SUBSCRIPTION_ID`.

## <a name="authentication"></a>Authentication

Per comunicare con Servizi di comunicazione di Azure, è prima necessario eseguire l'autenticazione in Azure. Questa operazione viene in genere eseguita usando un'identità dell'entità servizio.

### <a name="option-1-managed-identity"></a>Opzione 1: Identità gestita

Se il codice è in esecuzione come servizio in Azure, il modo più semplice per eseguire l'autenticazione consiste nell'acquisire un'identità gestita da Azure. Vedere altre informazioni sulle [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md).

[Servizi di Azure che supportano le identità gestite](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Come usare le identità gestite nel servizio app e in Funzioni di Azure](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Identità gestita assegnata dal sistema](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Identità gestita assegnata dall'utente](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

Il parametro ClientID dell'identità gestita creata devono essere passato a `ManagedIdentityCredential` in modo esplicito.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Opzione 2: Entità servizio

Invece di usare un'identità gestita, è possibile eseguire l'autenticazione in Azure usando un'entità servizio gestita in prima persona. Per altre informazioni, vedere la documentazione relativa a [creazione e gestione di un'entità servizio in Azure Active Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

Dopo aver creato l'entità servizio, è necessario raccogliere dal portale di Azure le informazioni seguenti relative all'entità:

- **ID client**
- **Segreto client**
- **ID tenant**

Archiviare questi valori rispettivamente nelle variabili di ambiente denominate `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`. È quindi possibile creare un client di gestione di Servizi di comunicazione nel modo seguente:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Opzione 3: Identità utente

Se si vuole chiamare Azure per conto di un utente interattivo invece di usare un'identità del servizio, è possibile usare il codice seguente per creare un client di gestione di Servizi di comunicazione di Azure. Verrà aperta una finestra del browser per richiedere all'utente le credenziali MSA o Azure AD.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Gestione delle risorse di Servizi di comunicazione

### <a name="interacting-with-azure-resources"></a>Interazione con le risorse di Azure

Dopo che è stata eseguita l'autenticazione, è possibile usare il client di gestione per effettuare chiamate API.

Per ognuno degli esempi seguenti, verranno assegnate risorse di Servizi di comunicazione a un gruppo di risorse esistente.

Se è necessario creare un gruppo di risorse, è possibile usare il [portale di Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md) o l' [SDK di Azure Resource Manager](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Creare e gestire una risorsa di Servizi di comunicazione

L'istanza di Communication Services Management SDK client ( ``Azure.ResourceManager.Communication.CommunicationManagementClient`` ) può essere usata per eseguire operazioni sulle risorse dei servizi di comunicazione.

#### <a name="create-a-communication-services-resource"></a>Creare una risorsa di Servizi di comunicazione

Quando si crea una risorsa di Servizi di comunicazione, è necessario specificare il nome del gruppo di risorse e il nome della risorsa. Si noti che la proprietà `Location` sarà sempre `global` e durante l'anteprima pubblica il valore di `DataLocation` deve essere `UnitedStates`.

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Aggiornare una risorsa di Servizi di comunicazione

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Elencare tutte le risorse di Servizi di comunicazione

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Eliminare una risorsa di Servizi di comunicazione

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Gestione delle chiavi e delle stringhe di connessione

Ogni risorsa di Servizi di comunicazione ha una coppia di chiavi di accesso e le corrispondenti stringhe di connessione. È possibile accedere a queste chiavi con l'SDK di gestione e quindi usarle da altri SDK di servizi di comunicazione per autenticarsi nei servizi di comunicazione di Azure.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Ottenere le chiavi di accesso per una risorsa di Servizi di comunicazione

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Rigenerare una chiave di accesso per una risorsa di Servizi di comunicazione

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```