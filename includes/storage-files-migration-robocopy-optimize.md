---
title: includere file
description: includere file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491680"
---
La velocità e la percentuale di successo di una determinata esecuzione di RoboCopy dipendono da diversi fattori:

* IOPS nell'archiviazione di origine e di destinazione
* larghezza di banda di rete disponibile tra l'origine e la destinazione
* possibilità di elaborare rapidamente file e cartelle in uno spazio dei nomi
* numero di modifiche tra le esecuzioni di RoboCopy


### <a name="iops-and-bandwidth-considerations"></a>Considerazioni su IOPS e larghezza di banda

In questa categoria è necessario prendere in considerazione le capacità dell' **archiviazione di origine**, dell' **archiviazione di destinazione** e della **rete** che la connette. La velocità effettiva massima possibile è determinata dal più lento di questi tre componenti. Assicurarsi che l'infrastruttura di rete sia configurata in modo da supportare velocità di trasferimento ottimali alle proprie capacità ottimali.

> [!CAUTION]
> Mentre la copia più veloce possibile è spesso più auspicabile, considerare l'utilizzo della rete locale e del dispositivo NAS per altre attività aziendali cruciali.

La copia più veloce possibile potrebbe non essere auspicabile quando esiste il rischio che la migrazione possa monopolizzare le risorse disponibili.

* Prendere in considerazione quando è più adatto all'ambiente per eseguire le migrazioni: durante il giorno, negli orari non lavorativi o durante i fine settimana.
* Prendere in considerazione anche la funzionalità QoS di rete in un server Windows per limitare la velocità di RoboCopy.
* Evitare il lavoro necessario per gli strumenti di migrazione.

RobCopy può inserire ritardi tra pacchetti specificando l' `/IPG:n` opzione in cui `n` viene misurato in millisecondi tra i pacchetti Robocopy. L'uso di questa opzione consente di evitare la monopolizzazione delle risorse sia nei dispositivi con vincoli IO che nei collegamenti di rete affollati.

`/IPG:n` non può essere usato per una limitazione di rete specifica a una determinata Mbps. Usare invece QoS di rete di Windows Server. RoboCopy si basa interamente sul protocollo SMB per tutte le esigenze di rete. L'uso di SMB è il motivo per cui RoboCopy non può influenzare la velocità effettiva della rete stessa, ma può rallentarne l'uso. 

Una linea di pensiero simile si applica ai IOPS osservati sul NAS. Le dimensioni del cluster nel volume NAS, le dimensioni dei pacchetti e una matrice di altri fattori influiscono sui IOPS osservati. L'introduzione del ritardo tra pacchetti è spesso il modo più semplice per controllare il carico sul NAS. Testare più valori, ad esempio da circa 20 millisecondi (n = 20) a multipli di tale numero. Una volta introdotto un ritardo, è possibile valutare se le altre app possono ora funzionare come previsto. Questa strategia di ottimizzazione consente di trovare la velocità di RoboCopy ottimale nell'ambiente in uso.

### <a name="processing-speed"></a>Velocità di elaborazione

RoboCopy attraverserà lo spazio dei nomi a cui punta e valuta ogni file e cartella per la copia. Ogni file verrà valutato durante una copia iniziale e durante le copie di recupero. Ad esempio, esecuzioni ripetute di RoboCopy/MIR con le stesse posizioni di archiviazione di origine e di destinazione. Queste esecuzioni ripetute sono utili per ridurre al minimo i tempi di inattività per utenti e app e per migliorare la percentuale complessiva di esecuzioni di file migrate.

Per impostazione predefinita, spesso si considera la larghezza di banda come fattore più limitato in una migrazione, che può essere true. Tuttavia, la possibilità di enumerare uno spazio dei nomi può influire sul tempo totale di copia ancora maggiore per spazi dei nomi più grandi con file più piccoli. Si consideri che la copia di 1 TiB di file di piccole dimensioni può richiedere molto più tempo rispetto alla copia di un TiB di un numero inferiore di file Supponendo che tutte le altre variabili rimangano invariate.

La ragione di questa differenza è la potenza di elaborazione necessaria per esaminare uno spazio dei nomi. RoboCopy supporta le copie multithread tramite il `/MT:n` parametro, dove n corrisponde al numero di thread del processore. Quindi, quando si esegue il provisioning di un computer specifico per RoboCopy, prendere in considerazione il numero di core del processore e la relativa relazione con il conteggio dei thread forniti. Più comuni sono due thread per core. Il numero di core e thread di un computer è un punto dati importante per decidere quali valori multithread `/MT:n` è necessario specificare. Prendere in considerazione anche il numero di processi RoboCopy che si prevede di eseguire in parallelo in un determinato computer.

Un numero maggiore di thread copia l'esempio 1-TiB di file di piccole dimensioni notevolmente più veloce rispetto a un numero inferiore di thread. Allo stesso tempo, l'investimento aggiuntivo di risorse in 1 TiB di file di dimensioni maggiori non può produrre vantaggi proporzionali. Un numero elevato di thread tenterà di copiare contemporaneamente più file di grandi dimensioni sulla rete. Questa attività di rete aggiuntiva aumenta la probabilità di vincolare la velocità effettiva o le operazioni di i/o al secondo di archiviazione.

### <a name="avoid-unnecessary-work"></a>Evitare il lavoro non necessario

Evitare modifiche su larga scala nello spazio dei nomi. Ad esempio, lo scambio di file tra directory, la modifica delle proprietà su larga scala o la modifica delle autorizzazioni (ACL NTFS). In particolare, le modifiche ACL possono avere un impatto elevato perché spesso hanno un effetto di modifica a cascata sui file più in basso nella gerarchia di cartelle. Le conseguenze possono essere:

* tempo di esecuzione del processo RoboCopy esteso perché è necessario aggiornare ogni file e cartella interessati da una modifica ACL
* potrebbe essere necessario ricopiare il riutilizzo dei dati spostati in precedenza. Ad esempio, è necessario copiare più dati quando si modificano le strutture delle cartelle dopo che i file sono già stati copiati in precedenza. Un processo RoboCopy non può eseguire una modifica dello spazio dei nomi. Il processo successivo deve eliminare i file precedentemente trasportati nella struttura di cartelle precedente e caricare nuovamente i file nella nuova struttura di cartelle.

Un altro aspetto importante consiste nell'utilizzare lo strumento RoboCopy in modo efficace. Con lo script RoboCopy consigliato è possibile creare e salvare un file di log per individuare eventuali errori. Possono verificarsi errori di copia. questa situazione è normale. Questi errori spesso rendono necessario eseguire più cicli di uno strumento di copia, ad esempio RoboCopy. Un'esecuzione iniziale, ad DataBox o a una condivisione file di Azure. E una o più esecuzioni aggiuntive con l'opzione/MIR per intercettare e ritentare i file che non vengono copiati.

È necessario essere pronti a eseguire più cicli di RoboCopy rispetto a un determinato ambito dello spazio dei nomi. Le esecuzioni successive verranno completate più velocemente poiché hanno meno copia, ma sono vincolate sempre più dalla velocità di elaborazione dello spazio dei nomi. Quando si eseguono più cicli, è possibile velocizzare ogni round senza che RoboCopy provi a eseguire la copia di tutti gli elementi in una determinata esecuzione. Questi commutatori RoboCopy possono avere una differenza significativa:

* `/R:n` n = frequenza con cui si tenta di copiare un file non riuscito e 
* `/W:n` n = numero di secondi di attesa tra i tentativi

`/R:5 /W:5` è un'impostazione ragionevole che è possibile adattare ai propri gusti. In questo esempio, verrà eseguito un nuovo tentativo di esecuzione di un file con errori cinque volte, con tempo di attesa di cinque secondi tra i tentativi. Se il file non viene ancora copiato, il processo RoboCopy successivo riproverà. Spesso i file non riusciti perché sono in uso o a causa di problemi di timeout potrebbero essere copiati correttamente in questo modo.
   
