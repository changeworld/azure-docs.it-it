---
title: Verificare la presenza di errori in pool e nodi
description: Questo articolo illustra le operazioni in background che possono verificarsi, assieme agli errori a cui prestare attenzione e come evitarli durante la creazione di pool e nodi.
author: mscurrell
ms.author: markscu
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: 8901877ab3055c02dfc8c129fb35864418cd19d8
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549136"
---
# <a name="check-for-pool-and-node-errors"></a>Verificare la presenza di errori in pool e nodi

Durante la creazione e la gestione di pool di Azure Batch, alcune operazioni vengono eseguite immediatamente, Il rilevamento degli errori per queste operazioni è in genere semplice, perché vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente. mentre altre sono asincrone e vengono eseguite in background, richiedendo alcuni minuti per il completamento.

Verificare di aver impostato le applicazioni per implementare il controllo completo degli errori, in particolare per le operazioni asincrone. Questo può essere utile per identificare e diagnosticare tempestivamente i problemi.

Questo articolo descrive come rilevare ed evitare errori nelle operazioni in background che possono verificarsi per i pool e i nodi del pool.

## <a name="pool-errors"></a>Errori dei pool

### <a name="resize-timeout-or-failure"></a>Timeout ridimensionamento o errore

Durante la creazione di un nuovo pool o il ridimensionamento di un pool esistente, si specifica il numero di nodi di destinazione. L'operazione di creazione o ridimensionamento viene completata immediatamente, ma l'effettiva allocazione dei nuovi nodi o la rimozione dei nodi esistenti potrebbe richiedere diversi minuti. È possibile specificare il timeout di ridimensionamento della camma nell'API [Crea](/rest/api/batchservice/pool/add) o [Ridimensiona](/rest/api/batchservice/pool/resize) . Se batch non è in grado di ottenere il numero di nodi di destinazione durante il periodo di timeout di ridimensionamento, il pool entra in uno stato stabile e segnala gli errori di ridimensionamento.

La proprietà [ResizeError](/rest/api/batchservice/pool/get#resizeerror) relativa alla valutazione più recente elenca gli errori che si sono verificati.

Ecco alcune cause comuni degli errori di ridimensionamento:

- Il timeout ridimensionamento è troppo breve
  - Nella maggior parte dei casi il timeout predefinito di 15 minuti è sufficiente per completare l'allocazione o la rimozione dei nodi del pool.
  - Se occorre allocare un numero elevato di nodi, è consigliabile impostare il timeout di ridimensionamento su 30 minuti. È il caso ad esempio del ridimensionamento a più di 1.000 nodi da un'immagine di Azure Marketplace o a più di 300 nodi da un'immagine di macchina virtuale personalizzata.
- Quota di core insufficiente
  - Un account Batch può allocare un numero limitato di core in tutti i pool. Quando viene raggiunta la quota limite, Batch smette di allocare nodi. È possibile [aumentare](./batch-quota-limit.md) la quota di core per consentire a Batch di allocare più nodi.
- Indirizzi IP di subnet insufficienti quando un [pool è in una rete virtuale](./batch-virtual-network.md)
  - Una subnet di rete virtuale deve disporre di un numero sufficiente di indirizzi IP non assegnati da allocare a ogni nodo di pool richiesto. In caso contrario non è possibile creare i nodi.
- Risorse insufficienti quando un [pool è in una rete virtuale](./batch-virtual-network.md)
  - Se si creano risorse come servizi di bilanciamento del carico, IP pubblici e gruppi di sicurezza di rete nella stessa sottoscrizione dell'account Batch, verificare che le quote della sottoscrizione siano sufficienti per queste risorse.
- Pool di grandi dimensioni con immagini di macchina virtuale personalizzate
  - L'allocazione di pool di grandi dimensioni che usano immagini di macchina virtuale personalizzate può richiedere più tempo, pertanto possono verificarsi timeout di ridimensionamento.  Vedere [Creare un pool con Raccolta immagini condivise](batch-sig-images.md) per consigli su limiti e configurazione.

### <a name="automatic-scaling-failures"></a>Errori di ridimensionamento automatico

È possibile impostare Azure Batch per ridimensionare automaticamente il numero di nodi in un pool. A questo scopo occorre definire i parametri per la [formula di ridimensionamento automatico per un pool](./batch-automatic-scaling.md). Il servizio batch utilizzerà quindi la formula per valutare periodicamente il numero di nodi nel pool e impostare un nuovo numero di destinazione.

Quando si usa il ridimensionamento automatico, possono verificarsi i tipi di problemi seguenti:

- La valutazione del ridimensionamento automatico non riesce.
- L'operazione di ridimensionamento risultante non riesce con conseguente timeout.
- Un problema nella formula di ridimensionamento automatico genera valori di destinazione dei nodi non corretti. Il ridimensionamento riesce oppure raggiunge il timeout.

Per ottenere informazioni sull'ultima valutazione di scalabilità automatica, usare la proprietà [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) . Questa proprietà indica il periodo della valutazione, i valori e il risultato, oltre agli eventuali errori di prestazioni.

L'[evento di completamento del ridimensionamento del pool](./batch-pool-resize-complete-event.md) acquisisce le informazioni su tutte le valutazioni.

### <a name="pool-deletion-failures"></a>Errori di eliminazione del pool

Quando si elimina un pool che contiene nodi, Batch elimina prima di tutto i nodi, Questa operazione può richiedere alcuni minuti. Successivamente, batch Elimina l'oggetto pool stesso.

Lo [stato del pool](/rest/api/batchservice/pool/get#poolstate) viene impostato su **deleting** durante il processo di eliminazione. L'applicazione chiamante può rilevare se l'eliminazione del pool richiede troppo tempo usando le proprietà **state** e **stateTransitionTime**.

## <a name="node-errors"></a>Errori del nodo

Anche se Batch alloca correttamente i nodi in un pool, vari problemi possono compromettere l'integrità di alcuni nodi e impedire l'esecuzione di attività. Questi nodi continuano a prevedere un addebito, per cui è importante rilevare i problemi per evitare di pagare per i nodi che non possono essere usati. Oltre agli errori comuni relativi ai nodi, conoscere lo [stato del processo](/rest/api/batchservice/job/get#jobstate) corrente è utile per la risoluzione dei problemi.

### <a name="start-task-failures"></a>Errori dell'attività di avvio

È possibile specificare un'[attività di avvio](/rest/api/batchservice/pool/add#starttask) facoltativa per un pool. Come per qualsiasi attività, è possibile usare una riga di comando e file di risorse da scaricare dallo spazio di archiviazione. Una volta avviata, l'attività di avvio viene eseguita per ogni nodo. La proprietà **waitForSuccess** specifica se Batch deve attendere il corretto completamento dell'attività di avvio prima di pianificare qualsiasi attività su un nodo.

Se il nodo è stato configurato in modo da attendere che l'attività di avvio venga completata correttamente, ma invece questa non riesce, In tal caso, il nodo non sarà utilizzabile, ma continuerà ad essere addebitato.

È possibile rilevare errori nell'attività di avvio mediante le proprietà [result](/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) della proprietà del nodo [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) di primo livello.

Se un'attività di avvio non riesce, inoltre, Batch imposta lo [stato](/rest/api/batchservice/computenode/get#computenodestate) del nodo su **starttaskfailed**, se la proprietà **waitForSuccess** è impostata su **true**.

Come per qualsiasi attività, è possibile che si verifichino molte cause per un errore di avvio dell'attività. Per risolvere il problema, controllare i file stdout, stderr e qualsiasi altro file di log specifico dell'attività.

Le attività di avvio devono essere rientranti, perché è possibile che l'attività di avvio venga eseguita più volte sullo stesso nodo; l'attività di avvio viene eseguita quando viene ricreata l'immagine di un nodo o questo viene riavviato. In casi rari, viene eseguita un'attività di avvio dopo un evento che ha causato il riavvio di un nodo, in cui è stata ricreata l'immagine di uno solo tra il disco del sistema operativo e quello temporaneo. Poiché le attività di avvio di Batch, come tutte le attività di Batch, vengono eseguite dal disco temporaneo, non si tratta in genere di un problema, ma in alcuni casi in cui l'attività di avvio installa un'applicazione nel disco del sistema operativo e mantiene altri dati sul disco temporaneo, ciò può causare problemi perché gli elementi non sono sincronizzati. Proteggere l'applicazione di conseguenza se si usano entrambi i dischi.

### <a name="application-package-download-failure"></a>Errore di download del pacchetto dell'applicazione

È possibile specificare uno o più pacchetti dell'applicazione per un pool. Batch scarica i file di pacchetto specificati in ogni nodo e li decomprime dopo l'avvio del nodo ma prima della pianificazione delle attività. È prassi comune usare la riga di comando di un'attività di avvio insieme ai pacchetti dell'applicazione, ad esempio per copiare file n una posizione diversa o per eseguire il programma di installazione.

La proprietà [Errors](/rest/api/batchservice/computenode/get#computenodeerror) del nodo indica un errore di download e decompressione di un pacchetto dell'applicazione. Lo stato del nodo è impostato su **Inutilizzabile**.

### <a name="container-download-failure"></a>Errore di download del contenitore

È possibile specificare uno o più riferimenti a contenitori in un pool. Batch scarica i contenitori specificati in ogni nodo. La proprietà [Errors](/rest/api/batchservice/computenode/get#computenodeerror) del nodo segnala un errore di download di un contenitore e imposta lo stato del nodo su **Inutilizzabile**.

### <a name="node-os-updates"></a>Aggiornamenti del sistema operativo del nodo

Per i pool di Windows, `enableAutomaticUpdates` è impostato su per `true` impostazione predefinita. È consigliabile consentire gli aggiornamenti automatici, ma è possibile interrompere l'avanzamento dell'attività, soprattutto se le attività hanno un'esecuzione prolungata. È possibile impostare questo valore su `false` se è necessario assicurarsi che un aggiornamento del sistema operativo non avvenga in modo imprevisto.

### <a name="node-in-unusable-state"></a>Nodo in stato inutilizzabile

Azure Batch può impostare lo [stato del nodo](/rest/api/batchservice/computenode/get#computenodestate) su **unusable** per diversi motivi. Quando lo stato del nodo è impostato su **unusable**, non è possibile pianificare attività sul nodo, il quale però rimane soggetto ad addebiti.

I nodi in stato **Inutilizzabile** ma senza [errori](/rest/api/batchservice/computenode/get#computenodeerror) indicano che Batch non è in grado di comunicare con la macchina virtuale. In questo caso, Batch tenta sempre di recuperare la macchina virtuale. Batch non tenterà automaticamente di recuperare le macchine virtuali in cui non è stato possibile installare pacchetti dell'applicazione o contenitori, anche se lo stato è **Inutilizzabile**.

Se Batch è in grado di determinare la causa, questa viene indicata nella proprietà [errors](/rest/api/batchservice/computenode/get#computenodeerror).

Altri esempi di cause di nodi inutilizzabili, con stato **unusable**, includono:

- Un'immagine di macchina virtuale personalizzata non è valida. È il caso ad esempio di un'immagine preparata in modo non corretto.

- Una macchina virtuale viene spostata a causa di un errore di infrastruttura o un aggiornamento di basso livello. Batch recupera il nodo.

- Un'immagine della macchina virtuale è stata distribuita nell'hardware, che non la supporta. Un esempio è quando si tenta di eseguire un'immagine HPC CentOS in una macchina virtuale [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md).

- Le macchine virtuali si trovano in una [rete virtuale di Azure](batch-virtual-network.md) e il traffico è stato bloccato sulle porte chiave.

- Le macchine virtuali si trovano in una rete virtuale, ma il traffico in uscita verso l'archiviazione di Azure è bloccato.

- Le macchine virtuali si trovano in una rete virtuale con una configurazione DNS del cliente e il server DNS non riesce a risolvere l'archiviazione di Azure.

### <a name="node-agent-log-files"></a>File di log dell'agente del nodo

Il processo dell'agente Batch in esecuzione in ogni nodo del pool può fornire file di log che potrebbero risultare utili se occorre contattare il supporto in relazione a un problema in un nodo del pool. I file di log relativi a un nodo possono essere caricati tramite il portale di Azure, Batch Explorer o un'[API](/rest/api/batchservice/computenode/uploadbatchservicelogs). È utile caricare e salvare i file di log. In seguito è possibile eliminare il nodo o il pool per evitare un addebito per i nodi in esecuzione.

### <a name="node-disk-full"></a>Disco del nodo pieno

L'unità temporanea per una macchina virtuale del nodo del pool viene utilizzata da batch per i file di processo, i file delle attività e i file condivisi, come nel seguente esempio:

- File dei pacchetti dell'applicazione
- File di risorse dell'attività
- File specifici dell'applicazione scaricati in una delle cartelle di Batch
- File stdout e stderr per ogni esecuzione dell'applicazione attività
- File di output specifici dell'applicazione

Alcuni di questi file vengono scritti una sola volta alla creazione dei nodi del pool, ad esempio i pacchetti dell'applicazione del pool o i file di risorse dell'attività di avvio del pool. Anche se questi file sono scritti una sola volta alla creazione del nodo, se troppo grandi potrebbero riempire l'unità temporanea.

Altri file vengono scritti per ogni attività eseguita in un nodo, ad esempio stdout e stderr. Se un numero elevato di attività viene eseguito sullo stesso nodo e/o se i file delle attività sono troppo grandi, è possibile che riempiano l'unità temporanea.

Le dimensioni dell'unità temporanea dipendono dalle dimensioni della macchina virtuale. Quando si selezionano le dimensioni di una macchina virtuale è buona norma assicurarsi che lo spazio disponibile nell'unità temporanea sia sufficiente.

- Nel portale di Azure, durante l'aggiunta di un pool è possibile visualizzare l'elenco completo delle dimensioni delle macchine virtuali ed è presente una colonna "Dimensioni disco risorsa".
- Gli articoli che descrivono tutte le dimensioni delle macchine virtuali contengono tabelle con una colonna "Archiviazione temporanea", ad esempio [Dimensioni delle macchine virtuali con ottimizzazione per il calcolo](../virtual-machines/sizes-compute.md)

Per i file scritti dalle singole attività, è possibile specificare un periodo di conservazione per ogni attività, che determini per quanto tempo i file dell'attività debbano essere conservati prima di essere eliminati automaticamente. Il tempo di conservazione può essere ridotto per ridurre i requisiti di archiviazione.

Se lo spazio sul disco temporaneo si esaurisce (o quasi), il nodo passerà allo stato [Inutilizzabile](/rest/api/batchservice/computenode/get#computenodestate) e verrà segnalato un errore del nodo per informare che il disco è pieno.

Se non si è certi di cosa sta occupando spazio nel nodo, provare a eseguire la comunicazione remota al nodo ed esaminare manualmente la posizione in cui lo spazio è andato. È anche possibile usare l'[API dei file di elenco processi batch](/rest/api/batchservice/file/listfromcomputenode) per esaminare i file nelle cartelle gestite da Batch (ad esempio, gli output delle attività). Si noti che questa API elenca solo i file nelle directory gestite da batch. Se le attività hanno creato file altrove, non verranno visualizzati.

Assicurarsi che tutti i dati necessari siano stati recuperati dal nodo o caricati in un archivio durevole, quindi eliminare i dati necessari per liberare spazio.

È possibile eliminare i vecchi processi completati o le precedenti attività completate, i cui dati attività sono ancora presenti nei nodi. Esaminare la [raccolta RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) nel nodo o nei [file del nodo](/rest/api/batchservice/file/listfromcomputenode). L'eliminazione di un processo comporterà l'eliminazione di tutte le attività nel processo. l'eliminazione delle attività nel processo attiverà i dati nelle directory delle attività nel nodo da eliminare, liberando così spazio. Dopo avere liberato spazio sufficiente, riavviare il nodo, che uscirà dallo stato "Inutilizzabile" per tornare a quello "Inattivo".

Per ripristinare un nodo inutilizzabile nei pool [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) , è possibile rimuovere un nodo dal pool usando l' [API Remove nodes](/rest/api/batchservice/pool/removenodes). Sarà quindi possibile aumentare di nuovo il pool per sostituire il nodo errato con uno aggiornato. Per i pool [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) è possibile ricreare l'immagine del nodo tramite l' [API di rielaborazione batch](/rest/api/batchservice/computenode/reimage). L'intero disco verrà pulito. La nuova creazione dell'immagine non è attualmente supportata per i pool [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [controllo degli errori di attività e processi](batch-job-task-error-checking.md).
- Informazioni sulle [procedure](best-practices.md) consigliate per l'utilizzo di Azure batch.
