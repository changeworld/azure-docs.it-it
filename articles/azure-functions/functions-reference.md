---
title: Linee guida per lo sviluppo di funzioni di Azure
description: Informazioni sui concetti e sulle tecniche di Funzioni di Azure necessari per sviluppare funzioni in Azure in tutti i linguaggi e i binding di programmazione.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614820"
---
# <a name="azure-functions-developer-guide"></a>Guida per sviluppatori di Funzioni di Azure
In Funzioni di Azure funzioni specifiche condividono alcuni concetti tecnici e componenti di base, indipendentemente dal linguaggio o dall'associazione che vengono usati. Prima di passare all'apprendimento di dettagli specifici per un linguaggio o un'associazione, assicurarsi di leggere questa panoramica generale.

Questo articolo presuppone che sia già stato letto [Panoramica di Funzioni di Azure](functions-overview.md).

## <a name="function-code"></a>Codice di funzione
La *funzione* è il concetto primario in Funzioni di Azure. Una funzione contiene due elementi importanti: il codice, che può essere scritto in svariati linguaggi, e un file config, il file function.json. Per i linguaggi compilati, questo file config viene generato automaticamente dalle annotazioni nel codice. Per i linguaggi di scripting, è necessario fornire personalmente il file config.

Il file function.json definisce il trigger, le associazioni e altre impostazioni di configurazione della funzione. Ogni funzione ha un solo trigger. Il runtime usa questo file config per determinare gli eventi da monitorare e come passare i dati e restituirli da un'esecuzione della funzione. Di seguito è riportato un esempio di file function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Per altre informazioni, vedere [concetti relativi a trigger e associazioni di funzioni di Azure](functions-triggers-bindings.md).

La proprietà `bindings` è quella che consente di configurare trigger e associazioni. Ogni associazione condivide alcune impostazioni comuni e altre specifiche per tipo. Ogni associazione richiede le impostazioni seguenti:

| Proprietà    | Valori | Tipo | Commenti|
|---|---|---|---|
| tipo  | Nome dell'associazione.<br><br>Ad esempio: `queueTrigger`. | string | |
| direction | `in`, `out`  | string | Indica se l'associazione consente la ricezione di dati nella funzione o l'invio di dati dalla funzione. |
| name | Identificatore della funzione.<br><br>Ad esempio: `myQueue`. | string | Il nome che viene usato per i dati associati nella funzione. Per C#, si tratta di un nome di argomento, per JavaScript è la chiave in un elenco di chiavi/valori. |

## <a name="function-app"></a>App per le funzioni
L'app per le funzioni offre un contesto di esecuzione per le funzioni. Di conseguenza, è l'unità di distribuzione e gestione per le funzioni. Un'app per le funzioni è costituita da una o più singole funzioni che vengono gestite, distribuite e ridimensionate insieme. Tutte le funzioni in un'app per le funzioni condividono lo stesso piano tariffario, il metodo di distribuzione e la versione del runtime. Un'app per le funzioni può essere considerata un modo per organizzare e gestire collettivamente le funzioni. Per altre informazioni, vedere [come gestire un'app](functions-how-to-use-azure-function-app-settings.md)per le funzioni. 

> [!NOTE]
> Tutte le funzioni in un'app per le funzioni devono essere create nello stesso linguaggio. Nelle [versioni precedenti](functions-versions.md) del runtime di Funzioni di Azure questo non è un requisito.

## <a name="folder-structure"></a>Struttura di cartelle
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

La struttura di cartelle mostrata sopra è quella predefinita (e consigliata) per un'app per le funzioni. Se si vuole modificare il percorso del file del codice di una funzione, modificare la sezione `scriptFile` del file _function.json_. È anche consigliabile usare la [distribuzione di pacchetti](deployment-zip-push.md) per distribuire il progetto nell'app per le funzioni in Azure. È anche possibile usare gli strumenti esistenti, ad esempio [integrazione e distribuzione continue](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Se si distribuisce un pacchetto manualmente, assicurarsi di distribuire il file _host.json_ e le cartelle della funzione direttamente nella cartella `wwwroot`. Non includere la cartella `wwwroot` nelle distribuzioni. In caso contrario, verranno create le cartelle `wwwroot\wwwroot`.

#### <a name="use-local-tools-and-publishing"></a>Usare strumenti e pubblicazione locali
È possibile creare e pubblicare app per le funzioni tramite vari strumenti, tra cui [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) e [Azure Functions Core Tools](./functions-develop-local.md). Per altre informazioni, vedere [Scrivere codice per Funzioni di Azure e testarle in locale](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Come modificare le funzioni nel portale di Azure
L'editor delle funzioni integrato nel portale di Azure permette di aggiornare il codice e il file *function.json* direttamente inline. Si tratta di un approccio consigliato solo per piccole modifiche o modelli di verifica, mentre la procedura consigliata consiste nell'usare uno strumento di sviluppo locale come Visual Studio Code.

## <a name="parallel-execution"></a>Esecuzione parallela
Quando si verificano rapidamente più eventi di trigger di quanti il runtime della funzione a thread singolo riesca a elaborare, il runtime chiama la funzione più volte in parallelo.  Se un'app per le funzioni usa il [piano di hosting a consumo](event-driven-scaling.md), il numero di istanze dell'app può aumentare automaticamente.  Ogni istanza dell'app per le funzioni, indipendentemente dal fatto che venga eseguita in un piano di hosting a consumo o in un normale [piano di hosting del servizio app](../app-service/overview-hosting-plans.md), può elaborare chiamate di funzione simultanee in parallelo usando più thread.  Il numero massimo di chiamate di funzione simultanee in ogni istanza dell'app per le funzioni dipende dal tipo di trigger usato e dalle risorse usate dalle altre funzioni nell'app per le funzioni.

## <a name="functions-runtime-versioning"></a>Controllo delle versioni del runtime di Funzioni

È possibile configurare la versione del runtime di Funzioni usando le impostazioni dell'app `FUNCTIONS_EXTENSION_VERSION`. Ad esempio, il valore "~ 3" indica che l'app per le funzioni userà 3. x come versione principale. Le app per le funzioni vengono aggiornate a ogni nuova versione secondaria appena rilasciate. Per altre informazioni, incluso come visualizzare la versione esatta dell'app per le funzioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

## <a name="repositories"></a>Repository
Il codice di Funzioni di Azure è open source e archiviato in repository GitHub:

* [Funzioni di Azure](https://github.com/Azure/Azure-Functions)
* [Host di funzioni di Azure](https://github.com/Azure/azure-functions-host/)
* [portale di Funzioni di Azure](https://github.com/azure/azure-functions-ux)
* [Modelli di Funzioni di Azure](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Estensioni di Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Associazioni
La tabella riportata di seguito elenca tutte le associazioni supportate.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

In caso di problemi con errori provenienti dalle associazioni, rivedere la documentazione [Azure Functions Binding Error Codes](functions-bindings-error-pages.md) (Codici degli errori di associazione di Funzioni di Azure).


## <a name="connections"></a>Connessioni

Il progetto di funzione fa riferimento alle informazioni di connessione in base al nome del provider di configurazione. Non accetta direttamente i dettagli della connessione, consentendo la modifica tra gli ambienti. Ad esempio, una definizione di trigger potrebbe includere una `connection` Proprietà. Questo può fare riferimento a una stringa di connessione, ma non è possibile impostare la stringa di connessione direttamente in un oggetto `function.json` . Al contrario, si imposta sul `connection` nome di una variabile di ambiente che contiene la stringa di connessione.

Il provider di configurazione predefinito usa le variabili di ambiente. Queste impostazioni possono essere impostate dalle [impostazioni dell'applicazione](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) durante l'esecuzione nel servizio funzioni di Azure o dal [file di impostazioni locale](functions-run-local.md#local-settings-file) quando si sviluppa in locale.

### <a name="connection-values"></a>Valori di connessione

Quando il nome della connessione viene risolto in un singolo valore esatto, il runtime identifica il valore come _stringa di connessione_, che in genere include un segreto. I dettagli di una stringa di connessione sono definiti dal servizio a cui si desidera connettersi.

Tuttavia, un nome di connessione può anche fare riferimento a una raccolta di più elementi di configurazione. Le variabili di ambiente possono essere considerate come una raccolta usando un prefisso condiviso che termina con un doppio carattere di sottolineatura `__` . Al gruppo è quindi possibile fare riferimento impostando il nome della connessione su questo prefisso.

Ad esempio, la `connection` proprietà per una definizione di trigger BLOB di Azure potrebbe essere `Storage1` . Se non esiste un singolo valore di stringa configurato con `Storage1` come nome, `Storage1__serviceUri` verrebbe utilizzato per la `serviceUri` proprietà della connessione. Le proprietà di connessione sono diverse per ogni servizio. Vedere la documentazione per l'estensione che usa la connessione.

### <a name="configure-an-identity-based-connection"></a>Configurare una connessione basata sull'identità

Alcune connessioni in funzioni di Azure sono configurate per l'uso di un'identità anziché di un segreto. Il supporto dipende dall'estensione che utilizza la connessione. In alcuni casi, una stringa di connessione potrebbe essere ancora necessaria nelle funzioni anche se il servizio a cui si è connessi supporta le connessioni basate sull'identità.

> [!IMPORTANT]
> Anche se un'estensione di binding supporta le connessioni basate su identità, la configurazione potrebbe non essere ancora supportata nel piano a consumo. Vedere la tabella di supporto riportata di seguito.

Le connessioni basate su identità sono supportate dal trigger e dalle estensioni di binding seguenti:

| Nome estensione | Versione dell'estensione                                                                                     | Supportato nel piano a consumo |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| BLOB Azure     | [Versione 5.0.0-beta1 o successiva](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Code di Azure    | [Versione 5.0.0-beta1 o successiva](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |
| Hub eventi di Azure    | [Versione 5.0.0-beta1 o successiva](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | No                                    |

> [!NOTE]
> Il supporto per le connessioni basate su identità non è ancora disponibile per le connessioni di archiviazione usate dal runtime di funzioni per i comportamenti di base. Ciò significa che l' `AzureWebJobsStorage` impostazione deve essere una stringa di connessione.

#### <a name="connection-properties"></a>Proprietà di connessione

Una connessione basata su identità per un servizio di Azure accetta le proprietà seguenti:

| Proprietà    | Obbligatorio per le estensioni | Variabile di ambiente | Descrizione |
|---|---|---|---|
| URI del servizio | BLOB di Azure, coda di Azure | `<CONNECTION_NAME_PREFIX>__serviceUri` |  URI del piano dati del servizio a cui si sta effettuando la connessione. |
| Spazio dei nomi completo | Hub eventi | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Spazio dei nomi completo dell'hub eventi. |

È possibile che siano supportate opzioni aggiuntive per un determinato tipo di connessione. Consultare la documentazione per il componente che effettua la connessione.

Quando sono ospitate nel servizio funzioni di Azure, le connessioni basate su identità usano un' [identità gestita](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). Per impostazione predefinita, viene utilizzata l'identità assegnata dal sistema. Quando vengono eseguiti in altri contesti, ad esempio lo sviluppo locale, viene invece usata l'identità dello sviluppatore, sebbene sia possibile personalizzarla usando parametri di connessione alternativi.

##### <a name="local-development"></a>Sviluppo locale

Durante l'esecuzione in locale, la configurazione precedente indica al runtime di usare l'identità dello sviluppatore locale. La connessione tenterà di ottenere un token dalle posizioni seguenti, nell'ordine:

- Una cache locale condivisa tra le applicazioni Microsoft
- Contesto utente corrente in Visual Studio
- Contesto utente corrente in Visual Studio Code
- Il contesto utente corrente nell'interfaccia della riga di comando di Azure

Se nessuna di queste opzioni ha esito positivo, si verificherà un errore.

In alcuni casi, è possibile specificare l'uso di un'identità diversa. È possibile aggiungere proprietà di configurazione per la connessione che puntano all'identità alternativa.

> [!NOTE]
> Le opzioni di configurazione seguenti non sono supportate quando sono ospitate nel servizio funzioni di Azure.

Per connettersi usando un'entità servizio Azure Active Directory con un ID client e un segreto, definire la connessione con le proprietà obbligatorie seguenti oltre alle [proprietà di connessione](#connection-properties) sopra indicate:

| Proprietà    | Variabile di ambiente | Descrizione |
|---|---|---|
| tenant_id | `<CONNECTION_NAME_PREFIX>__tenantId` | ID del tenant di Azure Active Directory (directory). |
| ID client | `<CONNECTION_NAME_PREFIX>__clientId` |  ID client (applicazione) di una registrazione dell'app nel tenant. |
| Segreto client | `<CONNECTION_NAME_PREFIX>__clientSecret` | Chiave privata client generata per la registrazione dell'app. |

Esempio di `local.settings.json` proprietà necessarie per la connessione basata sull'identità con il BLOB di Azure: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Concedere l'autorizzazione all'identità

Indipendentemente dall'identità utilizzata, è necessario disporre delle autorizzazioni per eseguire le azioni desiderate. Questa operazione viene in genere eseguita assegnando un ruolo in un controllo degli accessi in base al ruolo di Azure o specificando l'identità in un criterio di accesso, a seconda del servizio a cui ci si connette. Fare riferimento alla documentazione per ogni servizio sulle autorizzazioni necessarie e sul modo in cui possono essere impostate.

> [!IMPORTANT]
> Alcune autorizzazioni potrebbero essere esposte dal servizio che non sono necessarie per tutti i contesti. Laddove possibile, rispettare il **principio dei privilegi minimi**, concedendo all'identità solo i privilegi necessari. Ad esempio, se l'app deve semplicemente leggere da un BLOB, usare il ruolo [lettore dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) perché il [proprietario dei dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) include autorizzazioni eccessive per un'operazione di lettura.


## <a name="reporting-issues"></a>Segnalazione di problemi
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](./functions-develop-local.md)
* [Procedure consigliate per funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md)
* [Riferimenti per sviluppatori di funzioni di Azure Node.js](functions-reference-node.md)
