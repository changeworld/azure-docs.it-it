---
title: Guida al dimensionamento di cluster Interactive Query in HDInsight (LLAP)
description: Guida al dimensionamento di LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869668"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guida al dimensionamento di cluster Interactive Query in HDInsight (Hive LLAP)

Questo documento descrive il dimensionamento del cluster Interactive Query in HDInsight (Hive LLAP) per ottenere prestazioni ragionevoli con un carico di lavoro tipico. Si noti che le raccomandazioni fornite in questo documento sono linee guida generiche e che carichi di lavoro specifici possono richiedere un'ottimizzazione specifica.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipi di VM di Azure predefiniti per il cluster Interactive Query in HDInsight (LLAP)**

| Tipo di nodo      | Istanza | Dimensione     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB di RAM, SSD da 400 GB   |
| Worker   | **D14 v2**        | **16 vCPU, 112 GB di RAM, SSD da 800 GB**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB di RAM, SSD da 40 GB       |

***Nota: tutti i valori di configurazione raccomandati sono basati sul nodo di lavoro di tipo D14 V2***  

### <a name="configuration"></a>**Configurazione:**    
| Chiave di configurazione      | Valore consigliato  | Descrizione |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memoria totale fornita, in MB, per tutti i contenitori YARN in un nodo | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | L'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori a questo valore non avranno effetto |
| yarn.scheduler.maximum-allocation-vcores | 12 |Il numero massimo di core di CPU per ogni richiesta di contenitori in Resource Manager. Le richieste superiori a questo valore non avranno effetto. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Numero di core di CPU per NodeManager che è possibile allocare per i contenitori. |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | Allocazione di capacità YARN per la coda LLAP  |
| tez.am.resource.memory.mb | 4096 (MB) | La quantità di memoria, in MB, che dovrà usare Tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <numero_di_nodi_di_lavoro> |Il numero di sessioni per ogni coda specificata in hive.server2.tez.default.queues. Questo numero corrisponde al numero di coordinatori di query (Tez AM) |
| hive.tez.container.size | 4096 (MB) | Dimensioni del contenitore Tez specificato, in MB |
| hive.llap.daemon.num.executors | 19 | Numero di executor per ogni daemon LLAP | 
| hive.llap.io.threadpool.size | 19 | Dimensioni del pool di thread per gli executor |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | Memoria totale, in MB, usata dai singoli daemon LLAP (memoria per daemon)
| hive.llap.io.memory.size | 242688 (MB) | Dimensioni della cache, in MB, per il daemon LLAP, purché la cache SSD sia abilitata |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | Dimensioni della memoria, in MB, per eseguire un join di mappa |

### <a name="llap-architecturecomponents"></a>**Architettura/componenti di LLAP:**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="' Architettura/componenti di LLAP '" border="true":::

### <a name="llap-daemon-size-estimations"></a>**Stime delle dimensioni dei daemon LLAP:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinazione dell'allocazione di memoria totale a YARN per tutti i contenitori di un nodo**    
Configurazione: ***yarn.nodemanager.resource.memory-mb***  

Questo valore indica la somma massima di memoria, in MB, che possono usare i contenitori YARN in ogni nodo. Il valore specificato dovrà essere minore della quantità totale di memoria fisica disponibile in tale nodo.   
Memoria totale per tutti i contenitori YARN in un nodo = (memoria fisica totale-memoria per sistema operativo e altri servizi)  
Impostare questo valore su circa il 90% delle dimensioni della RAM disponibile.  
Per D14 v2, il valore consigliato è **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinazione della quantità massima di memoria per ogni richiesta di contenitori YARN**  
Configurazione: ***yarn.scheduler.maximum-allocation-mb***

Questo valore indica l'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori al valore specificato non avranno effetto. Resource Manager può fornire memoria ai contenitori in incrementi di *yarn.scheduler.minimum-allocation-mb* e non può superare le dimensioni specificate da *yarn.scheduler.maximum-allocation-mb*. Il valore specificato non dovrà essere maggiore del totale della memoria fornita per tutti i contenitori nel nodo, specificata da *yarn.nodemanager.resource.memory-mb*.    
Per i nodi di lavoro D14 v2, il valore consigliato è **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinazione della quantità massima di vCore per ogni richiesta di contenitori YARN**  
Configurazione: ***yarn.scheduler.maximum-allocation-vcores***  

Questo valore indica il numero massimo di core di CPU virtuali per ogni richiesta di contenitori in Resource Manager. La richiesta di un numero di vCore superiore a questo valore non avrà effetto. Si tratta di una proprietà globale dell'utilità di pianificazione YARN. Per il contenitore di daemon LLAP, questo valore può essere impostato sul 75% del totale di vCore disponibili. Il rimanente 25% dovrà essere riservato per NodeManager, DataNode e altri servizi in esecuzione nei nodi di lavoro.  
Le VM D14 v2 includono 16 vCore e per il contenitore di daemon LLAP è possibile usare il 75% del totale di 16 vCore.  
Per D14 v2, il valore consigliato è **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Numero di query simultanee**  
Configurazione: ***hive.server2.tez.sessions.per.default.queue***

Questo valore di configurazione determina il numero di sessioni Tez che è possibile avviare in parallelo. Queste sessioni Tez verranno avviate per ogni coda specificata da "hive.server2.tez.default.queues". Il valore corrisponde al numero di Tez AM (coordinatori di query). È consigliabile che sia identico al numero di nodi di lavoro. Il numero di Tez AM può essere maggiore del numero di nodi di daemon LLAP. La principale responsabilità di Tez AM è coordinare l'esecuzione delle query e assegnare frammenti del piano di query ai daemon LLAP corrispondenti per l'esecuzione. Mantenere questo valore come multiplo del numero di nodi di daemon LLAP per aumentare la velocità effettiva.  

Il cluster di HDInsight predefinito include quattro daemon LLAP in esecuzione in quattro nodi di lavoro, quindi il valore consigliato è **4**.  

**Dispositivo di scorrimento dell'interfaccia utente Ambari per la variabile di configurazione hive `hive.server2.tez.sessions.per.default.queue` :**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="' LLAP numero massimo di query simultanee '" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Dimensioni di Tez Container e Tez Application Master**    
Configurazione: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb*: definisce le dimensioni di Tez Application Master.  
Il valore consigliato è **4096 MB**.
   
*hive.tez.container.size*: definisce la quantità di memoria assegnata a Tez Container. Questo valore deve essere impostato tra le dimensioni minime di contenitori YARN (*yarn.scheduler.minimum-allocation-mb*) e le dimensioni massime di contenitori YARN (*yarn.scheduler.maximum-allocation-mb*). Gli executor di daemon LLAP usano questo valore per limitare l'utilizzo di memoria per ogni executor.  
Il valore consigliato è **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Allocazione di capacità alla coda LLAP**   
Configurazione: ***yarn.scheduler.capacity.root.llap.capacity***  

Questo valore indica una percentuale della capacità assegnata alla coda LLAP. Le allocazioni di capacità possono avere valori diversi per carichi di lavoro diversi, a seconda della configurazione delle code YARN. Se il carico di lavoro è costituito da operazioni di sola lettura, l'impostazione fino al 90% della capacità dovrebbe funzionare. Tuttavia, se il carico di lavoro è costituito da operazioni di aggiornamento/eliminazione/Unione mediante le tabelle gestite, è consigliabile concedere il 85% della capacità per la coda LLAP. La capacità rimanente del 15% può essere usata da altre attività, ad esempio la compattazione e così via, per allocare i contenitori dalla coda predefinita. In questo modo le attività della coda predefinita non tolgono risorse YARN.    

Per i nodi del ruolo di lavoro D14v2, il valore consigliato per la coda LLAP è **85**.     
Per i carichi di lavoro di sola lettura, può essere aumentato a 90 se appropriato.  

#### <a name="7-llap-daemon-container-size"></a>**7. Dimensioni dei contenitori di daemon LLAP**    
Configurazione: ***hive.llap.daemon.yarn.container.mb***  
   
Il daemon LLAP viene eseguito come contenitore YARN in ogni nodo di lavoro. Le dimensioni totali della memoria per i contenitori di daemon LLAP dipende dai fattori seguenti:    
*  Configurazioni delle dimensioni dei contenitori YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Numero di Tez AM in un nodo
*  Memoria totale configurata per tutti i contenitori in un nodo e capacità della coda LLAP  

La memoria necessaria per Tez Application Master (Tez AM) può essere calcolata come segue.  
Tez AM funge da coordinatore di query e il numero di TeZ AMs deve essere configurato in base a un numero di query simultanee da servire. In teoria, è possibile prendere in considerazione un Tez per ogni nodo di lavoro. Tuttavia, è possibile che venga visualizzato più di un Tez in un nodo di lavoro. A scopo di calcolo, si presuppone una distribuzione uniforme di TeZ AMs in tutti i nodi del daemon LLAP/nodi di lavoro.
È consigliabile avere 4 GB di memoria per ogni Tez AM.  

Numero di TeZ AMS = valore specificato da hive config ***hive. Server2. Tez. Sessions. per. default. Queue***.  
Numero di nodi del daemon LLAP = specificato dalla variabile env ***num_llap_nodes_for_llap_daemons*** nell'interfaccia utente di Ambari.  
Tez AM container Size = valore specificato da Tez config ***Tez. am. Resource. memory. MB***.  

Tez am memoria per nodo = **(** numero **di** Tez AMs **/** numero di nodi del daemon LLAP **)** **x** Tez am container size **)**  
Per D14 v2, la configurazione predefinita include quattro Tez AM e quattro nodi di daemon LLAP.  
Memoria del Tez AM per nodo = (ceil(4/4) x 4 GB) = 4 GB

La memoria totale disponibile per il nodo LLAP coda per ruolo di lavoro può essere calcolata come segue:  
Questo valore dipende dalla quantità totale di memoria disponibile per tutti i contenitori YARN in un nodo (*Yarn. nodemanager. Resource. memory-MB*) e dalla percentuale di capacità configurata per la coda LLAP (*Yarn. Scheduler. Capacity. root. LLAP. Capacity*).  
Memoria totale per la coda LLAP nel nodo di lavoro = Memoria totale disponibile per tutti i contenitori YARN in un nodo x Percentuale di capacità per la coda LLAP.  
Per D14 V2, questo valore è (100 GB x 0,85) = 85 GB.

Le dimensioni del contenitore di daemon LLAP vengono calcolate come segue:

**Dimensioni del contenitore del daemon LLAP = (memoria totale per la coda LLAP in un workernode)-(Tez memoria per nodo)-(dimensioni del contenitore master del servizio)**  
È presente un solo master del servizio (Master applicazione per il servizio LLAP) nel cluster generato in uno dei nodi del ruolo di lavoro. A scopo di calcolo, consideriamo un master del servizio per ogni nodo di lavoro.  
Per il nodo di lavoro D14 V2, HDI 4,0-il valore consigliato è (85 GB-4 GB-1 GB)) = **80 GB**   
(Per HDI 3,6, il valore consigliato è **79 GB** perché è necessario riservare ulteriori ~ 2 GB per il dispositivo di scorrimento).  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinazione del numero di executor per ogni daemon LLAP**  
Configurazione: ***hive.llap.daemon.num.executors** _, _ *_hive. LLAP. io. ThreadPool. Size_**

***hive.llap.daemon.num.executors***:   
Questa configurazione controlla il numero di executor che possono eseguire attività in parallelo per ogni daemon LLAP. Questo valore dipende dal numero di Vcore, dalla quantità di memoria usata per ogni Executor e dalla quantità di memoria totale disponibile per il contenitore del daemon LLAP.    Il numero di esecutori può essere sottoscritto al 120% del Vcore disponibile per ogni nodo del ruolo di lavoro. Tuttavia, deve essere regolato se non soddisfa i requisiti di memoria in base alla memoria necessaria per ogni Executor e alle dimensioni del contenitore del daemon LLAP.

Ogni executor è equivalente a un contenitore Tez e può utilizzare 4 GB di memoria (dimensioni del contenitore TEZ). Tutti gli executor nel daemon LLAP condividono la stessa memoria heap. Supponendo che non tutti gli esecutori eseguano contemporaneamente operazioni con utilizzo intensivo di memoria, è possibile considerare il 75% delle dimensioni del contenitore TeZ (4 GB) per ogni Executor. In questo modo è possibile aumentare il numero di esecutori assegnando a ogni Executor meno memoria, ad esempio 3 GB, per un maggiore parallelismo. Tuttavia, è consigliabile ottimizzare questa impostazione per il carico di lavoro di destinazione.

Le VM D14 v2 includono 16 vCore.
Per D14 V2, il valore consigliato per il numero di esecutori è (16 Vcore x 120%) ~ = **19** in ogni nodo di lavoro considerando 3 GB per Executor.

***hive.llap.io.threadpool.size***:   
questo valore specifica le dimensioni del pool di thread per gli executor. Poiché gli executor sono fissi, come specificato, il valore sarà identico al numero di executor per ogni daemon LAAP.    
Per D14 V2, il valore consigliato è **19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinazione delle dimensioni della cache dei daemon LLAP**  
Configurazione: ***hive.llap.io.memory.size***

La memoria del contenitore di daemon LLAP è costituita dai componenti seguenti:
*  Capacità aggiuntiva
*  Memoria heap usata dagli executor (Xmx)
*  Cache in memoria per ogni daemon (dimensioni della memoria off-heap, non applicabile se la cache SSD è abilitata)
*  Dimensioni dei metadati della cache in memoria (applicabile solo se la cache SSD è abilitata)

**Dimensioni della capacità aggiuntiva**: indicano una parte della memoria off-heap usata per l'overhead delle VM Java (metaspazi, stack di thread, strutture dei dati di GC e così via). In genere, questo overhead è circa il 6% delle dimensioni heap (Xmx). Per una maggiore tranquillità, questo valore può essere calcolato come il 6% del totale della memoria dei daemon LLAP.  
Per D14 V2, il valore consigliato è cella (80 GB x 0,06) ~ = **4 GB**.  

**Dimensioni heap (Xmx)** : si tratta di una quantità di memoria heap disponibile per tutti gli executor.
Dimensioni heap totali = numero di esecutori x 3 GB  
Per D14 V2, questo valore è 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="' LLAP Heap Size '" border="true":::

Quando la cache SSD è disabilitata, la cache in memoria è la quantità di memoria rimanente dopo aver preso in mano le dimensioni della superficie e dell'heap dalle dimensioni del contenitore del daemon LLAP.

Il calcolo delle dimensioni della cache è diverso se la cache SSD è abilitata.
Se si imposta *hive.llap.io.allocator.mmap* su true, la cache SSD verrà abilitata.
Se la cache SSD è abilitata, parte della memoria verrà usata per archiviare i relativi metadati. I metadati vengono archiviati in memoria ed è previsto che il numero di dimensioni della cache SSD sia pari a ~ 8%.   
Dimensioni dei metadati in memoria della cache SSD = dimensioni del contenitore del daemon LLAP-(stanza principale + dimensioni heap)  
Per D14 V2, con HDI 4,0, dimensioni dei metadati in memoria della cache SSD = 80 GB-(4 GB + 57 GB) = **19 GB**  
Per D14 V2, con HDI 3,6, dimensioni dei metadati in memoria della cache SSD = 79 GB-(4 GB + 57 GB) = **18 GB**

Considerando le dimensioni della memoria disponibili per archiviare i metadati della cache SSD, è possibile calcolare le dimensioni della cache SSD supportabili.  
Dimensioni dei metadati in memoria per la cache SSD = dimensioni del contenitore del daemon LLAP-(stanza principale + dimensioni heap) = 19 GB     
Dimensioni della cache SSD = dimensioni dei metadati in memoria per la cache SSD (19 GB)/0,08 (8%)  

Per D14 V2 e HDI 4,0, le dimensioni della cache SSD consigliate sono pari a 19 GB/0,08 ~ = **237 GB**  
Per D14 V2 e HDI 3,6, le dimensioni della cache SSD consigliate sono pari a 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Regolazione della memoria per join di mappa**   
Configurazione: ***hive.auto.convert.join.noconditionaltask.size***

Assicurarsi che *hive.auto.convert.join.noconditionaltask* sia abilitato per rendere effettivo questo parametro.
Questa configurazione determina la soglia per la selezione di MapJoin da parte di hive Optimizer che considera la sovrasottoscrizione della memoria di altri esecutori per avere più spazio per le tabelle hash in memoria per consentire più conversioni di join di mapping. Considerando 3 GB per Executor, questa dimensione può essere sottoscritta a 3 GB, ma la memoria heap può essere usata anche per i buffer di ordinamento, i buffer shuffling e così via dalle altre operazioni.   
Quindi, per D14 V2, con 3 GB di memoria per Executor, è consigliabile impostare questo valore su **2048 MB**.  

Nota: questo valore potrebbe richiedere modifiche appropriate per il carico di lavoro. Se viene impostato un valore troppo basso, è possibile che la funzionalità di conversione automatica non venga usata. L'impostazione di un valore troppo elevato può comportare l'indisponibilità di eccezioni di memoria o di pause GC che possono causare prestazioni negative.  

#### <a name="11-number-of-llap-daemons"></a>**11. numero di daemon LLAP**
Variabili di ambiente Ambari: ***num_llap_nodes, num_llap_nodes_for_llap_daemons***  

**num_llap_nodes** : specifica il numero di nodi usati dal servizio LLAP hive, inclusi i nodi che eseguono il daemon LLAP, il master del servizio LLAP e il Master applicazioni TeZ (Tez AM).  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="' Numero di nodi per il servizio LLAP '" border="true":::  

numero di nodi specificato **num_llap_nodes_for_llap_daemons** usato solo per i daemon LLAP. Le dimensioni del contenitore del daemon LLAP sono impostate sul nodo max fit, quindi verrà generato un daemon LLAP in ogni nodo.

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="' Numero di nodi per daemon LLAP '" border="true":::

È consigliabile usare entrambi i valori come numero di nodi del ruolo di lavoro nel cluster Interactive query.

### <a name="considerations-for-workload-management"></a>**Considerazioni per la gestione del carico di lavoro**  
Se si vuole abilitare la gestione del carico di lavoro per LLAP, assicurarsi di riservare una capacità sufficiente per la gestione del carico di lavoro per funzionare come previsto. La gestione del carico di lavoro richiede la configurazione di una coda YARN personalizzata, che è aggiunta alla `llap` coda. Assicurarsi di dividere la capacità totale delle risorse del cluster tra la coda LLAP e la coda di gestione del carico di lavoro in base ai requisiti del carico di lavoro.
Gestione del carico di lavoro genera Tez Application Masters (Tez AMs) quando viene attivato un piano di risorse.
Nota bene:

* Tez AMs generato mediante l'attivazione di un piano di risorse utilizza risorse dalla coda di gestione del carico di lavoro come specificato da `hive.server2.tez.interactive.queue` .  
* Il numero di TeZ AMs dipende dal valore di `QUERY_PARALLELISM` specificato nel piano di risorse.  
* Una volta attivata la gestione del carico di lavoro, Tez AMs nella coda LLAP non verrà usato. Per il coordinamento delle query vengono usati solo Tez AMs dalla coda di gestione del carico di lavoro. Tez AMs nella `llap` coda vengono usati quando la gestione del carico di lavoro è disabilitata.
 
Ad esempio: capacità totale del cluster = 100 GB di memoria, divisa tra LLAP, gestione del carico di lavoro e code predefinite, come indicato di seguito:
 - capacità coda LLAP = 70 GB
 - Capacità coda di gestione del carico di lavoro = 20 GB
 - Capacità coda predefinita = 10 GB

Con 20 GB nella capacità della coda di gestione del carico di lavoro, un piano di risorse può specificare `QUERY_PARALLELISM` il valore cinque, il che significa che la gestione del carico di lavoro può avviare cinque Tez AMs con dimensioni del contenitore di 4 GB ciascuna. Se `QUERY_PARALLELISM` il valore di è maggiore della capacità, è possibile che alcuni Tez AMs interrompano la risposta nello `ACCEPTED` stato. Hiveserver2 Interactive non può inviare frammenti di query al Tez AMs che non sono in `RUNNING` stato.


#### <a name="next-steps"></a>**Passaggi successivi**
Se l'impostazione di questi valori non ha consentito di risolvere il problema, vedere una delle risorse seguenti.

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Altri riferimenti:**
  * [Configurare altre proprietà di LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configurare le dimensioni heap del server Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Dimensionamento della memoria del join di mappa per LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Proprietà del motore di esecuzione di Tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Approfondimenti su LLAP Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)