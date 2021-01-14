---
title: Arrestare il server applicazioni normalmente.
description: Questo articolo fornisce informazioni sull'arresto normale del server dell'app SignalR
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201672"
---
# <a name="server-graceful-shutdown"></a>Arresto normale del server
Il servizio Microsoft Azure SignalR fornisce due modalità per arrestare normalmente un server. 

Il vantaggio principale derivante dall'utilizzo di questa funzionalità è impedire al cliente di riscontrare cadute di connessione inaspettatamente. 

In alternativa, è possibile attendere che le connessioni client si chiudano in relazione alla logica di business o persino alla migrazione della connessione client a un altro server senza perdere i dati. 

## <a name="how-it-works"></a>Funzionamento

In generale, ci saranno quattro fasi in un processo di arresto normale:

1. **Impostazione del server offline**

    Significa che non verrà più stindirizzata alcuna connessione client a questo server.

2. **Hook di trigger `OnShutdown`**

    È possibile registrare hook di arresto per ogni hub di proprietà del server.
    Verranno chiamati per quanto riguarda l'ordine registrato subito dopo aver ricevuto una risposta **FINACK** dal servizio Azure SignalR, il che significa che il server è stato impostato come offline nel servizio Azure SignalR.

    È possibile trasmettere messaggi o eseguire alcuni processi di pulizia in questa fase, una volta eseguiti tutti gli hook di arresto, si procederà alla fase successiva.

3. **Attendere il completamento di tutte le connessioni client**, a seconda della modalità scelta, potrebbe essere:

    **Modalità impostata su WaitForClientsToClose**

    Il servizio Azure SignalR conterrà i client esistenti.

    Potrebbe essere necessario progettare un modo, ad esempio trasmettere un messaggio di chiusura a tutti i client, e quindi consentire ai client di decidere quando chiudersi o riconnettersi.

    Leggere [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) per un esempio di utilizzo, in cui viene trasmesso un messaggio di uscita per attivare la chiusura del client nell'hook di arresto.

    **Modalità impostata su MigrateClients**

    Il servizio Azure SignalR tenterà di reindirizzare la connessione client in questo server a un altro server valido. 
    
    In questo scenario, `OnConnectedAsync` e `OnDisconnectedAsync` verranno attivati nel nuovo server e nel server precedente rispettivamente con un `IConnectionMigrationFeature` set in `HttpContext` , che può essere usato per identificare se è stata eseguita la migrazione della connessione client. Potrebbe essere utile in particolare per gli scenari con stato.

    Una volta recapitato il messaggio corrente, verrà immediatamente eseguita la migrazione della connessione client, il che significa che il messaggio successivo verrà indirizzato al nuovo server.

4. **Arresta connessioni server**

    Dopo che tutte le connessioni client sono state chiuse/migrate oppure è stato superato il timeout (30s per impostazione predefinita),

    SignalR Server SDK proseguirà il processo di arresto in questa fase e chiuderà tutte le connessioni al server.

    Le connessioni client verranno comunque eliminate se non è stato possibile chiudere/migrare. Ad esempio, non è stato completato alcun server di destinazione adatto/messaggio client-server corrente.

## <a name="sample-codes"></a>Codici di esempio.

Aggiungere le opzioni seguenti quando `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>configurare `OnConnected` e `OnDisconnected` impostare la modalità di arresto normale su `MigrateClients` .

È stato introdotto un "IConnectionMigrationFeature" per indicare se è stata eseguita la migrazione di una connessione.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```