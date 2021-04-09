---
title: Risolvere i problemi delle prestazioni dell'attività di copia
description: Informazioni su come risolvere i problemi relativi alle prestazioni dell'attività di copia in Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/07/2021
ms.openlocfilehash: ce7c97abfb879e9298edac5f38540bbc026274da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584401"
---
# <a name="troubleshoot-copy-activity-performance"></a>Risolvere i problemi delle prestazioni dell'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come risolvere i problemi relativi alle prestazioni dell'attività di copia in Azure Data Factory. 

Dopo aver eseguito un'attività di copia, è possibile raccogliere il risultato dell'esecuzione e le statistiche sulle prestazioni nella visualizzazione [monitoraggio attività di copia](copy-activity-monitoring.md) . Di seguito è riportato un esempio.

![Monitorare i dettagli dell'esecuzione dell'attività di copia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Suggerimenti per l'ottimizzazione delle prestazioni

In alcuni scenari, quando si esegue un'attività di copia in Data Factory, verranno visualizzati **"suggerimenti per l'ottimizzazione delle prestazioni"** nella parte superiore, come illustrato nell'esempio precedente. I suggerimenti indicano il collo di bottiglia identificato da ADF per questa particolare esecuzione della copia, oltre a suggerimenti su come aumentare la velocità effettiva di copia. Provare a eseguire la modifica riordinata, quindi eseguire di nuovo la copia.

Come riferimento, attualmente i suggerimenti per l'ottimizzazione delle prestazioni forniscono suggerimenti per i casi seguenti:

| Category              | Suggerimenti per l'ottimizzazione delle prestazioni                                      |
| --------------------- | ------------------------------------------------------------ |
| Specifico dell'archivio dati   | Caricamento dei dati in **Azure sinapsi Analytics**: è consigliabile usare l'istruzione di base o di copia se non viene usata. |
| &nbsp;                | Copia dei dati da/in un **database SQL di Azure**: quando DTU è in un utilizzo elevato, Suggerisci l'aggiornamento a un livello superiore. |
| &nbsp;                | Copia dei dati da/in **Azure Cosmos DB**: quando ur è sottoposto a un utilizzo elevato, suggerire l'aggiornamento a ur più grandi. |
|                       | Copia dei dati dalla tabella SAP: quando si **copiano** grandi quantità di dati, suggerire di sfruttare l'opzione di partizione del connettore SAP per abilitare il carico parallelo e aumentare il numero massimo di partizioni. |
| &nbsp;                | Inserimento di dati da **Amazon spostamento**: suggerire l'uso di Unload se non viene usato. |
| Limitazione dell'archivio dati | Se durante la copia un numero di operazioni di lettura/scrittura viene limitato dall'archivio dati, è consigliabile controllare e aumentare la frequenza di richieste consentite per l'archivio dati o ridurre il carico di lavoro simultaneo. |
| Runtime di integrazione  | Se si usa un **Integration Runtime self-hosted (IR)** e l'attività di copia resta in attesa lungo nella coda fino a quando il runtime di integrazione non dispone di risorse per l'esecuzione, suggerire la scalabilità orizzontale o verticale del runtime di integrazione. |
| &nbsp;                | Se si usa un **Azure Integration Runtime** che si trova in un'area non ottimale che produce una lettura/scrittura lenta, suggerire la configurazione per usare un runtime di integrazione in un'altra area. |
| Tolleranza di errore       | Se si configura la tolleranza di errore e le righe incompatibili vengono ignorate, è consigliabile verificare che i dati di origine e sink siano compatibili. |
| copia di staging           | Se la copia di gestione temporanea è configurata ma non è utile per la coppia di sink di origine, è consigliabile rimuoverla. |
| Riprendi                | Quando l'attività di copia viene riavviata dall'ultimo punto di errore ma si modifica l'impostazione DIU dopo l'esecuzione originale, tenere presente che la nuova impostazione DIU non ha effetto. |

## <a name="understand-copy-activity-execution-details"></a>Informazioni sui dettagli dell'esecuzione dell'attività di copia

I dettagli e le durate di esecuzione nella parte inferiore della visualizzazione monitoraggio dell'attività di copia descrivono le fasi principali dell'attività di copia (vedere l'esempio all'inizio di questo articolo), che è particolarmente utile per la risoluzione dei problemi relativi alle prestazioni di copia. Il collo di bottiglia dell'esecuzione della copia è quello con la durata più lunga. Vedere la tabella seguente per la definizione di ogni fase e informazioni su come [risolvere i problemi relativi all'attività di copia in Azure IR](#troubleshoot-copy-activity-on-azure-ir) e [risolvere i problemi relativi all'attività di copia nel runtime di integrazione self-hosted](#troubleshoot-copy-activity-on-self-hosted-ir) con tali informazioni.

| Fase           | Descrizione                                                  |
| --------------- | ------------------------------------------------------------ |
| Coda           | Tempo trascorso fino a quando l'attività di copia non inizia effettivamente sul runtime di integrazione. |
| Script di pre-copia | Tempo trascorso tra l'attività di copia a partire da IR e l'attività di copia che termina l'esecuzione dello script di pre-copia nell'archivio dati sink. Applicare quando si configura lo script di pre-copia per i sink di database, ad esempio durante la scrittura di dati nel database SQL di Azure, eseguire la pulizia prima di copiare nuovi dati. |
| Trasferimento        | Il tempo trascorso tra la fine del passaggio precedente e l'IR che trasferisce tutti i dati dall'origine al sink. <br/>Si noti che i passaggi secondari in transfer vengono eseguiti in parallelo e alcune operazioni non vengono visualizzate, ad esempio, il formato di file di analisi o di generazione.<br><br/>- **Tempo per il primo byte:** Tempo trascorso tra la fine del passaggio precedente e l'ora in cui il runtime di integrazione riceve il primo byte dall'archivio dati di origine. Si applica alle origini non basate su file.<br>- **Origine elenco:** Quantità di tempo impiegato per l'enumerazione di file di origine o partizioni di dati. Quest'ultimo si applica quando si configurano le opzioni di partizione per le origini del database, ad esempio quando si copiano dati da database come Oracle/SAP HANA/Teradata/Netezza e così via.<br/>-**Lettura dall'origine:** Quantità di tempo impiegato per il recupero dei dati dall'archivio dati di origine.<br/>- **Scrittura nel sink:** Quantità di tempo impiegato per la scrittura dei dati nell'archivio dati sink. Si noti che alcuni connettori attualmente non hanno questa metrica, tra cui Azure ricerca cognitiva, Azure Esplora dati, archiviazione tabelle di Azure, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce Service Cloud. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Risolvere i problemi relativi all'attività di copia in Azure IR

Seguire i [passaggi di ottimizzazione delle prestazioni](copy-activity-performance.md#performance-tuning-steps) per pianificare ed eseguire il test delle prestazioni per lo scenario. 

Quando le prestazioni dell'attività di copia non soddisfano le aspettative, per risolvere i problemi relativi all'attività di copia singola in esecuzione in Azure Integration Runtime, se vengono visualizzati suggerimenti per l' [ottimizzazione delle prestazioni](#performance-tuning-tips) nella visualizzazione copia monitoraggio, applicare il suggerimento e riprovare. In caso contrario, [comprendere i dettagli dell'esecuzione dell'attività di copia](#understand-copy-activity-execution-details), verificare quale fase ha la durata più **lunga** e applicare le indicazioni seguenti per migliorare le prestazioni di copia:

- **"Script di pre-copia" ha avuto una durata prolungata:** significa che lo script di pre-copia in esecuzione nel database sink richiede molto tempo. Ottimizzare la logica dello script di pre-copia specificata per migliorare le prestazioni. Per ulteriori informazioni sul miglioramento dello script, contattare il team del database.

- **"Trasferimento-tempi per il primo byte" ha avuto una durata di lavoro prolungata**: significa che la query di origine impiega molto tempo per restituire i dati. Controllare e ottimizzare la query o il server. Se è necessaria ulteriore assistenza, contattare il team dell'archivio dati.

- **"Trasferimento-listato di origine" ha sperimentato una lunga durata del lavoro**: significa che l'enumerazione dei file di origine o delle partizioni di dati del database di origine è lenta.
  - Quando si copiano dati da un'origine basata su file, se si usa il **filtro con caratteri jolly** per il percorso o il nome di file ( `wildcardFolderPath` o `wildcardFileName` ) oppure si usa il **filtro dell'ora dell'Ultima modifica del file** ( `modifiedDatetimeStart` o `modifiedDatetimeEnd` ), si noti che tale filtro comporterebbe l'inserimento dell'attività di copia nell'elenco di tutti i file nella cartella specificata sul lato client, quindi applica Questa enumerazione di file potrebbe diventare il collo di bottiglia soprattutto quando solo un piccolo set di file soddisfa la regola di filtro.

    - Controllare se è possibile [copiare i file in base al percorso o al nome del file partizionato DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). In questo modo non viene imposto alcun onere sul lato di origine dell'elenco.

    - Controllare se è invece possibile usare il filtro nativo dell'archivio dati, in particolare "**prefisso**" per Amazon S3/BLOB di Azure/archiviazione file di Azure e "**listAfter/listBefore**" per ADLS Gen1. Questi filtri sono un filtro sul lato server dell'archivio dati e offrono prestazioni molto migliori.

    - Si consiglia di suddividere un singolo set di dati di grandi dimensioni in diversi set di dati più piccoli e di consentire l'esecuzione simultanea di ogni processo di copia per ogni parte dei dati. Questa operazione può essere eseguita con Lookup/GetMetadata + ForEach + Copy. Vedere [copiare i file da più contenitori](solution-template-copy-files-multiple-containers.md) o [migrare i dati da Amazon S3 a ADLS Gen2](solution-template-migration-s3-azure.md) modelli di soluzione come esempio generale.

  - Controllare se ADF segnala eventuali errori di limitazione nell'origine o se l'archivio dati è in stato di utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Usare Azure IR nello stesso o vicino all'area dell'archivio dati di origine.

- **"Trasferimento-lettura dall'origine" durata di lavoro prolungata**: 

  - Adottare le procedure consigliate per il caricamento dei dati specifico del connettore, se applicabile. Ad esempio, quando si copiano dati da [Amazon](connector-amazon-redshift.md), configurare per l'uso dello scaricamento di spostamento verso il posto.

  - Controllare se ADF segnala eventuali errori di limitazione nell'origine o se l'archivio dati è sottoposto a un utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Verificare il modello di origine e sink di copia: 

    - Se il modello di copia supporta più di 4 unità di integrazione dati (DIUs), fare riferimento a [questa sezione](copy-activity-performance-features.md#data-integration-units) per informazioni dettagliate, in genere è possibile provare ad aumentare DIUs per ottenere prestazioni migliori. 

    - In caso contrario, è consigliabile suddividere un singolo set di dati di grandi dimensioni in diversi set di dati più piccoli e consentire l'esecuzione simultanea di ogni processo di copia. Questa operazione può essere eseguita con Lookup/GetMetadata + ForEach + Copy. Vedere [copiare i file da più contenitori](solution-template-copy-files-multiple-containers.md), [migrare i dati da Amazon S3 a ADLS Gen2 o eseguire](solution-template-migration-s3-azure.md) [una copia bulk con](solution-template-bulk-copy-with-control-table.md) i modelli di soluzione della tabella di controllo come esempio generale.

  - Usare Azure IR nello stesso o vicino all'area dell'archivio dati di origine.

- **"Trasferimento in scrittura nel sink" con una durata di lavoro prolungata**:

  - Adottare le procedure consigliate per il caricamento dei dati specifico del connettore, se applicabile. Ad esempio, durante la copia dei dati in [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md), usare l'istruzione di base o di copia. 

  - Controllare se ADF segnala eventuali errori di limitazione del sink o se l'archivio dati è sottoposto a un utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Verificare il modello di origine e sink di copia: 

    - Se il modello di copia supporta più di 4 unità di integrazione dati (DIUs), fare riferimento a [questa sezione](copy-activity-performance-features.md#data-integration-units) per informazioni dettagliate, in genere è possibile provare ad aumentare DIUs per ottenere prestazioni migliori. 

    - In caso contrario, ottimizzare gradualmente le [copie parallele](copy-activity-performance-features.md). si noti che troppe copie parallele potrebbero anche danneggiare le prestazioni.

  - Usare Azure IR nello stesso o vicino all'area dell'archivio dati sink.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Risolvere i problemi relativi all'attività di copia nel runtime di integrazione

Seguire i [passaggi di ottimizzazione delle prestazioni](copy-activity-performance.md#performance-tuning-steps) per pianificare ed eseguire il test delle prestazioni per lo scenario. 

Quando le prestazioni di copia non soddisfano le aspettative, per risolvere i problemi di singola attività di copia in esecuzione in Azure Integration Runtime, se vengono visualizzati suggerimenti per l' [ottimizzazione delle prestazioni](#performance-tuning-tips) nella visualizzazione copia monitoraggio, applicare il suggerimento e riprovare. In caso contrario, [comprendere i dettagli dell'esecuzione dell'attività di copia](#understand-copy-activity-execution-details), verificare quale fase ha la durata più **lunga** e applicare le indicazioni seguenti per migliorare le prestazioni di copia:

- **"Coda" lunga durata:** indica che l'attività di copia resta in attesa lungo nella coda fino a quando il runtime di integrazione self-hosted non dispone di risorse da eseguire. Controllare la capacità e l'utilizzo di IR e [aumentare o](create-self-hosted-integration-runtime.md#high-availability-and-scalability) ridurre le prestazioni in base al carico di lavoro.

- **"Trasferimento-tempi per il primo byte" ha avuto una durata di lavoro prolungata**: significa che la query di origine impiega molto tempo per restituire i dati. Controllare e ottimizzare la query o il server. Se è necessaria ulteriore assistenza, contattare il team dell'archivio dati.

- **"Trasferimento-listato di origine" ha sperimentato una lunga durata del lavoro**: significa che l'enumerazione dei file di origine o delle partizioni di dati del database di origine è lenta.

  - Controllare se il computer del runtime di integrazione self-hosted presenta una bassa latenza per la connessione all'archivio dati di origine. Se l'origine è in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per controllare la latenza dal computer IR self-hosted all'area di Azure, minore sarà il livello di prestazioni migliore.

  - Quando si copiano dati da un'origine basata su file, se si usa il **filtro con caratteri jolly** per il percorso o il nome di file ( `wildcardFolderPath` o `wildcardFileName` ) oppure si usa il **filtro dell'ora dell'Ultima modifica del file** ( `modifiedDatetimeStart` o `modifiedDatetimeEnd` ), si noti che tale filtro comporterebbe l'inserimento dell'attività di copia nell'elenco di tutti i file nella cartella specificata sul lato client, quindi applica Questa enumerazione di file potrebbe diventare il collo di bottiglia soprattutto quando solo un piccolo set di file soddisfa la regola di filtro.

    - Controllare se è possibile [copiare i file in base al percorso o al nome del file partizionato DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). In questo modo non viene imposto alcun onere sul lato di origine dell'elenco.

    - Controllare se è invece possibile usare il filtro nativo dell'archivio dati, in particolare "**prefisso**" per Amazon S3/BLOB di Azure/archiviazione file di Azure e "**listAfter/listBefore**" per ADLS Gen1. Questi filtri sono un filtro sul lato server dell'archivio dati e offrono prestazioni molto migliori.

    - Si consiglia di suddividere un singolo set di dati di grandi dimensioni in diversi set di dati più piccoli e di consentire l'esecuzione simultanea di ogni processo di copia per ogni parte dei dati. Questa operazione può essere eseguita con Lookup/GetMetadata + ForEach + Copy. Vedere [copiare i file da più contenitori](solution-template-copy-files-multiple-containers.md) o [migrare i dati da Amazon S3 a ADLS Gen2](solution-template-migration-s3-azure.md) modelli di soluzione come esempio generale.

  - Controllare se ADF segnala eventuali errori di limitazione nell'origine o se l'archivio dati è in stato di utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

- **"Trasferimento-lettura dall'origine" durata di lavoro prolungata**: 

  - Controllare se il computer del runtime di integrazione self-hosted presenta una bassa latenza per la connessione all'archivio dati di origine. Se l'origine è in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per controllare la latenza dal computer IR self-hosted alle aree di Azure, minore sarà il migliore.

  - Verificare che il computer IR self-hosted disponga di una larghezza di banda in ingresso sufficiente per leggere e trasferire i dati in modo efficiente. Se l'archivio dati di origine si trova in Azure, è possibile usare [questo strumento](https://www.azurespeed.com/Azure/Download) per controllare la velocità di download.

  - Controllare la tendenza di utilizzo della memoria e della CPU del runtime di integrazione self-hosted in portale di Azure > pagina di Panoramica di data factory >. Prendere in considerazione la possibilità di [aumentare](create-self-hosted-integration-runtime.md#high-availability-and-scalability) o ridurre le prestazioni se l'utilizzo della CPU è elevato o la memoria disponibile è insufficiente.

  - Adottare le procedure consigliate per il caricamento dei dati specifico del connettore, se applicabile. Ad esempio:

    - Quando si [copiano](connector-sap-table.md#sap-table-as-source)dati da [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), SAP TABLE e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), abilitare le opzioni della partizione di dati per copiare i dati in parallelo.

    - Per la copia di dati da [HDFS](connector-hdfs.md), configurare per l'uso di DistCp.

    - Quando si copiano dati da [Amazon](connector-amazon-redshift.md), configurare per l'uso dello scaricamento di spostamento verso il suo.

  - Controllare se ADF segnala eventuali errori di limitazione nell'origine o se l'archivio dati è sottoposto a un utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Verificare il modello di origine e sink di copia: 

    - Se si copiano dati da archivi dati abilitati per le opzioni di partizione, si consiglia di ottimizzare gradualmente le [copie parallele](copy-activity-performance-features.md). tenere presente che troppe copie parallele potrebbero addirittura danneggiare le prestazioni.

    - In caso contrario, è consigliabile suddividere un singolo set di dati di grandi dimensioni in diversi set di dati più piccoli e consentire l'esecuzione simultanea di ogni processo di copia. Questa operazione può essere eseguita con Lookup/GetMetadata + ForEach + Copy. Vedere [copiare i file da più contenitori](solution-template-copy-files-multiple-containers.md), [migrare i dati da Amazon S3 a ADLS Gen2 o eseguire](solution-template-migration-s3-azure.md) [una copia bulk con](solution-template-bulk-copy-with-control-table.md) i modelli di soluzione della tabella di controllo come esempio generale.

- **"Trasferimento in scrittura nel sink" con una durata di lavoro prolungata**:

  - Adottare le procedure consigliate per il caricamento dei dati specifico del connettore, se applicabile. Ad esempio, durante la copia dei dati in [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md), usare l'istruzione di base o di copia. 

  - Controllare se il computer del runtime di integrazione self-hosted presenta una bassa latenza per la connessione all'archivio dati sink. Se il sink si trova in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per controllare la latenza dal computer IR self-hosted all'area di Azure, minore sarà la qualità.

  - Verificare che il computer IR self-hosted disponga di una larghezza di banda in uscita sufficiente per trasferire e scrivere i dati in modo efficiente. Se l'archivio dati sink è in Azure, è possibile usare [questo strumento](https://www.azurespeed.com/Azure/UploadLargeFile) per controllare la velocità di caricamento.

  - Controllare se la tendenza di utilizzo della memoria e della CPU del runtime di integrazione self-hosted in portale di Azure > pagina di Panoramica di data factory >. Prendere in considerazione la possibilità di [aumentare](create-self-hosted-integration-runtime.md#high-availability-and-scalability) o ridurre le prestazioni se l'utilizzo della CPU è elevato o la memoria disponibile è insufficiente.

  - Controllare se ADF segnala eventuali errori di limitazione del sink o se l'archivio dati è sottoposto a un utilizzo elevato. In tal caso, è possibile ridurre i carichi di lavoro nell'archivio dati oppure provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Si consideri di ottimizzare gradualmente le [copie parallele](copy-activity-performance-features.md). si noti che troppe copie parallele potrebbero anche danneggiare le prestazioni.


## <a name="connector-and-ir-performance"></a>Prestazioni del connettore e IR 

Questa sezione illustra alcune guide per la risoluzione dei problemi relativi alle prestazioni per un particolare tipo di connettore o runtime di integrazione.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>Il tempo di esecuzione delle attività varia a seconda che si usi Azure IR e Azure VNet IR

Il tempo di esecuzione dell'attività varia quando il set di dati è basato su Integration Runtime diversi.

- **Sintomi**: è sufficiente impostare l'elenco a discesa servizio collegato nel set di dati per eseguire le stesse attività della pipeline, ma con tempi di esecuzione notevolmente diversi. Quando il set di dati è basato sulla rete virtuale gestita Integration Runtime, la media richiede più tempo rispetto all'esecuzione in base alla Integration Runtime predefinita.  

- **Motivo**: se si verificano i dettagli delle esecuzioni della pipeline, è possibile osservare che la pipeline lenta viene eseguita in un runtime di integrazione VNet (rete virtuale) gestito, mentre quello normale viene eseguito in Azure IR. Per impostazione predefinita, il runtime di integrazione VNet gestito impiega più tempo di coda rispetto a Azure IR perché non si riserva un nodo di calcolo per ogni data factory, quindi si verifica un riscaldamento per ogni attività di copia da avviare e si verifica principalmente nel join VNet anziché Azure IR. 



    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Prestazioni ridotte durante il caricamento dei dati nel database SQL di Azure

- **Sintomi**: la copia dei dati nel database SQL di Azure risulta lenta.

- **Causa**: la causa principale del problema viene principalmente attivata dal collo di bottiglia del lato del database SQL di Azure. Di seguito sono riportate alcune possibili cause:

    - Il livello del database SQL di Azure non è sufficientemente elevato.

    - L'utilizzo di DTU del database SQL di Azure è prossimo al 100%. È possibile [monitorare le prestazioni](../azure-sql/database/monitor-tune-overview.md) e prendere in considerazione l'aggiornamento del livello del database SQL di Azure.

    - Gli indici non sono impostati correttamente. Rimuovere tutti gli indici prima del caricamento dei dati e ricrearli al termine del caricamento.

    - WriteBatchSize non è sufficientemente grande da contenere le dimensioni della riga dello schema. Provare a ingrandire la proprietà del problema.

    - Anziché inserire in blocco, viene usato stored procedure, che dovrebbe avere prestazioni peggiori. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Timeout o rallentamento delle prestazioni durante l'analisi di file di Excel di grandi dimensioni

- **Sintomi**:

    - Quando si crea un set di dati di Excel e si importa lo schema dai fogli di lavoro di connessione/archivio, Anteprima dati, elenco o aggiornamento, è possibile che si verifichi un errore di timeout se le dimensioni del file di Excel sono elevate.

    - Quando si usa l'attività di copia per copiare dati da un file di Excel di grandi dimensioni (>= 100 MB) in un altro archivio dati, è possibile che si verifichi un problema di prestazioni insufficienti.

- **Causa**: 

    - Per operazioni quali l'importazione dello schema, l'anteprima dei dati e l'elenco dei fogli di lavoro nel set di dati di Excel, il timeout è 100 s e static. Per il file di Excel di grandi dimensioni, queste operazioni potrebbero non terminare entro il valore di timeout.

    - L'attività di copia ADF legge l'intero file di Excel in memoria e quindi individua le celle e il foglio di lavoro specificati per leggere i dati. Questo comportamento è dovuto all'utilizzo dell'ADF SDK sottostante.

- **Risoluzione**: 

    - Per l'importazione dello schema, è possibile generare un file di esempio più piccolo, ovvero un subset del file originale, e scegliere "Importa schema da file di esempio" anziché "Importa schema da connessione/archivio".

    - Per elencare il foglio di testo, nell'elenco a discesa del foglio di testo è possibile fare clic su "modifica" e immettere invece il nome o l'indice del foglio.

    - Per copiare un file di Excel di grandi dimensioni (>100 MB) in un altro archivio, è possibile usare l'origine Excel del flusso di dati, che consente di leggere e migliorare le prestazioni di streaming.
    
## <a name="other-references"></a>Altri riferimenti

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione BLOB di Azure: [obiettivi di scalabilità e prestazioni per](../storage/blobs/scalability-targets.md) l'archiviazione BLOB e l' [elenco di controllo di prestazioni e scalabilità per l'archiviazione BLOB](../storage/blobs/storage-performance-checklist.md).
* Archiviazione tabelle di Azure: [obiettivi di scalabilità e prestazioni per](../storage/tables/scalability-targets.md) l'archiviazione tabelle e l' [elenco di controllo di prestazioni e scalabilità per l'archiviazione tabelle](../storage/tables/storage-performance-checklist.md).
* Database SQL di Azure: è possibile [monitorare le prestazioni](../azure-sql/database/monitor-tune-overview.md) e controllare la percentuale di unità di transazione di database (DTU).
* Azure sinapsi Analytics: la relativa funzionalità viene misurata in unità data warehouse (DWU). Vedere [gestire la potenza di calcolo in Azure sinapsi Analytics (panoramica)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [monitorare e ottimizzare le prestazioni](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* File server locale: [ottimizzazione delle prestazioni per i file server](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Guida a prestazioni e scalabilità dell'attività di copia](copy-activity-performance.md)
- [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia](copy-activity-performance-features.md)
- [Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
