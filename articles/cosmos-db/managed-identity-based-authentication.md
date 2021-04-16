---
title: Come usare un'identità gestita assegnata dal sistema per accedere ai dati di Azure Cosmos DB
description: Informazioni su come configurare un'identità gestita Azure Active Directory (Azure AD) assegnata dal sistema (identità del servizio gestita) per accedere alle chiavi Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e4a41d508d15c3d8f41cc727776f233cc56c0817
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480937"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Usare le identità gestite assegnate dal sistema per accedere Azure Cosmos DB dati
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In questo articolo si imposterà una soluzione affidabile e indipendente dalla rotazione delle chiavi per accedere *alle* chiavi Azure Cosmos DB usando le [identità gestite.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) L'esempio in questo articolo Funzioni di Azure, ma è possibile usare qualsiasi servizio che supporta le identità gestite. 

Si apprenderà come creare un'app per le funzioni in grado di accedere Azure Cosmos DB dati senza dover copiare Azure Cosmos DB chiavi. L'app per le funzioni si riattiva ogni minuto e registra la temperatura corrente di un acquario. Per informazioni su come configurare un'app per le funzioni attivata da timer, vedere l'articolo Creare una funzione [in Azure attivata da un timer.](../azure-functions/functions-create-scheduled-function.md)

Per semplificare lo scenario, è [già configurata un'impostazione](./time-to-live.md) Time To Live per pulire i documenti di temperatura meno recenti. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Assegnare un'identità gestita assegnata dal sistema a un'app per le funzioni

In questo passaggio si assegna un'identità gestita assegnata dal sistema all'app per le funzioni.

1. Nella finestra [portale di Azure](https://portal.azure.com/)aprire il riquadro **Funzioni di Azure** e passare all'app per le funzioni. 

1. Aprire la **scheda Identità delle funzionalità**  >  **della** piattaforma: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Screenshot che mostra le funzionalità della piattaforma e le opzioni di identità per l'app per le funzioni.":::

1. Nella scheda **Identità** attivare lo stato **dell'identità** **del sistema** e selezionare **Salva.** Il **riquadro Identità** dovrebbe essere simile al seguente:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Screenshot che mostra lo stato dell'identità di sistema impostato su On.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Concedere l'accesso all'account Azure Cosmos

In questo passaggio si assegna un ruolo all'identità gestita assegnata dal sistema dell'app per le funzioni. Azure Cosmos DB ha più ruoli predefiniti che è possibile assegnare all'identità gestita. Per questa soluzione si useranno i due ruoli seguenti:

|Ruolo predefinito  |Descrizione  |
|---------|---------|
|[Collaboratore account DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|È in grado di gestire account Azure Cosmos DB. Consente il recupero di chiavi di lettura/scrittura. |
|[Ruolo Lettore dell'account Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Può leggere i dati degli account Azure Cosmos DB. Consente il recupero delle chiavi di lettura. |

> [!IMPORTANT]
> Il supporto per il controllo degli accessi in base al ruolo Azure Cosmos DB si applica solo alle operazioni del piano di controllo. Le operazioni del piano dati sono protette tramite chiavi primarie o token di risorsa. Per altre informazioni, vedere [l'articolo Accesso sicuro ai](secure-access-to-data.md) dati.

> [!TIP] 
> Quando si assegnano ruoli, assegnare solo l'accesso necessario. Se il servizio richiede solo la lettura dei dati, assegnare il ruolo **lettore** Cosmos DB account all'identità gestita. Per altre informazioni sull'importanza dell'accesso con privilegi minimi, vedere l'articolo Esposizione più bassa [degli account con privilegi.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

In questo scenario, l'app per le funzioni leggerà la temperatura dell'acquario e quindi scriverà i dati in un contenitore in Azure Cosmos DB. Poiché l'app per le funzioni deve scrivere i dati, è necessario assegnare il ruolo **Collaboratore account DocumentDB.** 

### <a name="assign-the-role-using-azure-portal"></a>Assegnare il ruolo usando portale di Azure

1. Accedere al portale di Azure e passare all'account Azure Cosmos DB personale. Aprire il **riquadro Controllo di accesso (IAM)** e quindi la scheda **Assegnazioni di** ruolo:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Screenshot che mostra il riquadro Controllo di accesso e la scheda Assegnazioni di ruolo.":::

1. Selezionare **Aggiungi** > **Aggiungi assegnazione di ruolo**.

1. Il **pannello Aggiungi assegnazione di** ruolo si apre a destra:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Screenshot che mostra il riquadro Aggiungi assegnazione di ruolo.":::

   * **Ruolo:** selezionare **Collaboratore account DocumentDB**
   * **Assegnare l'accesso** a : nella **sottosezione Seleziona identità gestita assegnata dal** sistema selezionare App per le **funzioni**.
   * **Selezionare**: il riquadro verrà popolato con tutte le app per le funzioni nella sottoscrizione con **un'identità di sistema gestita.** In questo caso, selezionare l'app per le funzioni **FishTankTemperatureService:** 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Screenshot che mostra il riquadro Aggiungi assegnazione di ruolo popolato con esempi.":::

1. Dopo aver selezionato l'app per le funzioni, selezionare **Salva.**

### <a name="assign-the-role-using-azure-cli"></a>Assegnare il ruolo usando l'interfaccia della riga di comando di Azure

Per assegnare il ruolo usando l'interfaccia della riga di comando di Azure, aprire il Azure Cloud Shell ed eseguire i comandi seguenti:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Accedere alle chiavi Azure Cosmos DB a livello di codice

È ora disponibile un'app per le funzioni con un'identità gestita assegnata dal sistema con il ruolo **Collaboratore Account DocumentDB** nelle autorizzazioni Azure Cosmos DB sistema. Il codice dell'app per le funzioni seguente otterrà le chiavi Azure Cosmos DB, creerà un oggetto CosmosClient, otterrà la temperatura del acquario e quindi lo salverà Azure Cosmos DB.

Questo esempio usa [l'API List Keys](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) per accedere alle chiavi Azure Cosmos DB account.

> [!IMPORTANT] 
> Per assegnare il [ruolo lettore Cosmos DB](#grant-access-to-your-azure-cosmos-account) account, è necessario usare l'API Elenca chiavi di [sola lettura](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Verranno popolate solo le chiavi di sola lettura.

L'API List Keys restituisce `DatabaseAccountListKeysResult` l'oggetto . Questo tipo non è definito nelle librerie C#. Il codice seguente illustra l'implementazione di questa classe:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

L'esempio usa anche un documento semplice denominato "TemperatureRecord", definito come segue:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Si userà la libreria [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) per ottenere il token di identità gestita assegnato dal sistema. Per altre informazioni su come ottenere il token e altre informazioni sulla libreria, vedere l'articolo Autenticazione da servizio `Microsoft.Azure.Service.AppAuthentication` [a](/dotnet/api/overview/azure/service-to-service-authentication) servizio.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

A questo punto è possibile distribuire [l'app per le funzioni](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione basata su certificati con Azure Cosmos DB e Azure Active Directory](certificate-based-authentication.md)
* [Proteggere Azure Cosmos DB chiavi con Azure Key Vault](access-secrets-from-keyvault.md)
* [Baseline di sicurezza per Azure Cosmos DB](security-baseline.md)
