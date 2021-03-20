---
title: Metriche di Analisi archiviazione di Azure (versione classica)
description: Informazioni su come usare le metriche di Analisi archiviazione di Azure. Informazioni sulle metriche relative a transazioni e capacità, su come vengono archiviate le metriche, abilitando le metriche e altro ancora.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d900ffa4481ba2b6deb21a8325f3f8def8084f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714732"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriche di Analisi archiviazione di Azure (versione classica)

Il **31 agosto 2023** analisi archiviazione metriche, dette anche *metriche classiche* , verranno ritirate. Per altre informazioni, consultare l'[annuncio ufficiale](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se si usano le metriche classiche, assicurarsi di passare alle metriche in Monitoraggio di Azure prima di tale data. Questo articolo consente di eseguire la transizione. 

Archiviazione di Azure usa la soluzione Analisi archiviazione per archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate a livello di operazione API e a livello di servizio di archiviazione. La capacità viene segnalata a livello di servizio di archiviazione. Le metriche possono essere usate per:
- Analizzare l'utilizzo del servizio di archiviazione.
- Diagnosticare i problemi con richieste al servizio di archiviazione.
- Migliorare le prestazioni delle applicazioni che usano un servizio.

 Le metriche di Analisi archiviazione sono abilitate per impostazione predefinita per i nuovi account di archiviazione. È possibile configurare le metriche nel [portale di Azure](https://portal.azure.com/), usando PowerShell o l'interfaccia della riga di comando di Azure. Per istruzioni dettagliate, vedere [abilitare e gestire la metrica di analisi di archiviazione di Azure (versione classica)](./manage-storage-analytics-logs.md). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.  

> [!NOTE]
> Le metriche di Analisi archiviazione sono disponibili per Archiviazione BLOB di Azure, Archiviazione code di Azure, Archiviazione tabelle di Azure e File di Azure.
> Le metriche di Analisi archiviazione sono ora metriche classiche. Si consiglia di usare [Metriche di archiviazione in Monitoraggio di Azure](../blobs/monitor-blob-storage.md) invece delle metriche di Analisi archiviazione.

## <a name="transaction-metrics"></a>Metriche di transazione  
 A intervalli di ore o minuti viene registrato un set di dati consistente per ciascun servizio di archiviazione e operazione API richiesta, inclusi ingresso e uscita, disponibilità, errori e percentuali di richieste suddivise in categorie. Un elenco completo dei dettagli delle transazioni è disponibile in [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 I dati delle transazioni vengono registrati a livello di servizio e a livello di operazione API. A livello di servizio, le statistiche che riepilogano tutte le operazioni API richieste vengono scritte in un'entità di tabella ogni ora, persino se non sono state eseguite richieste al servizio. A livello di operazione API, le statistiche vengono scritte in un'entità solo se l'operazione è stata richiesta entro l'ora.  

 Se, ad esempio, si esegue un'operazione **GetBlob** nel servizio BLOB, la metrica Analisi archiviazione registra la richiesta e la include nei dati aggregati sia per il servizio BLOB, sia per l'operazione **GetBlob**. Se nessuna operazione **GetBlob** viene richiesta in quest'ora, non viene scritta alcuna entità in *$MetricsTransactionsBlob* per questa operazione.  

 Le metriche delle transazioni vengono registrate sia per le richieste utente, sia per quelle eseguite dalla stessa Analisi archiviazione. Ad esempio, le richieste di Analisi archiviazione di scrivere entità di log e di tabella vengono registrate.

## <a name="capacity-metrics"></a>Metriche della capacità  

> [!NOTE]
>  Al momento, le metriche per la capacità sono disponibili solo per il servizio BLOB.

 I dati relativi alla capacità vengono registrati quotidianamente per il servizio BLOB di un account di archiviazione e vengono scritte due entità di tabella. Un'entità fornisce le statistiche per i dati utente e l'altra le statistiche sul contenitore BLOB `$logs` utilizzato da Analisi archiviazione. Nella tabella *$MetricsCapacityBlob* sono contenute le statistiche seguenti:  

- **Capacity**: quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte;  
- **ContainerCount**: numero di contenitori BLOB nel servizio BLOB dell'account di archiviazione;  
- **ObjectCount**: numero di BLOB di pagine o blocchi inviati e non inviati nel servizio BLOB dell'account di archiviazione.  

  Per altre informazioni sulle metriche della capacità, vedere [Schema di tabella della metrica di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Come vengono archiviate le metriche  

 Tutti i dati delle metriche per ognuno dei servizi di archiviazione vengono archiviati in tre tabelle riservate per il servizio. Una tabella per le informazioni sulle transazioni, una tabella per le informazioni sulle transazioni al minuto e un'altra tabella per le informazioni sulla capacità. Le informazioni sulla transazione e sulle transazioni al minuto consistono nei dati di richiesta e di risposta, mentre le informazioni sulla capacità consistono nei dati d'uso dell'archiviazione. Metriche per ora, minuto e capacità del servizio BLOB di un account di archiviazione sono accessibili nelle tabelle denominate nel modo descritto nella tabella seguente.  

|Livello metrica|Nomi tabella|Versioni supportate|  
|-------------------|-----------------|----------------------------|  
|Metriche orarie, posizione principale|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Solo versioni precedenti al 15 agosto 2013. Anche se tali nomi sono supportati, è consigliabile passare all'uso delle tabelle elencate di seguito.|  
|Metriche orarie, posizione principale|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo a partire dalla versione del 5 aprile 2015.|  
|Metriche per minuto, posizione principale|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|tutte le versioni. Il supporto per le metriche del servizio file è disponibile solo a partire dalla versione del 5 aprile 2015.|  
|Metriche orarie, posizione secondaria|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Metriche al minuto, posizione secondaria|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|tutte le versioni. Deve essere abilitata la replica con accesso in lettura con ridondanza geografica.|  
|Capacità (solo servizio BLOB)|$MetricsCapacityBlob|tutte le versioni.|  

 Tali tabelle vengono create automaticamente quando viene abilitato Analisi archiviazione per un endpoint di servizio di archiviazione. L'accesso alle tabelle viene eseguito tramite lo spazio dei nomi dell'account di archiviazione, ad esempio, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Le tabelle delle metriche non vengono visualizzate in un'operazione di elenco ed è necessario accedervi direttamente tramite il nome della tabella.

## <a name="metrics-alerts"></a>Avvisi delle metriche
È consigliabile impostare gli avvisi nel [portale di Azure](https://portal.azure.com) in modo da ricevere notifiche automatiche in caso di modifiche importanti nel comportamento dei servizi di archiviazione. Per istruzioni dettagliate, vedere [creare avvisi di metrica](./manage-storage-analytics-logs.md).

Se si usa uno strumento di esplorazione di archiviazione per scaricare i dati di metrica in un formato delimitato, è possibile usare Microsoft Excel per analizzare i dati. In [Strumenti client di Archiviazione di Azure](./storage-explorers.md) è presente un elenco degli strumenti di esplorazione di archiviazione disponibili.

> [!IMPORTANT]
> Potrebbe verificarsi un ritardo tra un evento di archiviazione e la memorizzazione dei relativi dati di metrica oraria o al minuto. In caso di metriche al minuto, è possibile che vengano scritti contemporaneamente diversi minuti di dati. Ciò può causare l'aggregazione di transazioni dai minuti precedenti nella transazione per il minuto corrente. Se si verifica questo problema, il servizio di avviso potrebbe non avere tutti i dati di metrica disponibili per l'intervallo di avviso configurato, il che potrebbe determinare l'attivazione imprevista degli avvisi.
>

## <a name="billing-on-storage-metrics"></a>Fatturazione delle metriche di archiviazione
Le richieste di scrittura per creare entità di tabella per le metriche vengono addebitate alle tariffe standard applicabili a tutte le operazioni di archiviazione di Azure.  

Anche le richieste di lettura ed eliminazione dei dati delle metriche da parte di un client sono fatturabili alle tariffe standard. Se è stato configurato un criterio di conservazione dei dati, non è necessario sostenere alcun addebito quando Archiviazione di Azure elimina i vecchi dati delle metriche. Se si eliminano dati di analisi, all'account vengono addebitate le operazioni di eliminazione.  

È fatturabile anche la capacità usata dalle tabelle delle metriche. Per stimare la quantità di capacità usata per l'archiviazione dei dati delle metriche usare le seguenti informazioni:  

-   Se ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, ogni ora circa 148 kB di dati vengono archiviati nelle tabelle delle transazioni metriche, se è stato abilitato il riepilogo a livello di servizio e a livello di API.  
-   Quando si abilita solo un riepilogo a livello di servizio, se entro ogni ora un servizio utilizza tutte le API presenti in ciascun servizio, nelle tabelle delle transazioni delle metriche vengono archiviati ogni ora circa 12 kB di dati.  
-   Alla tabella della capacità relativa ai BLOB vengono aggiunte due righe ogni giorno a condizione che l'utente abbia scelto i log. Questo implica che ogni giorno le dimensioni della tabella aumentano di circa 300 byte.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare un account di archiviazione](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema di tabella delle metriche di Analisi archiviazione](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Operazioni registrate di Analisi archiviazione e messaggi di stato](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registrazione di Analisi archiviazione](storage-analytics-logging.md)
