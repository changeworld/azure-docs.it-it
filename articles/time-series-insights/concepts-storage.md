---
title: Panoramica dell'archiviazione - Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sull'archiviazione dei dati in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725027"
---
# <a name="data-storage"></a>Archiviazione dati

Questo articolo descrive l'archiviazione dei dati in Azure Time Series Insights Gen2. Vengono illustrati il caldo e il freddo, la disponibilità dei dati e le procedure consigliate.

## <a name="provisioning"></a>Provisioning

Quando si crea un ambiente Azure Time Series Insights Gen2, sono disponibili le opzioni seguenti:

* Archiviazione dati a freddo:
  * Creare una nuova Archiviazione di Azure nella sottoscrizione e nell'area scelta per l'ambiente.
  * Collegare un account Archiviazione di Azure esistente. Questa opzione è disponibile solo tramite la [](/azure/templates/microsoft.timeseriesinsights/allversions)distribuzione da un modello Azure Resource Manager e non è visibile nel portale di Azure.
* Archiviazione a caldo dei dati:
  * Un warm store è facoltativo e può essere abilitato o disabilitato durante o dopo il provisioning. Se si decide di abilitare l'archivio a caldo in un [](concepts-storage.md#warm-store-behavior) secondo momento e nell'archivio a freddo sono già presenti dati, vedere questa sezione seguente per comprendere il comportamento previsto. Il tempo di conservazione dei dati dell'archivio ad accesso caldo può essere configurato per 7-31 giorni e può anche essere modificato in base alle esigenze.

Quando un evento viene ingerito, viene indicizzato sia nell'archivio a caldo (se abilitato) che nell'archivio a freddo.

[![Panoramica dell'archiviazione](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> In qualità di proprietario dell'account di Archiviazione BLOB di Azure in cui si trovano i dati dell'archivio ad accesso sporadico, l'utente ha completo accesso a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti Azure Time Series Insights Gen2 perché ciò può causare la perdita di dati.

## <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights partizioni di Gen2 e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventano disponibili per eseguire query sia dall'archivio ad accesso caldo (se abilitato) che dall'archivio a freddo dopo l'indicizzazione. La quantità di dati inseriti e la velocità effettiva per partizione possono influire sulla disponibilità. Esaminare le limitazioni di velocità effettiva [dell'origine evento](./concepts-streaming-ingress-throughput-limits.md) e [le procedure consigliate per](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) ottenere prestazioni ottimali. È anche possibile configurare un avviso di ritardo [per](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) ricevere una notifica se nell'ambiente si verificano problemi durante l'elaborazione dei dati.

> [!IMPORTANT]
> È possibile che si verifichi un periodo fino a 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite il portale di Azure.

## <a name="warm-store"></a>Warm Store

I dati nell'archivio ad accesso molto caldo sono disponibili solo tramite le API [di query di Time Series,](./concepts-query-overview.md)Azure Time Series Insights [TSI Explorer](./concepts-ux-panels.md)o il [connettore Power BI](./how-to-connect-power-bi.md). Le query dell'archivio ad accesso caldo sono gratuite e non è disponibile alcuna quota, ma esiste un [limite di 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) richieste simultanee.

### <a name="warm-store-behavior"></a>Comportamento dell'archivio ad caldo

* Se abilitata, tutti i dati trasmessi nell'ambiente verranno instradati all'archivio ad accesso caldo, indipendentemente dal timestamp dell'evento. Si noti che la pipeline di inserimento di streaming è compilata per lo streaming quasi in tempo reale e l'inserimento di eventi cronologici [non è supportato.](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)
* Il periodo di memorizzazione viene calcolato in base al momento in cui l'evento è stato indicizzato nell'archivio ad accesso caldo, non al timestamp dell'evento. Ciò significa che i dati non sono più disponibili nell'archivio ad accesso caldo dopo la scadenza del periodo di conservazione, anche se il timestamp dell'evento è per il futuro.
  * Esempio: un evento con previsioni meteo di 10 giorni viene inserimentato e indicizzato in un contenitore di archiviazione ad accesso caldo configurato con un periodo di conservazione di 7 giorni. Dopo sette giorni, la stima non è più accessibile in warm store, ma è possibile eseguire query da cold.
* Se si abilita l'archivio ad accesso caldo in un ambiente esistente in cui sono già stati indicizzati dati recenti nell'archiviazione a freddo, si noti che l'archivio ad accesso caldo non verrà riempito nuovamente con questi dati.
* Se è stato appena abilitato l'archivio ad accesso caldo e si verificano problemi durante la visualizzazione dei dati recenti in Esplora risorse, è possibile disattivare temporaneamente le query dell'archivio ad accesso caldo:

   [![Disabilitare le query ad caldo](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Archivio ad freddo

Questa sezione descrive i Archiviazione di Azure rilevanti per Azure Time Series Insights Gen2.

Per una descrizione completa dell'Archiviazione BLOB di Azure, vedere l'[introduzione ai BLOB di archiviazione ](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>L'account di archiviazione ad accesso freddo

Azure Time Series Insights Gen2 conserva fino a due copie di ogni evento nell'account Archiviazione di Azure servizio. Una copia archivia gli eventi ordinati in base al momento dell'inserimento, consentendo sempre l'accesso agli eventi in una sequenza temporale ordinata. Nel corso del tempo, Azure Time Series Insights Gen2 crea anche una copia ripartizionata dei dati per ottimizzare le query con prestazioni.

Tutti i dati vengono archiviati a tempo indeterminato nell'account Archiviazione di Azure account.

> [!WARNING]
> Non limitare l'accesso a Internet pubblico a un hub o a un'origine evento usata Time Series Insights la connessione necessaria verrà interrotta.

#### <a name="writing-and-editing-blobs"></a>Scrittura e modifica di BLOB

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati Azure Time Series Insights Gen2.

#### <a name="accessing-cold-store-data"></a>Accesso ai dati dell'archivio ad accesso a freddo

Oltre ad accedere ai dati dalle API [Azure Time Series Insights Explorer](./concepts-ux-panels.md) e Time [Series Query,](./concepts-query-overview.md)è anche possibile accedere ai dati direttamente dai file Parquet archiviati nell'archivio a freddo. Ad esempio, è possibile leggere, trasformare e pulire i dati in un notebook di Jupyter, e poi usarli per eseguire il training del modello di Azure Machine Learning nello stesso flusso di lavoro Spark.

Per accedere ai dati direttamente dall'account Archiviazione di Azure, è necessario l'accesso in lettura all'account usato per archiviare i dati Azure Time Series Insights Gen2. È quindi possibile leggere i dati selezionati in base all'ora di creazione del file parquet presente nella cartella `PT=Time` descritta di seguito nella sezione dedicata al [formato di file parquet](#parquet-file-format-and-folder-structure).  Per altre informazioni sull'abilitazione dell'accesso in lettura all'account di archiviazione, vedere [Gestire l'accesso alle risorse dell'account di archiviazione](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file Azure Time Series Insights Gen2. Gestire i dati correlati solo dall'Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura della cartelle

Parquet è un formato di file open source a colonne progettato per archiviazioni e prestazioni efficienti. Azure Time Series Insights Gen2 usa Parquet per abilitare le prestazioni delle query basate su ID Time Series su larga scala.

Per altre informazioni sul tipo di file parquet, vedere la [documentazione relativa a parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 archivia le copie dei dati nel modo seguente:

* La prima copia, quella iniziale, viene partizionata in base all'ora di inserimento e archivia i dati approssimativamente in ordine di arrivo. Questi dati si trovano nella cartella `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata viene raggruppata in base agli ID serie temporale e si trova nella cartella `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Il timestamp nei nomi dei BLOB nella cartella corrisponde all'ora di arrivo dei dati da Azure Time Series Insights Gen2 e non `PT=Time` al timestamp degli eventi.

I dati nella cartella `PT=TsId` verranno ottimizzati per la query nel tempo e non sono statici. Durante la ripartizionamento, alcuni eventi potrebbero essere presenti in più BLOB. Non è garantito che la denominazione dei BLOB in questa cartella rimanga invariata.

In generale, se è necessario accedere ai dati direttamente tramite file Parquet, usare la `PT=Time` cartella .  Le funzionalità future consentiranno un accesso efficiente alla `PT=TsId` cartella.

> [!NOTE]
>
> * `<YYYY>` esegue il mapping a una rappresentazione dell'anno a 4 cifre.
> * `<MM>` esegue il mapping a una rappresentazione del mese a 2 cifre.
> * `<YYYYMMDDHHMMSSfff>` esegue il mapping a una rappresentazione timestamp dell'anno a quattro cifre (`YYYY`), del mese a due cifre (`MM`), del giorno a due cifre (`DD`), dell'ora a due cifre (`HH`), dei minuti a due cifre (`MM`), dei secondi a due cifre (`SS`) e dei millisecondi a tre cifre (`fff`).

Azure Time Series Insights eventi di Gen2 vengono mappati al contenuto del file Parquet come indicato di seguito:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp dell'evento. La proprietà timestamp non ha mai un valore Null. Il valore predefinito è **l'ora di accodamento dell'evento** se la proprietà timestamp non è specificata nell'origine evento. Il timestamp archiviato è sempre in formato UTC.
* Ogni riga include le colonne Time Series ID (TSID) definite quando viene creato l'Azure Time Series Insights ambiente Gen2. Il nome della proprietà TSID include il suffisso `_string`.
* Tutte le altre proprietà inviate come dati di telemetria vengono mappate ai nomi di colonna che terminano con `_bool` (booleano), `_datetime` `_long` (timestamp), `_double` (long), (double), `_string` (stringa) o `dynamic` (dinamico), a seconda del tipo di proprietà.  Per altre informazioni, vedere Tipi [di dati supportati.](./concepts-supported-data-types.md)
* Questo schema di mapping si applica alla prima versione del formato di file, a cui si fa riferimento come **V=1,** e archiviato nella cartella di base con lo stesso nome. Con l'evolversi di questa funzionalità, lo schema di mapping potrebbe cambiare e il numero presente nel nome di riferimento potrebbe aumentare.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [modellazione dei dati.](./concepts-model-overview.md)

* Pianificare [l'Azure Time Series Insights Gen2.](./how-to-plan-your-environment.md)
