---
title: Ottimizzazione per consigli di cluster Advisor
titleSuffix: Azure HDInsight
description: Ottimizzare le raccomandazioni di Apache HBase per cluster Advisor in Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943010"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Avvisi di Apache HBase in Azure HDInsight

Questo articolo descrive diversi avvisi che consentono di ottimizzare le prestazioni di Apache HBase in Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Ottimizza HBase per leggere i dati scritti più di recente

Se la useCase prevede la lettura dei dati scritti più di recente da HBase, questo avviso può essere utile. Per ottenere prestazioni elevate, è ottimale che le letture HBase vengano gestite da memstore, anziché dall'archiviazione remota.

Query Advisory indica che per una determinata famiglia di colonne in una tabella > 75% di letture che vengono gestite da memstore. Questo indicatore suggerisce che anche se si verifica uno scaricamento in memstore, è necessario accedere al file recente e che deve trovarsi nella cache. I dati vengono innanzitutto scritti in memstore il sistema accede ai dati recenti. C'è la possibilità che i thread di scaricamento HBase interni rilevino che una determinata area ha raggiunto le dimensioni 128M (impostazione predefinita) e può attivare uno scaricamento. Questo scenario si verifica anche quando i dati più recenti sono stati scritti quando il memstore era intorno alle dimensioni di 128M. Pertanto, una lettura successiva dei record recenti potrebbe richiedere la lettura di un file anziché memstore. È quindi consigliabile ottimizzare che anche i dati recenti scaricati di recente possano trovarsi nella cache.

Per ottimizzare i dati recenti nella cache, prendere in considerazione le impostazioni di configurazione seguenti:

1. Impostare `hbase.rs.cacheblocksonwrite` su `true`. Questa configurazione predefinita in HDInsight HBase è `true` , quindi verificare che non venga reimpostata su `false` .

2. Aumentare il `hbase.hstore.compactionThreshold` valore in modo che sia possibile evitare l'avvio della compattazione. Per impostazione predefinita, questo valore è impostato su `3`. È possibile aumentarlo a un valore superiore, ad esempio `10` .

3. Se si segue il passaggio 2 e si imposta compactionThreshold, quindi si passa `hbase.hstore.compaction.max` a un valore superiore, ad esempio `100` , e si aumenta anche il valore della configurazione `hbase.hstore.blockingStoreFiles` a un valore più alto, ad esempio `300` .

4. Se si è certi di dover leggere solo i dati recenti, impostare la `hbase.rs.cachecompactedblocksonwrite` configurazione **su on**. Questa configurazione indica al sistema che anche se si verifica una compattazione, i dati rimangono nella cache. Le configurazioni possono essere impostate anche a livello di famiglia. 

   Nella shell di HBase eseguire il comando seguente per impostare la `hbase.rs.cachecompactedblocksonwrite` configurazione:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. La cache a blocchi può essere disattivata per un determinato gruppo in una tabella. Assicurarsi che **sia attivato per le famiglie con le** letture dei dati più recenti. Per impostazione predefinita, la cache a blocchi viene attivata per tutte le famiglie di una tabella. Se è stata disabilitata la cache a blocchi per una famiglia ed è necessario attivarla, utilizzare il comando ALTER dalla shell HBase.

   Queste configurazioni consentono di garantire che i dati siano disponibili nella cache e che i dati recenti non subiscono una compattazione. Se è possibile usare una durata (TTL) nello scenario, provare a usare la compattazione a livelli di data. Per altre informazioni, vedere [Guida di riferimento di Apache HBase: compattazione a livelli di data](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Ottimizzare la coda di scaricamento

Questo avviso indica che è possibile che sia necessario ottimizzare gli scaricamenti HBase. La configurazione corrente per i gestori di scaricamento potrebbe non essere sufficientemente elevata da gestire il traffico di scrittura, causando un rallentamento dello svuotamento.

Nell'interfaccia utente del server di area, si noti che la coda di scaricamento supera 100. Questa soglia indica che gli scaricamenti sono lenti e potrebbe essere necessario ottimizzare la   `hbase.hstore.flusher.count` configurazione. Per impostazione predefinita, il valore è 2. Assicurarsi che i thread di svuotamento massimo non aumentino oltre i 6.

Vedere anche se si ha una raccomandazione per l'ottimizzazione del numero di aree. In caso affermativo, è consigliabile provare l'ottimizzazione dell'area per verificare se questo consente di scaricare più rapidamente. In caso contrario, l'ottimizzazione dei thread di scaricamento potrebbe essere utile.

## <a name="region-count-tuning"></a>Ottimizzazione del numero di aree

Il numero di avvisi di ottimizzazione delle aree indica che HBase ha bloccato gli aggiornamenti e che il numero di aree può essere superiore alle dimensioni dell'heap supportate in modo ottimale. È possibile ottimizzare le dimensioni dell'heap, le dimensioni memstore e il numero di aree.

Uno scenario di esempio:

- Si supponga che le dimensioni dell'heap per il server di area siano di 10 GB. Per impostazione predefinita `hbase.hregion.memstore.flush.size` `128M` , è. Il valore predefinito per la proprietà `hbase.regionserver.global.memstore.size` è `0.4`. Il che significa che da 10 GB, 4 GB vengono allocati per memstore (globalmente).

- Si supponga che esista una distribuzione uniforme del carico di scrittura in tutte le aree e che ogni area cresca fino a 128 MB solo se il numero massimo di aree in questa configurazione è costituito dalle `32` aree. Se un determinato server di area è configurato in modo da avere 32 aree, il sistema meglio evita di bloccare gli aggiornamenti.

- Con queste impostazioni, il numero di aree è 100. Il memstore globale da 4 GB è ora suddiviso in 100 aree. Quindi, ogni area ottiene solo 40 MB per memstore. Quando le Scritture sono uniformi, il sistema esegue Scaricamenti frequenti e dimensioni inferiori dell'ordine < 40 MB. La presenza di molti thread di scaricamento potrebbe aumentare la velocità di scaricamento `hbase.hstore.flusher.count` .

L'Advisory significa che sarebbe opportuno riconsiderare il numero di aree per server, le dimensioni dell'heap e la configurazione globale delle dimensioni del memstore insieme all'ottimizzazione dei thread di scaricamento per evitare che gli aggiornamenti vengano bloccati.

## <a name="compaction-queue-tuning"></a>Ottimizzazione della coda di compattazione

Se la coda di compattazione HBase aumenta fino a 2000 e si verifica periodicamente, è possibile aumentare i thread di compattazione a un valore più grande.

Quando è presente un numero eccessivo di file per la compattazione, può comportare un maggiore utilizzo dell'heap correlato al modo in cui i file interagiscono con il file system di Azure. Quindi, è preferibile completare la compattazione nel minor tempo possibile. Alcune volte nei cluster meno recenti, le configurazioni di compattazione correlate alla limitazione potrebbero causare un rallentamento della velocità di compattazione.

Controllare le configurazioni `hbase.hstore.compaction.throughput.lower.bound` e `hbase.hstore.compaction.throughput.higher.bound` . Se sono già impostati su 50M e 100M, lasciarli invariati. Tuttavia, se queste impostazioni sono state configurate su un valore inferiore, come nel caso dei cluster meno recenti, modificare rispettivamente i limiti a 50M e 100M.

Le configurazioni sono `hbase.regionserver.thread.compaction.small` e `hbase.regionserver.thread.compaction.large` (i valori predefiniti sono 1 ogni).
Limitare il valore massimo per questa configurazione in modo che sia minore di 3.

## <a name="full-table-scan"></a>Analisi completa tabella

L'Advisory di analisi completa delle tabelle indica che oltre il 75% delle analisi rilasciate sono analisi complete della tabella/area. È possibile rivedere il modo in cui il codice chiama le analisi per migliorare le prestazioni di esecuzione delle query. Prendere in considerazione le procedure seguenti:

* Impostare la riga di avvio e di arresto appropriata per ogni analisi.

* Usare l'API **MultiRowRangeFilter** in modo che sia possibile eseguire query su intervalli diversi in una chiamata di analisi. Per ulteriori informazioni, vedere la [documentazione dell'API MultiRowRangeFilter](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Nei casi in cui è necessaria un'analisi completa della tabella o dell'area, controllare se esiste la possibilità di evitare l'utilizzo della cache per tali query, in modo che le altre query che usano la cache potrebbero non rimuovere i blocchi che sono a caldo. Per assicurarsi che le analisi non usino la cache, usare l'API **Scan** con l'opzione di memorizzazione nella cache **(false)** nel codice: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Passaggi successivi

[Ottimizzare Apache HBase usando Ambari](../optimize-hbase-ambari.md)
