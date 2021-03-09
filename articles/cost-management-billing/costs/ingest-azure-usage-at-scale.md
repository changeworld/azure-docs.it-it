---
title: Recupero di set di impostazioni di grandi dimensioni con esportazioni frequenti
description: Questo articolo consente di esportare regolarmente grandi quantità di dati con esportazioni da gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509680"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recupero di set di impostazioni di grandi dimensioni con esportazioni frequenti

Questo articolo consente di esportare regolarmente grandi quantità di dati con esportazioni da gestione costi di Azure. L'esportazione è la modalità consigliata per recuperare i dati dei costi non aggregati. Soprattutto quando i file di utilizzo sono troppo grandi per essere chiamati e scaricati in modo affidabile usando l'API Dettagli utilizzo. I dati esportati vengono inseriti nell'account di archiviazione di Azure scelto. Da qui è possibile caricarli nei propri sistemi e analizzarli in base alle esigenze. Per configurare le esportazioni nel portale di Azure, vedere [Esportare dati](tutorial-export-acm-data.md).

Se si vogliono automatizzare le esportazioni in diversi ambiti, la richiesta di API di esempio nella sezione successiva è un buon punto di partenza. È possibile usare l'API Esportazioni per creare esportazioni automatiche nell'ambito della configurazione generale dell'ambiente. Le esportazioni automatiche consentono di ottenere i dati necessari. È possibile usarle nei sistemi della propria organizzazione man mano che si espande l'utilizzo di Azure.

## <a name="common-export-configurations"></a>Configurazioni di esportazione comuni

Prima di creare la prima esportazione, considerare lo scenario e le opzioni di configurazione necessarie per abilitarlo. Considerare le opzioni di esportazione seguenti:

- **Ricorrenza**: determina la frequenza con cui viene eseguito il processo di esportazione e quando si inserisce un file nell'account di archiviazione di Azure. Scegliere tra Ogni giorno, Ogni settimana e Ogni mese. Provare a configurare la ricorrenza in modo che corrisponda a quella dei processi di importazione dei dati usati dal sistema interno dell'organizzazione.
- **Periodo di ricorrenza**: determina per quanto tempo rimane valida l'esportazione. I file vengono esportati solo durante il periodo di ricorrenza.
- **Intervallo di tempo**: determina la quantità di dati generati dall'esportazione in una determinata esecuzione. Le opzioni comuni sono MonthToDate e WeekToDate.
- **StartDate**: configura quando avviare la pianificazione dell'esportazione. L'esportazione viene creata in base alla data di inizio indicata in StartDate e successivamente in base alla ricorrenza.
- **Tipo**: sono disponibili tre tipi di esportazione:
  - ActualCost: mostra l'utilizzo totale e i costi per il periodo specificato, man mano che vengono accumulati e visualizzati nella fattura.
  - AmortizedCost: mostra l'utilizzo totale e i costi per il periodo specificato, con ammortamento applicato ai costi di acquisto della prenotazione applicabili.
  - Usage: tutte le esportazioni create prima del 20 luglio 2020 sono di tipo Usage. Aggiornare tutte le esportazioni pianificate come ActualCost o AmortizedCost.
- **Colonne**: definisce i campi dati da includere nel file di esportazione. Corrispondono ai campi disponibili nell'API Dettagli utilizzo. Per altre informazioni, vedere [API Dettagli utilizzo](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Creare un'esportazione giornaliera da inizio mese per una sottoscrizione

URL richiesta: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Copiare BLOB di archiviazione di Azure di grandi dimensioni

È possibile usare gestione costi per pianificare esportazioni dei dettagli sull'utilizzo di Azure negli account di archiviazione di Azure come BLOB. Le dimensioni dei BLOB risultanti possono avere una dimensione superiore a Gigabyte. Il team di gestione costi di Azure ha collaborato con il team di archiviazione di Azure per testare la copia di BLOB di archiviazione di Azure. I risultati sono descritti nelle sezioni riportate di seguito. È possibile che si verifichino risultati simili quando si copiano i BLOB di archiviazione da un'area di Azure a un'altra.

Per testare le prestazioni, il team ha trasferito i BLOB dagli account di archiviazione nell'area Stati Uniti occidentali alla stessa e ad altre aree. Il team ha misurato velocità comprese tra 2 GB al secondo nella stessa area e 150 MB al secondo per gli account di archiviazione nell'area di Asia orientale meridionale.

### <a name="test-configuration"></a>Configurazione di test

Per misurare le velocità di trasferimento dei BLOB, il team ha creato una semplice applicazione console .NET che fa riferimento alla versione più recente (v 2.0.1) di Azure Data Movement Library (DLM) tramite NuGet. DLM è un SDK fornito dal team di archiviazione di Azure che facilita l'accesso a livello di codice ai servizi di trasferimento. Hanno quindi creato gli account di archiviazione standard v2 in più aree e usano gli Stati Uniti occidentali come area di origine. Gli account di archiviazione sono stati popolati con i contenitori, dove ognuno contiene i BLOB in blocchi da 10 2 GB. I contenitori sono stati copiati in altri account di archiviazione usando il metodo _TransferManager. CopyDirectoryAsync ()_ di DLM con l'opzione _CopyMethod. ServiceSideSyncCopy_ . I test sono stati eseguiti in un computer che esegue Windows 10 con 12 core e una rete da 1 GbE.

Impostazioni applicazione utilizzate:

- _TransferManager.Configurations._  =  _Ambiente ParallelOperations. ProcessorCount \* 32_. Il team ha trovato l'impostazione per avere la massima efficacia sulla velocità effettiva complessiva. Il valore 32 volte il numero di core fornito la velocità effettiva migliore per il client di test.
- _ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Se viene impostato su un valore massimo, il controllo completo del parallelismo di trasferimento viene passato all'impostazione _ParallelOperations_ riportata sopra.
- _TransferManager.Configurations. BlockSize = 4.194.304_. Aveva un effetto sulle velocità di trasferimento con 4 MB, dimostrando di essere ottimale per i test.

Per ulteriori informazioni e codice di esempio, vedere i collegamenti nella sezione [passaggi successivi](#next-steps) .

### <a name="test-results"></a>Risultati dei test

| **Numero di test** | **Area geografica** | **BLOB** | **Tempo (sec)** | **MB/s** | **Commenti** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2.000 |   |
| 2 | Stati Uniti occidentali 2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 trasferimenti paralleli con 8 account di archiviazione: da 4 ad ovest a 4, media est per trasferimento |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 4 trasferimenti paralleli da 1 account di archiviazione a un altro |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 8 trasferimenti paralleli con 8 account di archiviazione: 4 da ovest a 4x2 est medio per trasferimento |
| 7 | Sud-est asiatico | 2 GB x 10 | 133 | 150 |   |
| 8 | Sud-est asiatico | 2 GB x 10 x 4 | 444 | 180 | 4 trasferimenti paralleli da 1 account di archiviazione a un altro |

### <a name="sync-transfer-characteristics"></a>Caratteristiche del trasferimento di sincronizzazione

Di seguito sono riportate alcune delle caratteristiche del trasferimento di sincronizzazione sul lato servizio utilizzato con DML rilevanti per l'utilizzo:

- DML può trasferire un singolo BLOB o una directory. Per il trasferimento di directory è possibile usare un criterio di ricerca per trovare la corrispondenza con il prefisso del BLOB.
- I trasferimenti di BLOB in blocchi vengono eseguiti in parallelo. Tutte completate fino alla fine del processo di trasferimento. I singoli blocchi BLOB vengono trasferiti in parallelo.
- Il trasferimento viene eseguito in modo asincrono nel client. Lo stato di trasferimento è periodicamente disponibile tramite un callback a un metodo che può essere definito in un oggetto _TransferContext_ .
- Il trasferimento Crea checkpoint durante lo stato di avanzamento ed espone un oggetto _TransferCheckpoint_ . L'oggetto rappresenta il checkpoint più recente tramite l'oggetto _TransferContext_ . Se il _TransferCheckpoint_ viene salvato prima che il trasferimento venga annullato o interrotto, il trasferimento può essere ripreso dal Checkpoint per un massimo di sette giorni. Il trasferimento può essere ripreso da qualsiasi Checkpoint, non solo dal più recente.
- Se il processo del client di trasferimento viene terminato e riavviato senza implementare la funzionalità di checkpoint.
  - Prima che siano stati completati i trasferimenti di BLOB, il trasferimento viene riavviato.
  - Una volta completati alcuni BLOB, il trasferimento viene riavviato solo per i BLOB non completati.
- La sospensione dell'esecuzione del client sospende i trasferimenti.
- La funzionalità di trasferimento BLOB astrae il client da errori temporanei. Ad esempio, la limitazione dell'account di archiviazione non genererà un errore di trasferimento ma rallenterà il trasferimento.
- I trasferimenti sul lato servizio hanno un basso utilizzo delle risorse client per la CPU e la memoria, la larghezza di banda di rete e le connessioni.

### <a name="async-transfer-characteristics"></a>Caratteristiche del trasferimento asincrono

È possibile richiamare il metodo _TransferManager. CopyDirectoryAsync ()_ con l'opzione _CopyMethod. ServiceSideAsyncCopy_ . Funziona in modo analogo al meccanismo di trasferimento della sincronizzazione dal punto di vista del client, ma con le seguenti differenze di funzionamento:

- Le velocità di trasferimento sono molto più lente rispetto al trasferimento di sincronizzazione equivalente (in genere di 10 MB/s o meno).
- Il trasferimento continua anche se il processo client viene terminato.
- Sebbene i checkpoint siano supportati, la ripresa di un trasferimento tramite un _TransferCheckpoint_ non riprende in corrispondenza dell'ora del checkpoint, ma nello stato corrente del trasferimento.

### <a name="test-summary"></a>Riepilogo test

Archiviazione BLOB di Azure supporta velocità di trasferimento globali elevate con la funzionalità di trasferimento della sincronizzazione sul lato servizio. Usare la funzionalità nelle applicazioni .NET è semplice usando la libreria per lo spostamento dei dati. È possibile che le esportazioni di gestione costi consentano di copiare in modo affidabile centinaia di gigabyte di dati in un account di archiviazione in un punto qualsiasi in meno di un'ora.

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'origine della libreria per lo [spostamento dei dati archiviazione di Microsoft Azure](https://github.com/Azure/azure-storage-net-data-movement) .
- [Trasferire i dati con la libreria per lo spostamento dei dati](../../storage/common/storage-use-data-movement-library.md).
- Vedere l'esempio di origine [dell'applicazione di esempio AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup) .
- Leggi [la velocità effettiva elevata con l'archiviazione BLOB di Azure](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).