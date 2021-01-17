---
title: Creare entità del bus di servizio di Azure a livello di codice | Microsoft Docs
description: Questo articolo illustra come usare il provisioning di entità e spazi dei nomi del bus di servizio in modo dinamico o a livello di codice.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539883"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Eseguire il provisioning dinamico di spazi dei nomi ed entità del bus di servizio 
Le librerie di gestione del bus di servizio di Azure possono eseguire il provisioning di entità e spazi dei nomi del bus di servizio in modo dinamico, per consentire distribuzioni complesse e scenari di messaggistica e permettere di determinare a livello di codice le entità di cui eseguire il provisioning. Queste librerie sono attualmente disponibili per .NET.

## <a name="overview"></a>Panoramica
Sono disponibili tre librerie di gestione per creare e gestire entità del bus di servizio. Ad esempio:

- [Azure. Messaging. ServiceBus. Administration](#azuremessagingservicebusadministration)
- [Microsoft. Azure. ServiceBus. Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Tutti questi pacchetti supportano le operazioni di creazione, Get, elenco, eliminazione, aggiornamento, eliminazione e aggiornamento su **code, argomenti e sottoscrizioni**. Tuttavia, solo [Microsoft. Azure. Management. ServiceBus](#microsoftazuremanagementservicebus) supporta le operazioni create, Update, List, Get e Delete sugli **spazi dei nomi**, elencando e rigenerando le chiavi di firma di accesso condiviso e altro ancora. 

La libreria Microsoft. Azure. Management. ServiceBus funziona solo con l'autenticazione Azure Active Directory (Azure AD) e non supporta l'uso di una stringa di connessione. Mentre le altre due librerie (Azure. Messaging. ServiceBus e Microsoft. Azure. ServiceBus) supportano l'uso di una stringa di connessione per l'autenticazione con il servizio e sono più facili da usare. Tra queste librerie, Azure. Messaging. ServiceBus è il più recente ed è quello che si consiglia di usare.

Le sezioni seguenti forniscono altri dettagli su queste librerie. 

## <a name="azuremessagingservicebusadministration"></a>Azure. Messaging. ServiceBus. Administration
È possibile usare la classe [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) nello spazio dei nomi [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration) per gestire spazi dei nomi, code, argomenti e sottoscrizioni. Ecco il codice di esempio. Per un esempio completo, vedere [esempio CRUD](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. ServiceBus. Management 
È possibile usare la classe [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) nello spazio dei nomi [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) per gestire spazi dei nomi, code, argomenti e sottoscrizioni. Ecco il codice di esempio: 

> [!NOTE]
> Si consiglia di usare la `ServiceBusAdministrationClient` classe dalla `Azure.Messaging.ServiceBus.Administration` libreria, che è l'SDK più recente. Per informazioni dettagliate, vedere la [prima sezione](#azuremessagingservicebusadministration). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Questa libreria fa parte dell'SDK del piano di controllo basato su Azure Resource Manager. 

### <a name="prerequisites"></a>Prerequisiti

Per iniziare a usare questa libreria, è necessario eseguire l'autenticazione con il servizio Azure Active Directory (Azure AD). Azure AD richiede l'autenticazione come entità servizio, che fornisce l'accesso alle risorse di Azure in uso. Per informazioni su come creare un'entità servizio, vedere uno di questi articoli:  

* [Usare il portale di Azure per creare Active Directory applicazione e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md)
* [Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Usare l'interfaccia della riga di comando di Azure per creare un'entità servizio per accedere alle risorse](/cli/azure/create-an-azure-service-principal-azure-cli)

Nel corso di queste esercitazioni vengono forniti un `AppId` (ID client), un `TenantId` e un `ClientSecret` (chiave di autenticazione) che sono usati per l'autenticazione da parte delle librerie di gestione. Per il gruppo di risorse in cui si desidera eseguire, è necessario disporre almeno delle autorizzazioni di proprietario o [**collaboratore**](../role-based-access-control/built-in-roles.md#contributor) [**dei dati del bus di servizio di Azure**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) .

### <a name="programming-pattern"></a>Modello di programmazione

Il modello di modifica delle risorse del bus di servizio segue un protocollo comune:

1. Ottenere un token da Azure AD usando la libreria **Microsoft.IdentityModel.Clients.ActiveDirectory**:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Creare l'oggetto `ServiceBusManagementClient`:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Impostare i parametri `CreateOrUpdate` sui valori specificati:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Eseguire la chiamata:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>Completare il codice per creare una coda
Ecco il codice di esempio per creare una coda del bus di servizio. Per un esempio completo, vedere l'esempio di [gestione .NET su GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>Libreria Fluent
Per un esempio dell'uso della libreria Fluent per gestire le entità del bus di servizio, vedere [questo esempio](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli argomenti di riferimento seguenti: 

- [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)