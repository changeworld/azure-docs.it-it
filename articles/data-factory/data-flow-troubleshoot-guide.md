---
title: Risolvere i problemi di mapping dei flussi di dati
description: Informazioni su come risolvere problemi relativi ai flussi di dati in Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523006"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi al mapping dei flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi relativi al mapping dei flussi di dati in Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Messaggi e codici di errore comuni 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Codice errore: DF-Executor-SourceInvalidPayload
- **Messaggio**: data preview, debug, and pipeline data flow execution failed because container does not exist (esecuzione del flusso di dati di anteprima, debug e pipeline non riuscita. Il contenitore non esiste)
- **Cause**: il set di dati contiene un contenitore che non esiste nella risorsa di archiviazione
- **Raccomandazione**: verificare che il contenitore a cui si fa riferimento nel set di dati esista o sia accessibile.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice errore: DF-Executor-SystemImplicitCartesian

- **Messaggio**: implicit cartesian product for INNER join is not supported, use CROSS JOIN instead (il prodotto cartesiano implicito per INNER JOIN non è supportato. Usare CROSS JOIN). Le colonne usate nel join devono creare una chiave univoca per le righe.
- **Cause**: il prodotto cartesiano implicito per INNER JOIN tra i piani logici non è supportato. Se le colonne vengono utilizzate nel join, è necessaria la chiave univoca con almeno una colonna da entrambi i lati della relazione.
- **Raccomandazione**: per i join basati sulla non uguaglianza è necessario scegliere un CROSS JOIN personalizzato.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice errore: DF-Executor-SystemInvalidJson

- **Messaggio**: JSON parsing error, unsupported encoding or multiline (errore di analisi JSON, codifica non supportata o su più righe)
- **Cause**: possibili problemi con il file JSON: codifica non supportata, byte danneggiati o uso dell'origine JSON come documento singolo su molte righe annidate
- **Raccomandazione**: verificare che la codifica del file JSON sia supportata. Nella trasformazione Source (Origine) che usa un set di dati JSON, espandere "JSON Settings" (Impostazioni JSON) e attivare "Single Document" (Documento singolo).
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Codice errore: DF-Executor-BroadcastTimeout

- **Messaggio**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (errore di timeout del join di trasmissione. Assicurarsi che il flusso di trasmissione generi i dati entro 60 secondi nelle esecuzioni di debug ed entro 300 secondi nelle esecuzioni di processi)
- **Cause**: la trasmissione ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e di 300 secondi durante l'esecuzione del processo. Il flusso scelto per la trasmissione sembra troppo grande per produrre dati entro questo limite.
- **Raccomandazione**: selezionare la scheda Optimize (Ottimizza) per le trasformazioni del flusso di dati per Join, Exists (Esistente) e Lookup (Ricerca). L'opzione predefinita per la trasmissione è "Auto". Se è impostato "auto" o se si imposta manualmente il lato sinistro o destro su broadcast in "Fixed", è possibile impostare una configurazione di Azure Integration Runtime più grande o disattivare broadcast. L'approccio consigliato per ottenere prestazioni ottimali nei flussi di dati consiste nel consentire a Spark di eseguire la trasmissione tramite l'opzione "Auto" e usare Azure IR ottimizzato per la memoria. Se si esegue il flusso di dati in un'esecuzione di test di debug da un'esecuzione di pipeline di debug, è possibile che si verifichi questa condizione con maggiore frequenza. Questo perché ADF limita il timeout di trasmissione a 60 secondi per mantenere un'esperienza di debug più veloce. Se si desidera estendere il valore al timeout di 300 secondi da un'esecuzione attivata, è possibile utilizzare l'opzione debug > utilizza Runtime attività per utilizzare le Azure IR definite nell'attività Esegui pipeline flusso di dati.

- **Messaggio**: errore di timeout di broadcast join. per evitare questo problema, è possibile scegliere ' off ' dell'opzione broadcast in join/exists/Lookup Transformation. Se si intende trasmettere l'opzione di join per migliorare le prestazioni, assicurarsi che Stream Broadcast possa produrre dati entro 60 secondi nelle esecuzioni di debug e 300 secondi nelle esecuzioni del processo.
- **Cause**: la trasmissione ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e di 300 secondi nelle esecuzioni di processi. In broadcast join il flusso scelto per la trasmissione sembra troppo grande per produrre dati entro questo limite. Se non viene usato un join broadcast, la trasmissione predefinita eseguita dal flusso di lavoro può raggiungere lo stesso limite
- **Consiglio**: disattivare l'opzione di trasmissione o evitare di trasmettere flussi di dati di grandi dimensioni in cui l'elaborazione può richiedere più di 60 secondi. Scegliere invece un flusso più piccolo da trasmettere. Le tabelle SQL/DW di grandi dimensioni e i file di origine sono in genere candidati non validi. In assenza di un join broadcast, utilizzare un cluster di dimensioni maggiori se si verifica l'errore.

### <a name="error-code-df-executor-conversion"></a>Codice errore: DF-Executor-Conversion

- **Messaggio**: converting to a date or time failed due to an invalid character (conversione in una data o ora non riuscita a causa di un carattere non valido)
- **Cause**: i dati non sono nel formato previsto
- **Raccomandazione**: usare il tipo di dati corretto

### <a name="error-code-df-executor-invalidcolumn"></a>Codice errore: DF-Executor-InvalidColumn
- **Messaggio**: column name needs to be specified in the query, set an alias if using a SQL function (specificare il nome della colonna nella query, impostare un alias se si usa una funzione SQL)
- **Cause**: non è stato specificato un nome per la colonna
- **Raccomandazione**: impostare un alias se si usa una funzione SQL, ad esempio min()/max() e così via.

### <a name="error-code-df-executor-drivererror"></a>Codice di errore: DF-Executor-DriverError
- **Messaggio**: INT96 è di tipo timestamp legacy che non è supportato dal flusso di file ADF. Si consiglia di aggiornare il tipo di colonna ai tipi più recenti.
- **Cause**: errore del driver
- **Raccomandazione**: INT96 è di tipo timestamp legacy, che non è supportato dal flusso di file ADF. Provare ad aggiornare il tipo di colonna ai tipi più recenti.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Codice di errore: DF-Executor-BlockCountExceedsLimitError
- **Messaggio**: il conteggio dei blocchi di cui non è stato eseguito il commit non può superare il limite massimo di 100.000 blocchi. Controllare la configurazione del BLOB.
- **Cause**: possono essere presenti al massimo 100.000 blocchi di cui non è stato eseguito il commit in un BLOB.
- **Consiglio**: per informazioni dettagliate, contattare il team del prodotto Microsoft in merito a questo problema

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Codice di errore: DF-Executor-PartitionDirectoryError
- **Messaggio**: il percorso di origine specificato ha più directory partizionate, ad esempio <Source Path> /<directory radice della partizione 1>/a = 10/b = 20, <Source Path> /<directory radice partizione 2>/c = 10/d = 30) o directory partizionata con altro file o directory non partizionata (ad esempio <Source Path> /<directory radice partizione 1>/a = 10/b = 20, <Source Path> /Directory 2/file1), rimuovere la directory radice della partizione dal percorso di origine e leggerla tramite una trasformazione di origine separata.
- **Cause**: il percorso di origine contiene più directory partizionate o una directory partizionata con altro file o directory non partizionata.
- **Raccomandazione**: rimuovere la directory radice partizionata dal percorso di origine e leggerla tramite una trasformazione di origine separata.

### <a name="error-code-df-executor-outofmemoryerror"></a>Codice di errore: DF-Executor-OutOfMemoryError
- **Messaggio**: si è verificata un problema di memoria insufficiente durante l'esecuzione. riprovare a usare un runtime di integrazione con un numero di core maggiore e/o un tipo di calcolo con ottimizzazione per la memoria
- **Cause**: memoria insufficiente per il cluster
- **Raccomandazione**: i cluster di debug sono destinati a scopi di sviluppo. Sfruttare il campionamento dei dati, il tipo di calcolo appropriato e le dimensioni per eseguire il payload. Per ottenere prestazioni ottimali, vedere la [Guida alle prestazioni del flusso di dati di mapping](concepts-data-flow-performance.md) per l'ottimizzazione.

### <a name="error-code-df-executor-invalidtype"></a>Codice di errore: DF-Executor-InvalidType
- **Messaggio**: assicurarsi che il tipo di parametro corrisponda al tipo di valore passato. Il passaggio di parametri float dalle pipeline non è attualmente supportato.
- **Cause**: tipi di dati non compatibili tra il tipo dichiarato e il valore del parametro effettivo
- **Raccomandazione**: verificare che i valori dei parametri passati in un flusso di dati corrispondano al tipo dichiarato.

### <a name="error-code-df-executor-columnunavailable"></a>Codice di errore: DF-Executor-ColumnUnavailable
- **Messaggio**: il nome della colonna utilizzato nell'espressione non è disponibile o non è valido
- **Cause**: nome di colonna non valido o non disponibile usato nelle espressioni
- **Raccomandazione**: controllare i nomi di colonna usati nelle espressioni

### <a name="error-code-df-executor-parseerror"></a>Codice di errore: DF-Executor-ParseError
- **Messaggio**: non è possibile analizzare l'espressione
- **Cause**: l'espressione presenta errori di analisi dovuti alla formattazione
- **Raccomandazione**: controllare la formattazione nell'espressione.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice errore: DF-Executor-SystemImplicitCartesian
- **Messaggio**: implicit cartesian product for INNER join is not supported, use CROSS JOIN instead (il prodotto cartesiano implicito per INNER JOIN non è supportato. Usare CROSS JOIN). Le colonne usate nel join devono creare una chiave univoca per le righe.
- **Cause**: il prodotto cartesiano implicito per INNER JOIN tra i piani logici non è supportato. Se le colonne utilizzate nel join creano la chiave univoca
- **Raccomandazione**: per i join non basati sull'uguaglianza è necessario optare per cross join.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice errore: DF-Executor-SystemInvalidJson
- **Messaggio**: JSON parsing error, unsupported encoding or multiline (errore di analisi JSON, codifica non supportata o su più righe)
- **Cause**: possibili problemi con il file JSON: codifica non supportata, byte danneggiati o uso dell'origine JSON come documento singolo su molte righe annidate
- **Raccomandazione**: verificare che la codifica del file JSON sia supportata. Nella trasformazione Source (Origine) che usa un set di dati JSON, espandere "JSON Settings" (Impostazioni JSON) e attivare "Single Document" (Documento singolo).



### <a name="error-code-df-executor-conversion"></a>Codice errore: DF-Executor-Conversion
- **Messaggio**: converting to a date or time failed due to an invalid character (conversione in una data o ora non riuscita a causa di un carattere non valido)
- **Cause**: i dati non sono nel formato previsto
- **Raccomandazione**: usare il tipo di dati corretto.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Codice di errore: DF-Executor-BlockCountExceedsLimitError
- **Messaggio**: il conteggio dei blocchi di cui non è stato eseguito il commit non può superare il limite massimo di 100.000 blocchi. Controllare la configurazione del BLOB.
- **Cause**: possono essere presenti al massimo 100.000 blocchi di cui non è stato eseguito il commit in un BLOB.
- **Raccomandazione**: per altri dettagli, contattare il team del prodotto Microsoft per informazioni su questo problema.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Codice di errore: DF-Executor-PartitionDirectoryError
- **Messaggio**: il percorso di origine specificato ha più directory partizionate, ad esempio *<Source Path> /<directory radice della partizione 1>/a = 10/b = 20, <Source Path> /<directory radice partizione 2>/c = 10/d = 30*) o directory partizionata con altro file o directory non partizionata (ad esempio *<Source Path> /<directory radice partizione 1>/A = 10/b = 20, <Source Path> /Directory 2/file1*), rimuovere la directory radice della partizione dal percorso di origine e leggerla tramite una trasformazione di origine separata.
- **Cause**: il percorso di origine contiene più directory partizionate o una directory partizionata con altro file o directory non partizionata.
- **Raccomandazione**: rimuovere la directory radice partizionata dal percorso di origine e leggerla tramite una trasformazione di origine separata.

### <a name="error-code-getcommand-outputasync-failed"></a>Codice errore: GetCommand OutputAsync failed
- **Messaggio**: durante il debug di Flusso di dati e l'anteprima dei dati: GetCommand OutputAsync failed with ... (l'operazione GetCommand OutputAsync non è riuscita con ...)
- **Cause**: si tratta di un errore del servizio back-end. È possibile ripetere l'operazione e anche riavviare la sessione di debug.
- **Raccomandazione**: se la ripetizione dell'operazione e il riavvio non risolvono il problema, contattare il supporto tecnico. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Codice di errore: DF-Executor-OutOfMemoryError
 
- **Messaggio**: si è verificata un problema di memoria insufficiente durante l'esecuzione. riprovare a usare un runtime di integrazione con un numero di core maggiore e/o un tipo di calcolo con ottimizzazione per la memoria
- **Cause**: memoria insufficiente per il cluster.
- **Raccomandazione**: i cluster di debug sono destinati a scopi di sviluppo. Utilizzare il tipo di calcolo e le dimensioni appropriati per il campionamento dei dati per eseguire il payload. Per ottimizzare le prestazioni, vedere la [Guida alle prestazioni del flusso di risultati](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) per ottimizzare i flussi di data.

### <a name="error-code-df-executor-illegalargument"></a>Codice di errore: DF-Executor-illegalArgument
- **Messaggio**: assicurarsi che la chiave di accesso nel servizio collegato sia corretta.
- **Cause**: il nome account o la chiave di accesso non è corretta.
- **Consiglio**: specificare il nome dell'account o la chiave di accesso corretti.

- **Messaggio**: assicurarsi che la chiave di accesso nel servizio collegato sia corretta
- **Cause**: il nome dell'account o la chiave di accesso non è corretta
- **Raccomandazione**: assicurarsi che il nome dell'account o la chiave di accesso specificata nel servizio collegato sia corretta. 

### <a name="error-code-df-executor-invalidtype"></a>Codice di errore: DF-Executor-InvalidType
- **Messaggio**: assicurarsi che il tipo di parametro corrisponda al tipo di valore passato. Il passaggio di parametri float dalle pipeline non è attualmente supportato.
- **Cause**: tipi di dati non compatibili tra il tipo dichiarato e il valore del parametro effettivo
- **Raccomandazione**: specificare i tipi di dati corretti.

### <a name="error-code-df-executor-columnunavailable"></a>Codice di errore: DF-Executor-ColumnUnavailable
- **Messaggio**: il nome della colonna utilizzato nell'espressione non è disponibile o non è valido.
- **Cause**: il nome di colonna non valido o non disponibile viene utilizzato nelle espressioni.
- **Raccomandazione**: controllare i nomi di colonna usati nelle espressioni.


### <a name="error-code-df-executor-parseerror"></a>Codice di errore: DF-Executor-ParseError
- **Messaggio**: Impossibile analizzare l'espressione.
- **Cause**: l'espressione presenta errori di analisi dovuti alla formattazione.
- **Raccomandazione**: controllare la formattazione nell'espressione.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Codice di errore: DF-Executor-OutOfDiskSpaceError
- **Messaggio**: errore interno del server
- **Cause**: lo spazio su disco del cluster è insufficiente.
- **Raccomandazione**: ripetere la pipeline. Se il problema persiste, contattare il supporto tecnico.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Codice di errore: DF-Executor-StoreIsNotDefined
- **Message**: la configurazione dell'archivio non è definita. Questo errore potrebbe essere causato da un'assegnazione di parametro non valida nella pipeline.
- **Cause**: non determinato
- **Raccomandazione**: controllare l'assegnazione del valore del parametro nella pipeline. L'espressione parametro può contenere caratteri non validi.

### <a name="error-code-df-excel-invalidconfiguration"></a>Codice di errore: DF-Excel-InvalidConfiguration
- **Messaggio**: il nome o l'indice del foglio di Excel è obbligatorio.
- **Cause**: non determinato
- **Raccomandazione**: controllare il valore del parametro e specificare il nome del foglio o l'indice per leggere i dati di Excel.

- **Messaggio**: il nome e l'indice del foglio di Excel non possono esistere nello stesso momento.
- **Cause**: non determinato
- **Raccomandazione**: controllare il valore del parametro e specificare il nome del foglio o l'indice per leggere i dati di Excel.

- **Message**: è stato specificato un intervallo non valido.
- **Cause**: non determinato
- **Raccomandazione**: controllare il valore del parametro e specificare un intervallo valido per riferimento: [proprietà di Excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).

- **Messaggio**: viene fornito un file di Excel non valido mentre sono supportati solo i file con estensione xlsx e xls
- **Cause**: non determinato
- **Raccomandazione**: assicurarsi che l'estensione del file di Excel sia xlsx o xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Codice di errore: DF-Excel-InvalidData
- **Messaggio**: foglio di lavoro di Excel inesistente.
- **Cause**: non determinato
- **Raccomandazione**: controllare il valore del parametro e specificare un nome di foglio o un indice valido per leggere i dati di Excel.

- **Messaggio**: la lettura dei file di Excel con schema diverso non è attualmente supportata.
- **Cause**: non determinato
- **Raccomandazione**: usare il file di Excel corretto.

- **Messaggio**: tipo di dati non supportato.
- **Cause**: non determinato
- **Raccomandazione**: usare i tipi di dati corretti per il file di Excel.

### <a name="error-code-4502"></a>Codice di errore: 4502
- **Message**: sono presenti sostanziali esecuzioni di MappingDataflow simultanee che causano errori a causa della limitazione delle richieste in Integration Runtime.
- **Cause**: una grande quantità di esecuzioni di attività del flusso di lavoro avviene simultaneamente sul Integration Runtime. Ulteriori informazioni sui [limiti Azure Data Factory](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Raccomandazione**: nel caso in cui si voglia eseguire più attività del flusso di dati in parallelo, distribuirle in più runtime di integrazione.


### <a name="error-code-invalidtemplate"></a>Codice di errore: InvalidTemplate
- **Message**: Impossibile valutare l'espressione della pipeline.
- **Cause**: l'espressione della pipeline passata nell'attività del flusso di lavoro non viene elaborata correttamente a causa di un errore di sintassi.
- **Consiglio**: controllare l'attività nel monitoraggio attività per verificare l'espressione.

### <a name="error-code-2011"></a>Codice di errore: 2011
- **Messaggio**: l'attività è in esecuzione in Azure Integration Runtime e non è stato possibile decrittografare le credenziali dell'archivio dati o del calcolo connesse tramite una Integration Runtime self-hosted. Controllare la configurazione dei servizi collegati associati a questa attività e assicurarsi di usare il tipo di runtime di integrazione appropriato.
- **Cause**: il flusso di dati non supporta i servizi collegati con il runtime di integrazione self-hosted.
- **Consiglio**: configurare il flusso di dati per l'esecuzione in Integration Runtime con "rete virtuale gestita".

## <a name="miscellaneous-troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi vari
- **Problema**: eccezione imprevista e esecuzione non riuscita
    - **Messaggio**: durante l'esecuzione dell'attività Flusso di dati: hit unexpected exception and execution failed (eccezione imprevista ed esecuzione non riuscita).
    - **Cause**: si tratta di un errore del servizio back-end. È possibile ripetere l'operazione e anche riavviare la sessione di debug.
    - **Raccomandazione**: se la ripetizione dell'operazione e il riavvio non risolvono il problema, contattare il supporto tecnico.

-  **Problema**: Anteprima dati di debug senza dati di output in join
    - **Messaggio**: numero elevato di valori null o valori mancanti che potrebbero essere causati dalla presenza di un numero insufficiente di righe campionate. Provare ad aggiornare il limite di righe di debug e ad aggiornare i dati.
    - **Cause**: la condizione di join non corrisponde ad alcuna riga o ha prodotto un numero elevato di valori null durante l'anteprima dei dati.
    - **Suggerimento**: passare a impostazioni di debug e aumentare il numero di righe nel limite di righe di origine. Assicurarsi di aver selezionato un Azure IR con un cluster di flussi di dati sufficientemente grande per gestire più dati.
    
- **Problema**: errore di convalida nell'origine con file CSV su più righe 
    - **Message**: è possibile che venga visualizzato uno dei messaggi di errore seguenti:
        - L'ultima colonna è null o mancante.
        - La convalida dello schema nell'origine non riesce.
        - L'importazione dello schema non viene visualizzata correttamente nell'esperienza utente e l'ultima colonna contiene un carattere di nuova riga nel nome.
    - **Cause**: nel flusso di dati di mapping, attualmente, l'origine CSV su più righe non funziona con \r\n come delimitatore di riga. Talvolta le righe aggiuntive a capo restituiscono interruzioni dei valori di origine. 
    - **Raccomandazione**: generare il file nell'origine con \n come delimitatore di riga anziché \r\n. In alternativa, usare l'attività di copia per convertire il file CSV con \r\n in \n come delimitatore di riga.

## <a name="general-troubleshooting-guidance"></a>Indicazioni generali sulla risoluzione dei problemi
1. Verificare lo stato delle connessioni del set di dati. In ogni trasformazione Source (Origine) e Sink controllare il servizio collegato per ogni set di dati in uso e testare le connessioni.
2. Verificare lo stato delle connessioni di file e tabelle dalla finestra di progettazione del flusso di dati. Attivare il debug e fare clic su Data Preview (Anteprima dati) nelle trasformazioni Source (Origine) per assicurarsi di poter accedere ai dati.
3. Se dall'anteprima dei dati sembra tutto corretto, passare alla finestra di progettazione della pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
