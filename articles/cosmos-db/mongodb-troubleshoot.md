---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per il database Mongo
description: Questo documento illustra i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: faf50899e5897a8f06cf0e24166abd303d24b491
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011392"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L'articolo seguente descrive gli errori comuni e le soluzioni per le distribuzioni usando l'API Azure Cosmos DB per MongoDB.

>[!Note]
> Azure Cosmos DB non ospita il motore MongoDB. Fornisce un'implementazione del protocollo wire di MongoDB. Alcuni di questi errori sono pertanto disponibili solo nell'API Azure Cosmos DB per MongoDB. 

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

| Codice       | Errore                | Descrizione  | Soluzione  |
|------------|----------------------|--------------|-----------|
| 2 | Il percorso di indice corrispondente all'elemento Order by specificato viene escluso o la query Order by non dispone di un indice composto corrispondente da cui può essere servito. | La query richiede un ordinamento in un campo non indicizzato. | Creare un indice o un indice composto corrispondente per la query di ordinamento da tentare. |
| 13 | Non autorizzata | La richiesta non dispone delle autorizzazioni necessarie per il completamento. | Assicurarsi di impostare le autorizzazioni appropriate per il database e la raccolta.  |
| 16 | InvalidLength | La lunghezza della richiesta specificata non è valida. | Se si usa la funzione explain (), assicurarsi di specificare una sola operazione. |
| 26 | NamespaceNotFound | Impossibile trovare il database o la raccolta a cui si fa riferimento nella query. | Verificare che il nome del database o della raccolta corrisponda esattamente al nome della query.|
| 50 | ExceededTimeLimit | L'esecuzione della richiesta ha superato il timeout di 60 secondi. |  Questo errore può essere dovuto a numerose cause. Una delle cause è quando la capacità delle unità richiesta attualmente allocate non è sufficiente per completare la richiesta. Questo problema può essere risolto aumentando le unità richiesta di tale raccolta o database. In altri casi, questo errore può essere risolto suddividendo una richiesta di grandi dimensioni in quelle più piccole.|
| 61 | ShardKeyNotFound | Il documento nella richiesta non contiene la chiave di partizione della raccolta (Azure Cosmos DB chiave di partizione). | Verificare che la chiave di partizione della raccolta sia utilizzata nella richiesta.|
| 66 | ImmutableField | La richiesta sta provando a modificare un campo non modificabile | i campi "ID" non sono modificabili. Assicurarsi che la richiesta non tenti di aggiornare quel campo. |
| 67 | CannotCreateIndex | Non è possibile completare la richiesta di creazione di un indice. | In un contenitore è possibile creare fino a 500 indici di campo singolo. È possibile includere fino a otto campi in un indice composto (gli indici composti sono supportati nella versione 3.6 +). |
| 115 | CommandNotSupported | Il tentativo di richiesta non è supportato. | Nell'errore è necessario fornire ulteriori dettagli. Se questa funzionalità è importante per le distribuzioni, segnalare il problema creando un ticket di supporto nella [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | La chiave di partizione (Azure Cosmos DB chiave di partizione) del documento che si sta inserendo esiste già nella raccolta oppure è stato violato un vincolo di campo indice univoco. | Utilizzare la funzione Update () per aggiornare un documento esistente. Se il vincolo di campo indice univoco è stato violato, inserire o aggiornare il documento con un valore di campo che non esiste ancora nella partizione/partizione. |
| 16500 | TooManyRequests  | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Valutare la possibilità di dimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure riprovare. Se si Abilita SSR (nuovo tentativo lato server), Azure Cosmos DB ritenta automaticamente le richieste non riuscite a causa di questo errore. |
| 16501 | ExceededMemoryLimit | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome della fase della pipeline non riconosciuto. | Il nome della fase nella richiesta della pipeline di aggregazione non è stato riconosciuto. | Verificare che tutti i nomi delle pipeline di aggregazione siano validi nella richiesta. |
| - | Problemi con la versione protocollo di collegamento di MongoDB | Le versioni precedenti dei driver MongoDB non sono in grado di rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Aggiungere *appname = @**AccountName** @* alla fine della stringa di connessione dell'API Cosmos DB per MongoDB, dove ***AccountName*** è il nome dell'account Cosmos DB. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
