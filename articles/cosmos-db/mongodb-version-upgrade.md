---
title: Aggiornare la versione di Mongo dell'API Azure Cosmos DB per l'account MongoDB
description: Come aggiornare facilmente la versione del protocollo di rete MongoDB per l'API Azure Cosmos DB esistente per gli account MongoDB
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771813"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Aggiornare la versione dell'API dell'API Azure Cosmos DB per l'account MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Questo articolo descrive come aggiornare la versione dell'API dell'API del Azure Cosmos DB per l'account MongoDB. Dopo l'aggiornamento, è possibile usare le funzionalità più recenti nell'API Azure Cosmos DB per MongoDB. Il processo di aggiornamento non interrompe la disponibilità dell'account e non utilizza ur/s né diminuisce la capacità del database in qualsiasi momento. Questo processo non influirà sui dati o sugli indici esistenti. 

Quando si esegue l'aggiornamento a una nuova versione dell'API, iniziare con carichi di lavoro di sviluppo/test prima di aggiornare i carichi di lavoro di produzione. È importante aggiornare i client a una versione compatibile con la versione dell'API a cui si sta eseguendo l'aggiornamento prima di aggiornare il Azure Cosmos DB API per l'account MongoDB.

>[!Note]
> Al momento, solo gli account validi che usano la versione 3,2 del server possono essere aggiornati alla versione 3,6 o 4,0. Se l'account non Visualizza l'opzione di aggiornamento, inviare [un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Aggiornamento alla versione 4.0 o 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Vantaggi dell'aggiornamento alla versione 4.0

Di seguito sono riportate le nuove funzionalità incluse nella versione 4,0:
- Supporto per le transazioni a più documenti all'interno di raccolte non partizionate.
- Nuovi operatori di aggregazione
- Prestazioni di scansioni migliorate
- Archiviazione più efficiente e veloce

### <a name="benefits-of-upgrading-to-version-36"></a>Vantaggi dell'aggiornamento alla versione 3.6

Di seguito sono riportate le nuove funzionalità incluse nella versione 3,6:
- Prestazioni e stabilità migliorate
- Supporto per i nuovi comandi di database
- Supporto per la pipeline di aggregazione per impostazione predefinita e le nuove fasi di aggregazione
- Supporto per la modifica dei flussi
- Supporto per gli indici composti
- Supporto tra partizioni per le operazioni seguenti: aggiornamento, eliminazione, calcolo e ordinamento
- Miglioramento delle prestazioni per le operazioni di aggregazione seguenti: $count, $skip, $limit and $group
- L'indicizzazione con caratteri jolly è ora supportata

### <a name="changes-from-version-32"></a>Modifiche rispetto alla versione 3.2

- Per impostazione predefinita, la funzionalità di [ripetizione del lato server](prevent-rate-limiting-errors.md) è abilitata, in modo che le richieste provenienti dall'applicazione client non restituiscano errori 16500. Al contrario, le richieste verranno riavviate fino al completamento o al raggiungimento di 60 secondi di timeout.
- Il timeout configurato per richiesta è di 60 secondi.
- Per impostazione predefinita, nelle nuove raccolte MongoDB create nella nuova versione del protocollo di trasmissione sarà indicizzata solo la proprietà `_id`.

### <a name="action-required-when-upgrading-from-32"></a>Azione richiesta in caso di aggiornamento dalla versione 3.2

Quando si esegue l'aggiornamento dalla versione 3.2, il suffisso dell'endpoint per l'account del database verrà aggiornato nel formato seguente:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Se si esegue l'aggiornamento dalla versione 3,2, è necessario sostituire l'endpoint esistente nelle applicazioni e nei driver che si connettono a questo account di database. **Solo le connessioni che usano il nuovo endpoint avranno accesso alle funzionalità nella nuova versione dell'API**. Il suffisso per l'endpoint 3.2 precedente deve essere `.documents.azure.com`.

>[!Note]
> Questo endpoint potrebbe avere piccole differenze se l'account è stato creato in un cloud di Azure sovrano, governativo o limitato.

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

1. Accedere al [portale di Azure.](https://portal.azure.com/)

1. Passare all'API Azure Cosmos DB per l'account MongoDB. Aprire il riquadro **introduttivo** e verificare che la **versione** corrente del Server sia 3,2 o 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Controllare la versione corrente dell'account MongoDB dal portale di Azure." border="true":::

1. Dal menu a sinistra aprire il `Features` riquadro. Questo riquadro Mostra le funzionalità a livello di account disponibili per l'account di database.

1. Selezionare la riga `Upgrade MongoDB server version`. Se questa opzione non è presente, è possibile che l'account non sia idoneo per l'aggiornamento. Se questo è il caso, inviare [un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Aprire il pannello funzionalità e aggiornare l'account." border="true":::

1. Esaminare le informazioni visualizzate sull'aggiornamento. Selezionare `Set server version to 4.0` (o 3,6 in base alla versione corrente).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Esaminare le linee guida per l'aggiornamento e selezionare Aggiorna." border="true":::

1. Dopo aver avviato l'aggiornamento, il menu **funzionalità** viene disattivato e lo stato è impostato su *in sospeso*. Per il completamento dell'aggiornamento sono necessari circa 15 minuti. Questo processo non influirà sulle funzionalità o sulle operazioni esistenti dell'account di database. Al termine, lo stato della **versione dell'aggiornamento MongoDB server** visualizzerà la versione aggiornata. Se si è verificato un problema durante l'elaborazione della richiesta, [contattare il supporto tecnico](https://azure.microsoft.com/en-us/support/create-ticket/) .

1. Di seguito sono riportate alcune considerazioni dopo l'aggiornamento dell'account:

    1. Se è stato eseguito l'aggiornamento da 3,2, tornare al riquadro **Panoramica** e copiare la nuova stringa di connessione da usare nell'applicazione. La stringa di connessione precedente che esegue la versione 3.2 non verrà interrotta. Per garantire un'esperienza coerente, è necessario che tutte le applicazioni usino il nuovo endpoint.

    1. Se è stato eseguito l'aggiornamento dalla versione 3.6, la stringa di connessione esistente verrà aggiornata alla versione specificata e verrà ancora usata.

## <a name="how-to-downgrade"></a>Come effettuare il downgrade

È anche possibile effettuare il downgrade dell'account da 4,0 a 3,6 usando la stessa procedura descritta nella sezione "come eseguire l'aggiornamento".

Se è stato eseguito l'aggiornamento da 3,2 a (4,0 o 3,6) e si vuole eseguire il downgrade a 3,2, è sufficiente tornare a usare la stringa di connessione precedente (3,2) con l'host `accountname.documents.azure.com` che rimane attivo dopo l'aggiornamento che esegue la versione 3,2.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [funzionalità supportate e non della versione 4.0 di MongoDB](mongodb-feature-support-40.md).
- Informazioni sulle [funzionalità supportate e non della versione 3.6 di MongoDB](mongodb-feature-support-36.md).
- Per altre informazioni, vedere le [funzionalità della versione Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
