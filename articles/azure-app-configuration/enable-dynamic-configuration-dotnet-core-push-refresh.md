---
title: "Esercitazione: usare la configurazione dinamica usando l'aggiornamento push in un'app .NET Core"
titleSuffix: Azure App Configuration
description: Questa esercitazione illustra come aggiornare dinamicamente i dati di configurazione per le app .NET Core usando l'aggiornamento push
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701519"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Esercitazione: usare la configurazione dinamica usando l'aggiornamento push in un'app .NET Core

La libreria client .NET Core per la configurazione delle app supporta l'aggiornamento della configurazione su richiesta senza causare il riavvio di un'applicazione. Un'applicazione può essere configurata in modo da rilevare le modifiche nella configurazione dell'app usando uno o entrambi i due approcci seguenti.

1. Modello di polling: questo è il comportamento predefinito che usa il polling per rilevare le modifiche nella configurazione. Una volta che il valore memorizzato nella cache di un'impostazione scade, la chiamata successiva a `TryRefreshAsync` o `RefreshAsync` Invia una richiesta al server per verificare se la configurazione è stata modificata ed esegue il pull della configurazione aggiornata, se necessario.

1. Modello push: USA [gli eventi di configurazione dell'app](./concept-app-configuration-event.md) per rilevare le modifiche nella configurazione. Una volta configurata la configurazione dell'app per l'invio di eventi di modifica del valore chiave a griglia di eventi di Azure, l'applicazione può usare questi eventi per ottimizzare il numero totale di richieste necessarie per aggiornare la configurazione. Le applicazioni possono scegliere di sottoscriverle direttamente da griglia di eventi o anche se uno dei [gestori di eventi supportati](../event-grid/event-handlers.md) , ad esempio un webhook, una funzione di Azure o un argomento del bus di servizio.

Le applicazioni possono scegliere di sottoscrivere questi eventi direttamente da griglia di eventi o tramite un webhook oppure tramite l'invio di eventi al bus di servizio di Azure. Azure Service Bus SDK fornisce un'API per la registrazione di un gestore di messaggi che semplifica questo processo per le applicazioni che non dispongono di un endpoint HTTP o non desiderano eseguire il polling continuo della griglia di eventi per le modifiche.

Questa esercitazione illustra come implementare gli aggiornamenti della configurazione dinamica nel codice usando l'aggiornamento push. Si basa sull'app presentata negli argomenti di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app .NET Core con Configurazione app](./quickstart-dotnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima opzione ed è disponibile per le piattaforme Windows, macOS e Linux.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare una sottoscrizione per inviare eventi di modifica della configurazione dalla configurazione dell'app a un argomento del bus di servizio
> * Configurare l'app .NET Core per aggiornarne la configurazione in risposta alle modifiche apportate alla configurazione dell'app.
> * Utilizzare la configurazione più recente nell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Configurare l'argomento e la sottoscrizione del bus di servizio di Azure

Questa esercitazione usa l'integrazione del bus di servizio per griglia di eventi per semplificare il rilevamento delle modifiche di configurazione per le applicazioni che non vogliono eseguire il polling continuo della configurazione dell'app per le modifiche. Azure Service Bus SDK fornisce un'API per registrare un gestore di messaggi che può essere usato per aggiornare la configurazione quando vengono rilevate modifiche nella configurazione dell'app. Seguire i passaggi della [Guida introduttiva: usare la portale di Azure per creare un argomento e una sottoscrizione del bus di servizio](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) per creare uno spazio dei nomi, un argomento e una sottoscrizione del bus di servizio.

Una volta create le risorse, aggiungere le variabili di ambiente seguenti. Questi verranno usati per registrare un gestore eventi per le modifiche di configurazione nel codice dell'applicazione.

| Chiave | Valore |
|---|---|
| ServiceBusConnectionString | Stringa di connessione per lo spazio dei nomi del bus di servizio |
| ServiceBusTopic | Nome dell'argomento del bus di servizio |
| ServiceBusSubscription | Nome della sottoscrizione del bus di servizio |

## <a name="set-up-event-subscription"></a>Configurare la sottoscrizione di eventi

1. Aprire la risorsa di configurazione dell'app nel portale di Azure, quindi fare clic su on `+ Event Subscription` nel `Events` riquadro.

    ![Eventi di configurazione dell'app](./media/events-pane.png)

1. Immettere un nome per il `Event Subscription` e il `System Topic` .

    ![Crea sottoscrizione di eventi](./media/create-event-subscription.png)

1. Selezionare il `Endpoint Type` come `Service Bus Topic` , scegliere l'argomento Service Bus, quindi fare clic su on `Confirm Selection` .

    ![Endpoint del bus di servizio per la sottoscrizione di eventi](./media/event-subscription-servicebus-endpoint.png)

1. Fare clic su `Create` per creare la sottoscrizione di eventi.

1. Fare clic sul `Event Subscriptions` `Events` riquadro per verificare che la sottoscrizione sia stata creata correttamente.

    ![Sottoscrizioni di eventi di configurazione delle app](./media/event-subscription-view.png)

> [!NOTE]
> Quando si esegue la sottoscrizione per le modifiche alla configurazione, è possibile usare uno o più filtri per ridurre il numero di eventi inviati all'applicazione. Possono essere configurati come [filtri di sottoscrizione di griglia di eventi](../event-grid/event-filtering.md) o [filtri di sottoscrizione del bus di servizio](../service-bus-messaging/topic-filters.md). È ad esempio possibile usare un filtro di sottoscrizione per sottoscrivere solo gli eventi per le modifiche in una chiave che inizia con una stringa specifica.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registrare il gestore eventi per ricaricare i dati dalla configurazione dell'app

Aprire *Program.cs* e aggiornare il file con il codice seguente.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

Il metodo [IsDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) viene usato per impostare il valore memorizzato nella cache per i valori di chiave registrati per l'aggiornamento come dirty. In questo modo si garantisce che la chiamata successiva a `RefreshAsync` o `TryRefreshAsync` riconvalida i valori memorizzati nella cache con la configurazione dell'app e li aggiorni se necessario.

Un ritardo casuale viene aggiunto prima che il valore memorizzato nella cache venga contrassegnato come Dirty per ridurre la potenziale limitazione nel caso in cui più istanze vengano aggiornate contemporaneamente. Il ritardo massimo predefinito prima che il valore memorizzato nella cache venga contrassegnato come Dirty è 30 secondi, ma è possibile eseguirne l'override passando un `TimeSpan` parametro facoltativo al `SetDirty` metodo.

> [!NOTE]
> Per ridurre il numero di richieste alla configurazione dell'app quando si usa l'aggiornamento push, è importante chiamare `SetCacheExpiration(TimeSpan cacheExpiration)` con un valore appropriato per il `cacheExpiration` parametro. Questa operazione Controlla la scadenza della cache per l'aggiornamento pull e può essere usata come rete di sicurezza in caso di problemi con la sottoscrizione di eventi o con la sottoscrizione del bus di servizio. Il valore consigliato è `TimeSpan.FromDays(30)`.

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **AppConfigurationConnectionString** e impostarla sulla chiave di accesso per l'archivio di configurazione dell'app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Eseguire il comando seguente per compilare l'app console:

    ```console
     dotnet build
    ```

1. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```console
     dotnet run
    ```

    ![Aggiornamento push eseguito prima dell'aggiornamento](./media/dotnet-core-app-pushrefresh-initial.png)

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Configuration Explorer** e aggiornare i valori delle chiavi seguenti:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure - Aggiornati |

1. Attendere 30 secondi per consentire l'elaborazione dell'evento e la configurazione da aggiornare.

    ![Aggiornamento push eseguito dopo l'aggiornamento](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app .NET Core per aggiornare in modo dinamico le impostazioni di configurazione di Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)