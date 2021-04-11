---
title: Persistenza e serializzazione dei dati in Durable Functions-Azure
description: Informazioni sul modo in cui l'estensione Durable Functions per funzioni di Azure rende permanente i dati
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057984"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Persistenza e serializzazione dei dati in Durable Functions (funzioni di Azure)

Durable Functions rende automaticamente permanenti i parametri della funzione, i valori restituiti e altro stato a un back-end durevole per offrire un'esecuzione affidabile. Tuttavia, la quantità e la frequenza dei dati salvati in modo permanente in un archivio durevole possono influito sui costi delle transazioni di archiviazione e delle prestazioni dell'applicazione. A seconda del tipo di dati archiviati dall'applicazione, potrebbe essere necessario prendere in considerazione anche la conservazione dei dati e i criteri di privacy.

## <a name="azure-storage"></a>Archiviazione di Azure

Per impostazione predefinita, Durable Functions Salva in modo permanente i dati in code, tabelle e BLOB in un account di [archiviazione di Azure](https://azure.microsoft.com/services/storage/) specificato.

### <a name="queues"></a>Code

Durable Functions usa le code di archiviazione di Azure per pianificare in modo affidabile tutte le esecuzioni di funzioni. Questi messaggi della coda contengono input o output della funzione, a seconda che il messaggio venga utilizzato per pianificare un'esecuzione o restituire un valore a una funzione chiamante. Questi messaggi della coda includono inoltre metadati aggiuntivi che Durable Functions utilizza per finalità interne, come il routing e la correlazione end-to-end. Al termine dell'esecuzione di una funzione in risposta a un messaggio ricevuto, il messaggio viene eliminato e anche il risultato dell'esecuzione può essere salvato in modo permanente nelle tabelle di archiviazione di Azure o nei BLOB di archiviazione di Azure.

All'interno di un singolo [Hub attività](durable-functions-task-hubs.md), durable Functions Crea e aggiunge messaggi a una coda di *elementi di lavoro* denominata `<taskhub>-workitem` per la pianificazione delle funzioni di attività e una o più *code di controllo* denominate `<taskhub>-control-##` per pianificare o riprendere gli agenti di orchestrazione e le funzioni dell'entità. Il numero di code di controllo è uguale al numero di partizioni configurate per l'applicazione. Per ulteriori informazioni sulle code e sulle partizioni, vedere la [documentazione relativa a prestazioni e scalabilità](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tabelle

Una volta che le orchestrazioni elaborano i messaggi correttamente, i record delle azioni risultanti vengono salvati in modo permanente nella tabella di *cronologia* denominata `<taskhub>History` . Anche gli input, gli output e i dati di stato personalizzati dell'orchestrazione vengono salvati in modo permanente nella tabella delle *istanze* denominata `<taskhub>Instances` .

### <a name="blobs"></a>BLOB

Nella maggior parte dei casi Durable Functions non usa i BLOB di archiviazione di Azure per salvare in modo permanente i dati. Tuttavia, le code e le tabelle presentano [limiti di dimensioni](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) che possono impedire a Durable Functions di salvare in modo permanente tutti i dati necessari in un messaggio di riga o di coda di archiviazione. Ad esempio, quando una parte di dati che devono essere salvati in modo permanente in una coda è maggiore di 45 KB durante la serializzazione, Durable Functions comprimerà i dati e li archivierà in un BLOB. Quando si rende persistenti i dati nell'archivio BLOB in questo modo, la funzione durevole archivia un riferimento a tale BLOB nel messaggio della riga o della coda della tabella. Quando Durable Functions necessario recuperare i dati, verranno recuperati automaticamente dal BLOB. Questi BLOB vengono archiviati nel contenitore BLOB `<taskhub>-largemessages` .

> [!NOTE]
> I passaggi aggiuntivi per le operazioni di compressione e BLOB per i messaggi di grandi dimensioni possono essere costosi in termini di costi della CPU e della latenza di I/O. Inoltre, Durable Functions necessario caricare i dati salvati in modo permanente in memoria e possono eseguire questa operazione per molte esecuzioni di funzioni diverse nello stesso momento. Di conseguenza, i payload di dati di grandi dimensioni possono causare anche un utilizzo elevato della memoria. Per ridurre al minimo il sovraccarico di memoria, considerare la possibilità di salvare manualmente i payload di dati di grandi dimensioni (ad esempio, nell'archivio BLOB) e di passare invece i riferimenti a questi dati. In questo modo il codice può caricare i dati solo quando è necessario per evitare carichi ridondanti durante la riproduzione della funzione dell'agente di [orchestrazione](durable-functions-orchestrations.md#reliability). Tuttavia, l'archiviazione dei payload su disco *non* è consigliata perché non è garantito che lo stato su disco sia disponibile perché le funzioni possono essere eseguite in macchine virtuali diverse per tutta la loro durata.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Tipi di dati serializzati e salvati in modo permanente
Di seguito è riportato un elenco dei diversi tipi di dati che verranno serializzati e resi permanente quando si utilizzano le funzionalità di Durable Functions:

- Tutti gli input e gli output delle funzioni dell'agente di orchestrazione, dell'attività e dell'entità, inclusi tutti gli ID e le eccezioni non gestite
- Nomi di agente di orchestrazione, attività e funzioni di entità
- Payload e nomi degli eventi esterni
- Payload di stato dell'orchestrazione personalizzati
- Messaggi di terminazione dell'orchestrazione
- Payload del timer durevole
- URL, intestazioni e payload di richiesta e risposta HTTP durevoli
- Payload del segnale e della chiamata di entità
- Payload dello stato dell'entità

### <a name="working-with-sensitive-data"></a>Utilizzo dei dati sensibili
Quando si usa archiviazione di Azure, tutti i dati vengono crittografati automaticamente a riposo. Tuttavia, chiunque disponga dell'accesso all'account di archiviazione può leggere i dati nel formato non crittografato. Se è necessaria una protezione più avanzata per i dati sensibili, è consigliabile innanzitutto crittografare i dati usando le proprie chiavi di crittografia in modo che Durable Functions mantenga i dati in un formato pre-crittografato.

In alternativa, gli utenti .NET hanno la possibilità di implementare provider di serializzazione personalizzati che forniscono la crittografia automatica. Un esempio di serializzazione personalizzata con crittografia è reperibile in [questo esempio di GitHub](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Se si decide di implementare la crittografia a livello di applicazione, tenere presente che le orchestrazioni e le entità possono esistere per una quantità di tempo indefinita. Ciò è importante quando si verifica il tempo di rotazione delle chiavi di crittografia perché un'orchestrazione o entità può essere eseguita più a lungo rispetto ai criteri di rotazione delle chiavi. Se si verifica una rotazione della chiave, la chiave utilizzata per crittografare i dati potrebbe non essere più disponibile per decrittografarla alla successiva esecuzione dell'orchestrazione o dell'entità. La crittografia dei clienti è pertanto consigliata solo quando si prevede che le orchestrazioni e le entità vengano eseguite per periodi di tempo relativamente brevi.

## <a name="customizing-serialization-and-deserialization"></a>Personalizzazione della serializzazione e della deserializzazione

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Logica di serializzazione predefinita

Durable Functions usa internamente [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) per serializzare i dati dell'orchestrazione e dell'entità in JSON. Le impostazioni predefinite Durable Functions USA per Json.NET sono:

**Input, output e stato:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Eccezioni**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Leggi la documentazione più dettagliata su `JsonSerializerSettings` [qui](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Personalizzazione della serializzazione con gli attributi .NET

Quando si serializzano i dati, Json.NET cerca [diversi attributi](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) per le classi e le proprietà che controllano la modalità di serializzazione e deserializzazione dei dati da JSON. Se si è proprietari del codice sorgente per il tipo di dati passato a Durable Functions API, è consigliabile aggiungere questi attributi al tipo per personalizzare la serializzazione e la deserializzazione.

## <a name="customizing-serialization-with-dependency-injection"></a>Personalizzazione della serializzazione con l'inserimento di dipendenze

Le app per le funzioni destinate a .NET ed eseguite nel runtime di Functions V3 possono usare l' [inserimento di dipendenze (di)](../functions-dotnet-dependency-injection.md) per personalizzare la modalità di serializzazione dei dati e delle eccezioni. Il codice di esempio seguente illustra come usare l'inserimento delle dipendenze per sostituire le impostazioni di serializzazione predefinite di Json.NET usando implementazioni personalizzate delle `IMessageSerializerSettingsFactory` `IErrorSerializerSettingsFactory` interfacce del servizio e.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Logica di serializzazione e deserializzazione

Le applicazioni del nodo funzioni di Azure usano [ `JSON.stringify()` per la serializzazione](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) e [ `JSON.Parse()` la deserializzazione](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). La maggior parte dei tipi deve essere serializzata e deserializzata facilmente. Nei casi in cui la logica predefinita non è sufficiente, la definizione di un `toJSON()` metodo sull'oggetto comporterà il Hijack della logica di serializzazione. Tuttavia, non esiste alcuna analogia per la deserializzazione dell'oggetto.

Per la personalizzazione completa della pipeline di serializzazione/deserializzazione, valutare la possibilità di gestire la serializzazione e la deserializzazione con codice personalizzato e passare i dati come stringhe.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Logica di serializzazione e deserializzazione

Si consiglia vivamente di utilizzare le annotazioni di tipo per assicurarsi che Durable Functions serializza e deserializza correttamente i dati. Sebbene molti tipi incorporati vengano gestiti automaticamente, per alcuni tipi di dati predefiniti è necessario che le annotazioni di tipo mantengano il tipo durante la deserializzazione.

Per i tipi di dati personalizzati, è necessario definire la serializzazione e la deserializzazione JSON di un tipo di dati esportando un `to_json` metodo e statico `from_json` dalla classe.

---
