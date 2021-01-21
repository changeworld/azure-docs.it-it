---
title: Informazioni sulla fatturazione File di Azure | Microsoft Docs
description: Informazioni su come interpretare i modelli di fatturazione con pagamento in base al consumo per le condivisioni file di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/20/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 19ecbea70d9cb6b8cc31c72ed3c1294cd137ce93
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632479"
---
# <a name="understanding-azure-files-billing"></a>Informazioni sulla fatturazione File di Azure
File di Azure offre due modelli di fatturazione distinti: con provisioning e con pagamento in base al consumo. Il modello di cui è stato effettuato il provisioning è disponibile solo per le condivisioni file Premium, ovvero le condivisioni file distribuite nel tipo di account di archiviazione **filestorage** . Il modello con pagamento in base al consumo è disponibile solo per le condivisioni file standard, ovvero le condivisioni file distribuite nel tipo di account di archiviazione per **utilizzo generico versione 2 (GPv2)** . Questo articolo illustra il funzionamento di entrambi i modelli per facilitare la comprensione della fattura mensile File di Azure.

I prezzi attuali per File di Azure sono disponibili nella pagina relativa ai [prezzi file di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="provisioned-model"></a>Modello sottoposta a provisioning
File di Azure usa un modello con provisioning per le condivisioni file Premium. In un modello di business di cui è stato effettuato il provisioning, è possibile specificare in modo proattivo il File di Azure servizio che i requisiti di archiviazione sono, anziché essere fatturati in base a ciò che si usa. Questa operazione è simile all'acquisto di hardware in locale, in quanto quando si effettua il provisioning di una condivisione file di Azure con una determinata quantità di spazio di archiviazione, si paga per tale spazio di archiviazione, indipendentemente dal fatto che si usi o meno, così come non si inizia a pagare i costi dei supporti fisici locali quando si inizia a usare lo spazio. Diversamente dall'acquisto di supporti fisici in locale, le condivisioni file di cui è stato effettuato il provisioning possono essere aumentate o ridotte in modo dinamico a seconda delle caratteristiche delle prestazioni di archiviazione e i/o.

Quando si esegue il provisioning di una condivisione file Premium, si specifica il numero di GiBs necessari per il carico di lavoro. Ogni GiB di cui viene effettuato il provisioning consente di eseguire operazioni di IOPS e velocità effettiva aggiuntive su un rapporto fisso. Oltre alle operazioni di i/o al secondo per cui è garantita la garanzia, ogni condivisione file Premium supporta il prorompersi in base al massimo sforzo. Le formule per IOPS e velocità effettiva sono le seguenti:

- IOPS Baseline = 400 + 1 * GiB con provisioning. (Fino a un massimo di 100.000 IOPS).
- Limite di espansione = MAX (4000, 3 * IOPS baseline).
- Velocità in uscita = 60 MiB/s + 0,06 * GiB con provisioning.
- Velocità in ingresso = 40 MiB/s + 0,04 * GiB con provisioning.

Le dimensioni di provisioning della condivisione file possono essere aumentate in qualsiasi momento, ma possono essere diminuite solo dopo 24 ore dall'ultimo aumento. Dopo aver atteso 24 ore senza un aumento della quota, è possibile ridurre la quota di condivisione tutte le volte che si desidera, fino a quando non si aumenta nuovamente. Le modifiche alla scalabilità IOPS/velocità effettiva saranno effettive entro pochi minuti dopo la modifica delle dimensioni di cui è stato effettuato il provisioning.

È possibile ridurre le dimensioni della condivisione di cui è stato effettuato il provisioning sotto il GiB usato. In tal caso, non si perderanno i dati, ma verranno comunque addebitate le dimensioni usate e si riceveranno le prestazioni (IOPS di base, velocità effettiva e IOPS di aumento) della condivisione di cui è stato effettuato il provisioning, non le dimensioni usate.

La tabella seguente illustra alcuni esempi di queste formule per le dimensioni della condivisione di cui è stato effettuato il provisioning:

|Capacità (GiB) | Operazioni di I/O al secondo di base | IOPS a impulsi | In uscita (MiB/s) | Ingresso (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Fino a 4.000     | 66   | 44   |
|500         | 900     | Fino a 4.000  | 90   | 60   |
|1\.024       | 1.424   | Fino a 4.000   | 122   | 81   |
|5120       | 5.520   | Fino a 15.360  | 368   | 245   |
|10.240      | 10.640  | Fino a 30.720  | 675   | 450   |
|33.792      | 34.192  | Fino a 100.000 | 2.088 | 1.392   |
|51.200      | 51.600  | Fino a 100.000 | 3.132 | 2.088   |
|102.400     | 100,000 | Fino a 100.000 | 6.204 | 4.136   |

Le prestazioni effettive della condivisione file sono soggette ai limiti della rete del computer, alla larghezza di banda di rete disponibile, alle dimensioni di i/o e al parallelismo, tra Ad esempio, in base a test interni con 8 dimensioni di i/o di lettura/scrittura, una singola macchina virtuale Windows senza SMB multicanale abilitato, *F16s_v2 standard*, connessa alla condivisione file Premium tramite SMB, potrebbe ottenere 20 IOPS di lettura e 15.000 di scrittura. Con le dimensioni di i/o in lettura/scrittura di 512 MiB, la stessa VM potrebbe ottenere 1,1 GiB/s in uscita e 370 MiB/s di velocità effettiva in ingresso. Lo stesso client può ottenere fino a \~ 3x prestazioni se SMB multicanale è abilitato nelle condivisioni Premium. Per ottenere la massima scalabilità delle prestazioni, [abilitare SMB multicanale](storage-files-enable-smb-multichannel.md) e distribuire il carico tra più macchine virtuali. Per alcuni problemi comuni di prestazioni e soluzioni alternative, vedere Guida alle prestazioni e alla [risoluzione dei problemi](storage-troubleshooting-files-performance.md) di [SMB multicanale](storage-files-smb-multichannel-performance.md) .

### <a name="bursting"></a>Espansione nel
Se il carico di lavoro richiede le prestazioni aggiuntive per soddisfare i picchi della domanda, la condivisione può usare i crediti di espansione per superare il limite di IOPS della linea di base per offrire le prestazioni di condivisione necessarie per soddisfare la domanda. Le condivisioni file Premium possono aumentare il numero di IOPS fino a 4.000 o fino a un fattore pari a tre, a seconda del valore più alto. L'espansione è automatizzata e funziona in base a un sistema di credito. Il processo di prolungamento funziona in base al massimo sforzo e il limite di espansione non è una garanzia, le condivisioni file possono *aumentare fino* al limite per una durata massima di 60 minuti.

I crediti si accumulano in un bucket di espansione ogni volta che il traffico per la condivisione file è inferiore al valore di IOPS Baseline. Una condivisione GiB 100, ad esempio, ha 500 IOPS Baseline. Se il traffico effettivo nella condivisione è 100 IOPS per un intervallo di 1 secondo specifico, le operazioni di 400 i/o al secondo non usate vengono accreditate a un bucket di espansione. Analogamente, la condivisione 1 TiB inattiva aumenta il credito di picco a 1.424 IOPS. Questi crediti verranno quindi usati in seguito quando le operazioni superano il IOPS di base.

Ogni volta che una condivisione supera le operazioni di i/o al secondo e ha crediti in un bucket di espansione, il numero massimo di picchi consentito è elevato. Le condivisioni possono continuare a essere interrotte fino a quando i crediti restano, fino a un massimo di 60 minuti di durata, ma si basano sul numero di crediti di espansione accumulati. Ogni i/o oltre le operazioni di i/o al secondo di base utilizza un credito e una volta che tutti i crediti vengono utilizzati, la condivisione tornerà al IOPS di base.

I crediti di condivisione hanno tre stati:

- Accumulo, quando la condivisione file usa un numero di IOPS inferiore a quello di base.
- In declino, quando la condivisione file usa più di IOPS di base e in modalità di espansione.
- Costante, quando la condivisione file usa esattamente la linea di base IOPS, non sono presenti crediti accumulati o usati.

Le nuove condivisioni file iniziano con il numero completo di crediti nel bucket di espansione. Se i valori di IOPS della condivisione scendono al di sotto della linea di base IOPS a causa della limitazione del server, i crediti di espansione non verranno acquisiti.

## <a name="pay-as-you-go-model"></a>Modello con pagamento in base al consumo
File di Azure usa un modello di business con pagamento in base al consumo per le condivisioni file standard. In un modello di business con pagamento in base al consumo, l'importo pagato è determinato dalla quantità effettivamente usata, anziché in base a una quantità di cui è stato effettuato il provisioning. A un livello elevato, si paga un costo per la quantità di dati archiviati su disco e quindi un set aggiuntivo di transazioni in base all'utilizzo di tali dati. Un modello con pagamento in base al consumo può essere conveniente, perché non è necessario eseguire l'overprovisioning per tenere conto dei requisiti di crescita o di prestazioni futuri o effettuare il deprovisioning se il carico di lavoro è un footprint di dati diverso nel tempo. D'altra parte, un modello con pagamento in base al consumo può anche essere difficile da pianificare come parte di un processo di budget, perché il modello di fatturazione con pagamento in base al consumo è determinato dal consumo dell'utente finale.

### <a name="differences-in-standard-tiers"></a>Differenze nei livelli standard
Quando si crea una condivisione file standard, è possibile scegliere tra i livelli di transazione ottimizzata, ad accesso frequente e ad accesso sporadico. Tutti e tre i livelli vengono archiviati nello stesso hardware di archiviazione standard. La differenza principale per questi tre livelli è rappresentata dai prezzi di archiviazione dei dati inattivi, che sono più bassi nei livelli ad accesso più sporadico, e dai prezzi delle transazioni, che sono più elevati nei livelli ad accesso più sporadico. Ciò significa:

- Il livello ottimizzato per le transazioni, come suggerisce il nome, ottimizza il prezzo per i carichi di lavoro con un numero di transazioni elevato. A questo livello sono associati i prezzi più alti per l'archiviazione dei dati inattivi, ma i prezzi più bassi per le transazioni.
- Il livello di archiviazione ad accesso frequente è per i carichi di lavoro attivi che non comportano un numero elevato di transazioni e ha un prezzo leggermente inferiore per l'archiviazione dei dati inattivi, ma prezzi leggermente più elevati per le transazioni rispetto al livello ottimizzato per le transazioni. È possibile considerarlo come una base intermedia tra la transazione ottimizzata e i livelli di accesso sporadico.
- Cool ottimizza il prezzo per i carichi di lavoro che non hanno molta attività, offrendo il prezzo di archiviazione dei dati inattivi più basso, ma i prezzi di transazione più elevati.

Se si inserisce un carico di lavoro a cui si accede raramente nel livello di transazione ottimizzata, si paga quasi nulla per alcune volte in un mese in cui si effettuano transazioni sulla propria condivisione, ma si pagherà un importo elevato per i costi di archiviazione dei dati. Se fosse necessario spostare la stessa condivisione nel livello di accesso sporadico, si pagherebbe praticamente nulla per i costi delle transazioni, semplicemente perché le transazioni per questo carico di lavoro non sono di frequente, ma il livello di accesso sporadico presenta un prezzo di archiviazione dati molto più economico. Selezionando il livello appropriato per il caso d'uso, è possibile ridurre notevolmente i costi. Selezionando il livello appropriato per il caso d'uso, è possibile ridurre notevolmente i costi.

Analogamente, se si inserisce un carico di lavoro con accesso elevato nel livello di accesso sporadico, si pagheranno molto più nei costi delle transazioni, ma meno per i costi di archiviazione dei dati. Ciò può comportare una situazione in cui i costi maggiori dei prezzi delle transazioni aumentano rispetto ai risparmi ottenuti dal prezzo di archiviazione dei dati diminuito, consentendo di pagare più denaro in modo interessante rispetto a quello che si otterrebbe per la transazione ottimizzata. Per alcuni livelli di utilizzo è possibile che, mentre il livello di accesso frequente sarà il livello più conveniente, il livello di accesso sporadico sarà più costoso della transazione ottimizzata.

Il carico di lavoro e il livello di attività determinano il livello più vantaggioso in termini di costo per la condivisione file standard. In pratica, il modo migliore per scegliere il livello più conveniente consiste nell'esaminare il consumo effettivo delle risorse della condivisione (dati archiviati, transazioni di scrittura e così via).

### <a name="what-are-transactions"></a>Che cosa sono le transazioni?
Le transazioni sono operazioni o richieste rispetto a File di Azure per caricare, scaricare o modificare in altro modo il contenuto della condivisione file. Ogni azione eseguita su una condivisione file si traduce in una o più transazioni e nelle condivisioni standard che usano il modello di fatturazione con pagamento in base al consumo, che si traduce in costi di transazione.

Sono disponibili cinque categorie di transazioni di base: Write, List, Read, other ed Delete. Tutte le operazioni eseguite tramite l'API REST o SMB vengono suddivise in una delle quattro categorie seguenti:

| Tipo di operazione | Transazioni di scrittura | Elencare le transazioni | Transazioni di lettura | Altre transazioni | Elimina transazioni |
|-|-|-|-|-|-|
| Operazioni di gestione | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operazioni sui dati | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4,1 è disponibile solo per le condivisioni file Premium, che usano il modello di fatturazione con provisioning, le transazioni non influiscono sulla fatturazione per le condivisioni file Premium.

## <a name="see-also"></a>Vedi anche
- [File di Azure pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).
- [Pianificazione di una distribuzione di file di Azure](./storage-files-planning.md) e [pianificazione di una distribuzione di sincronizzazione file di Azure](./storage-sync-files-planning.md).
- [Creare una condivisione file](./storage-how-to-create-file-share.md) e [distribuire Sincronizzazione file di Azure](./storage-sync-files-deployment-guide.md).