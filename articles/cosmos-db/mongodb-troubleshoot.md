---
title: Risolvere gli errori comuni nell'API di Azure Cosmos DB per il database Mongo
description: Questo documento illustra i modi per risolvere i problemi comuni riscontrati nell'API di Azure Cosmos DB per MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692233"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Risolvere i problemi comuni nell'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L'articolo seguente descrive gli errori comuni e le soluzioni per le distribuzioni usando l'API Azure Cosmos DB per MongoDB.

>[!Note]
> Azure Cosmos DB non ospita il motore MongoDB. Fornisce un'implementazione della [versione del protocollo wire](mongodb-feature-support-40.md)di MongoDB 4,0, [3,6](mongodb-feature-support-36.md)e del supporto legacy per il [protocollo wire versione 3,2](mongodb-feature-support.md). Alcuni di questi errori sono pertanto disponibili solo nell'API Azure Cosmos DB per MongoDB.

## <a name="common-errors-and-solutions"></a>Errori comuni e soluzioni

| Codice       | Errore                | Descrizione  | Soluzione  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Una causa comune è che il percorso dell'indice corrispondente all'elemento Ordina per specificato è escluso oppure la query Ordina per non dispone di un indice composto corrispondente da cui può essere servita. La query richiede un ordinamento in un campo non indicizzato. | Creare un indice corrispondente o un indice composto per la query di ordinamento tentata. |
| 2 | Transazione non attiva | La transazione con documenti multipli superava il limite fissato di 5 secondi. | Ritentare la transazione con documenti multipli o limitare l'ambito delle operazioni all'interno della transazione stessa per completare il processo entro il limite di 5 secondi. |
| 13 | Non autorizzata | La richiesta non dispone delle autorizzazioni necessarie per il completamento. | Assicurarsi di usare le chiavi corrette.  |
| 26 | NamespaceNotFound | Non è possibile trovare il database o la raccolta a cui si fa riferimento nella query. | Verificare che il nome del database o della raccolta corrisponda esattamente al nome nella query.|
| 50 | ExceededTimeLimit | L'esecuzione della richiesta ha superato il timeout di 60 secondi. |  Le cause dell'errore possono essere molteplici. Una delle cause è che la capacità attualmente allocata per le unità richiesta non è sufficiente per completare la richiesta. Questo problema può essere risolto aumentando le unità richiesta di tale raccolta o database. In altri casi, l'errore può essere risolto suddividendo una richiesta di grandi dimensioni in richieste più piccole. La ripetizione del tentativo di esecuzione di un'operazione di scrittura che ha ricevuto questo errore può causare un'operazione di scrittura duplicata. <br><br>Se si sta tentando di eliminare grandi quantità di dati senza conseguenze sulle UR: <br>- Considerare l'uso di TTL (in base al timestamp): [Impostare la scadenza dei dati con l'API di Azure Cosmos DB per MongoDB](mongodb-time-to-live.md) <br>- Usare il cursore/le dimensioni del batch per eseguire l'eliminazione. È possibile recuperare un singolo documento alla volta ed eliminarlo tramite un ciclo. Questo consente di eliminare lentamente i dati senza influire sull'applicazione di produzione.|
| 61 | ShardKeyNotFound | Il documento nella richiesta non contiene la chiave di partizione della raccolta, ovvero la chiave di partizione di Azure Cosmos DB. | Verificare che la chiave di partizione della raccolta sia usata nella richiesta.|
| 66 | ImmutableField | La richiesta sta provando a modificare un campo non modificabile | i campi "_id" non sono modificabili. Assicurarsi che la richiesta non tenti di aggiornare quel campo o il campo della chiave di partizione. |
| 67 | CannotCreateIndex | Non è possibile completare la richiesta di creazione di un indice. | In un contenitore è possibile creare fino a 500 indici di campo singolo. In un indice composto è possibile includere fino a otto campi. Gli indici composti sono supportati nella versione 3.6 e nelle versioni successive. |
| 112 | WriteConflict | La transazione con documenti multipli non è riuscita a causa di una transazione con documenti multipli in conflitto | Ripetere la transazione con documenti multipli fino a quando non avrà esito positivo. |
| 115 | CommandNotSupported | La richiesta tentata non è supportata. | Nell'errore è necessario specificare altri dettagli. Se questa funzionalità è importante per le distribuzioni, creare un ticket di supporto nella [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e il team di Azure Cosmos DB tornerà all'utente. |
| 11000 | DuplicateKey | La chiave di partizione, ovvero la chiave di partizione di Azure Cosmos DB, del documento che si sta inserendo esiste già nella raccolta oppure è stato violato un vincolo di campo dell'indice univoco. | Utilizzare la funzione update() per aggiornare un documento esistente. Se il vincolo di campo dell'indice univoco è stato violato, inserire o aggiornare il documento con un valore di campo che non esiste ancora nella partizione. Un'altra opzione consiste nell'usare un campo contenente una combinazione dei campi dell'ID e della chiave di partizione. |
| 16500 | TooManyRequests  | Il numero totale di unità richiesta utilizzate è maggiore del livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Valutare la possibilità di dimensionare la velocità effettiva assegnata a un contenitore o a un set di contenitori dal portale di Azure oppure riprovare. Se si abilita il nuovo tentativo lato server, Azure Cosmos DB ritenta automaticamente le richieste non riuscite a causa di questo errore. |
| 16501 | ExceededMemoryLimit | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. Si applica solo all'API di Azure Cosmos DB per la versione 3.2 di MongoDB. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Esempio: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nome della fase di pipeline non riconosciuto. | Il nome della fase nella richiesta della pipeline di aggregazione non è stato riconosciuto. | Verificare che tutti i nomi delle pipeline di aggregazione siano validi nella richiesta. |
| - | Problemi con la versione protocollo di collegamento di MongoDB | Le versioni precedenti dei driver MongoDB non riescono a rilevare il nome dell'account Azure Cosmos nelle stringhe di connessione. | Aggiungere `appName=@accountName@` alla fine della stringa di connessione, dove `accountName` è il nome dell'account Azure Cosmos DB. |
| - | Problemi di rete del client MongoDB, ad esempio, eccezioni socket o endOfStream.| La richiesta di rete non è riuscita. Questo errore è spesso causato da una connessione TCP inattiva che il client MongoDB sta tentando di usare. I driver MongoDB spesso usano il pool di connessioni, che genera una connessione casuale scelta dal pool usato per una richiesta. In genere per le connessioni inattive si verifica un timeout sul lato Azure Cosmos DB dopo quattro minuti. | È possibile ritentare le richieste non riuscite nel codice dell'applicazione, modificare le impostazioni del client (driver) MongoDB per disinstallare le connessioni TCP inattive prima della finestra di timeout di quattro minuti oppure configurare le impostazioni `keepalive` del sistema operativo per mantenere le connessioni TCP in uno stato attivo.<br><br>Per evitare messaggi di connettività, è possibile modificare la stringa di connessione in modo da impostare `maxConnectionIdleTime` su 1-2 minuti.<br>- Driver di Mongo: configurare `maxIdleTimeMS=120000` <br>- Node.JS: configurare `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minuti
| - | La shell Mongo non funziona nel portale di Azure | Quando l'utente tenta di aprire una shell Mongo, l'operazione non viene eseguita e la scheda rimane vuota.  | Verificare il firewall. Il firewall non è supportato con la shell Mongo nel portale di Azure. <br>- Installare la shell Mongo nel computer locale all'interno delle regole del firewall <br>- Usare la shell Mongo legacy
| - | Non è possibile connettersi con la stringa di connessione  | La stringa di connessione è stata modificata durante l'aggiornamento dalla versione 3.2 alla versione 3.6 | Si noti che quando si usa l'API di Azure Cosmos DB per gli account MongoDB, la versione 3.6 degli account ha l'endpoint nel formato `*.mongo.cosmos.azure.com` mentre la versione 3.2 degli account ha l'endpoint nel formato `*.documents.azure.com`.  

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
