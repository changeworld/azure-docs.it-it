---
title: Informazioni File di Azure fatturazione | Microsoft Docs
description: Informazioni su come interpretare i modelli di fatturazione con provisioning e con pagamento in base al prezzo per le condivisioni file di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788386"
---
# <a name="understand-azure-files-billing"></a>Informazioni File di Azure fatturazione
File di Azure due modelli di fatturazione distinti: con provisioning e con pagamento in base al go. Il modello di cui è stato effettuato il provisioning è disponibile solo per le condivisioni file Premium, ovvero condivisioni file distribuite nel tipo di account di archiviazione **FileStorage.** Il modello con pagamento in base al costo è disponibile solo per le condivisioni file standard, ovvero condivisioni file distribuite nel tipo di account di archiviazione per utilizzo generico **versione 2 (GPv2).** Questo articolo illustra il funzionamento di entrambi i modelli per facilitare la comprensione della fattura mensile File di Azure fatturazione.

Per File di Azure sui prezzi, vedere File di Azure [prezzi.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="storage-units"></a>Unità di archiviazione    
File di Azure usa unità di misura in base 2 per rappresentare la capacità di archiviazione: KiB, MiB, GiB e TiB. Il sistema operativo può usare o meno la stessa unità di misura o sistema di conteggio.

### <a name="windows"></a>Windows

Sia il sistema operativo Windows che File di Azure misurano la capacità di archiviazione usando il sistema di conteggio in base 2, ma esiste una differenza nell'etichettatura delle unità. File di Azure la capacità di archiviazione con unità di misura in base 2, mentre Windows etichetta la capacità di archiviazione in unità di misura in base 10. Quando si segnala la capacità di archiviazione, Windows non converte la capacità di archiviazione da base 2 a base 10.

|Acronimo  |Definizione  |Unità  |Windows viene visualizzato come  |
|---------|---------|---------|---------|
|Kib     |1.024 byte         |kibibyte         |KB (kilobyte)         |
|MiB     |1.024 KiB (1.048.576 byte)         |mebibyte         |MB (megabyte)         |
|GiB     |1024 MiB (1.073.741.824 byte)         |gibibyte         |GB (gigabyte)         |
|Tib     |1024 GiB (1.099.511.627.776 byte)         |Tebibyte         |TB (terabyte)         |

### <a name="macos"></a>macOS

Per [determinare il sistema di conteggio](https://support.apple.com/HT201402) usato, vedere Come iOS e macOS segnalano la capacità di archiviazione nel sito Web di Apple.

### <a name="linux"></a>Linux

Un sistema di conteggio diverso può essere usato da ogni sistema operativo o singolo componente software. Vedere la documentazione per determinare il modo in cui segnalano la capacità di archiviazione.

## <a name="provisioned-model"></a>Modello con provisioning
File di Azure usa un modello con provisioning per le condivisioni file Premium. In un modello aziendale con provisioning, è possibile specificare in modo proattivo al servizio File di Azure quali sono i requisiti di archiviazione, anziché essere fatturati in base a ciò che si usa. È simile all'acquisto di hardware in locale, in quanto quando si effettua il provisioning di una condivisione file di Azure con una determinata quantità di spazio di archiviazione, si paga per tale archiviazione indipendentemente dal fatto che lo si usi o meno, proprio come non si inizia a pagare i costi dei supporti fisici in locale quando si inizia a usare lo spazio. A differenza dell'acquisto di supporti fisici in locale, le condivisioni file di cui è stato effettuato il provisioning possono essere ridimensionate in modo dinamico a seconda delle caratteristiche di archiviazione e delle prestazioni di I/O.

Quando si effettua il provisioning di una condivisione file Premium, si specifica il numero di GiB necessari per il carico di lavoro. Ogni GiB di cui si effettua il provisioning consente di aumentare le operazioni di I/O al secondo e la velocità effettiva in base a un rapporto fisso. Oltre alle operazioni di I/O al secondo di base per cui si è garantiti, ogni condivisione file Premium supporta il bursting in base al massimo sforzo. Le formule per le operazioni di I/O al secondo e la velocità effettiva sono le seguenti:

- Operazioni di I/O al secondo di base = 400 + 1 * GiB con provisioning. (fino a un massimo di 100.000 operazioni di I/O al secondo).
- Limite burst = MAX(4000, 3 * IOPS di base).
- Velocità in uscita = 60 MiB/s + 0,06 * GiB con provisioning.
- Velocità in ingresso = 40 MiB/s + 0,04 * GiB con provisioning.

Le dimensioni di cui è stato effettuato il provisioning della condivisione file possono essere aumentate in qualsiasi momento, ma possono essere ridotte solo dopo 24 ore dall'ultimo aumento. Dopo aver atteso 24 ore senza un aumento della quota, è possibile ridurre la quota di condivisione tutte le volte che si desidera, fino a quando non si aumenta di nuovo. Le modifiche alla scalabilità delle operazioni di I/O al secondo/velocità effettiva saranno effettive entro pochi minuti dopo la modifica delle dimensioni di cui è stato effettuato il provisioning.

È possibile ridurre le dimensioni della condivisione di cui è stato effettuato il provisioning al di sotto del GiB usato. In questo caso, non si perderanno i dati, ma si riceveranno comunque le dimensioni usate e si riceveranno le prestazioni (operazioni di I/O al secondo di base, velocità effettiva e I/O al secondo burst) della condivisione di cui è stato effettuato il provisioning, non le dimensioni usate.

La tabella seguente illustra alcuni esempi di queste formule per le dimensioni di condivisione di cui è stato effettuato il provisioning:

|Capacità (GiB) | Operazioni di I/O al secondo di base | Operazioni di I/O al secondo burst | Uscita (MiB/s) | Ingresso (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Fino a 4.000     | 66   | 44   |
|500         | 900     | Fino a 4.000  | 90   | 60   |
|1\.024       | 1,424   | Fino a 4.000   | 122   | 81   |
|5120       | 5,520   | Fino a 15.360  | 368   | 245   |
|10,240      | 10,640  | Fino a 30.720  | 675   | 450   |
|33,792      | 34,192  | Fino a 100.000 | 2,088 | 1,392   |
|51,200      | 51,600  | Fino a 100.000 | 3.132 | 2,088   |
|102,400     | 100,000 | Fino a 100.000 | 6,204 | 4,136   |

Le prestazioni effettive della condivisione file sono soggette a limiti di rete del computer, larghezza di banda di rete disponibile, dimensioni di I/O, parallelismo, tra molti altri fattori. Ad esempio, in base a test interni con dimensioni di I/O in lettura/scrittura da 8 KiB, una singola macchina virtuale Windows senza SMB multicanale abilitato, *standard F16s_v2*, connesso alla condivisione file Premium tramite SMB, può ottenere 20.000 operazioni di I/O al secondo in lettura e 15.000 operazioni di I/O al secondo in scrittura. Con dimensioni di I/O in lettura/scrittura di 512 MiB, la stessa macchina virtuale può raggiungere 1,1 GiB/s in uscita e una velocità effettiva in ingresso di 370 MiB/s. Lo stesso client può ottenere prestazioni fino \~ a 3x se SMB multicanale è abilitato nelle condivisioni Premium. Per ottenere la massima scalabilità delle prestazioni, [abilitare SMB multicanale](storage-files-enable-smb-multichannel.md) e distribuire il carico tra più macchine virtuali. Per alcuni problemi di [](storage-troubleshooting-files-performance.md) prestazioni comuni e soluzioni alternative, vedere la guida alle prestazioni [multicanale SMB](storage-files-smb-multichannel-performance.md) e alla risoluzione dei problemi.

### <a name="bursting"></a>Scoppio
Se il carico di lavoro necessita di prestazioni aggiuntive per soddisfare il picco della domanda, la condivisione può usare crediti burst per andare oltre il limite di operazioni di I/O al secondo di base per offrire prestazioni di condivisione necessarie per soddisfare la domanda. Le condivisioni file Premium possono eseguire il burst delle operazioni di I/O al secondo fino a 4.000 o fino a un fattore pari a tre, a seconda del valore più alto. Il bursting è automatizzato e funziona in base a un sistema di credito. L'burst funziona in modo ottimale e il limite di burst  non è una garanzia, le condivisioni file possono essere burst fino al limite per una durata massima di 60 minuti.

I crediti si accumulano in un bucket burst ogni volta che il traffico per la condivisione file è inferiore alle operazioni di I/O al secondo di base. Ad esempio, una condivisione da 100 GiB ha 500 operazioni di I/O al secondo di base. Se il traffico effettivo sulla condivisione è stato di 100 IOPS per un intervallo specifico di 1 secondo, le 400 operazioni di I/O al secondo non utilizzate vengono accreditate in un bucket burst. Analogamente, una condivisione inattiva da 1 TiB accumula un credito burst a 1.424 operazioni di I/O al secondo. Questi crediti verranno quindi usati in un secondo momento quando le operazioni supereranno le operazioni di I/O al secondo di base.

Ogni volta che una condivisione supera le operazioni di I/O al secondo di base e ha crediti in un bucket burst, viene burst al massimo picco consentito. Le condivisioni possono continuare a essere burst fino a quando i crediti sono rimasti, fino a un massimo di 60 minuti di durata, ma si basa sul numero di crediti burst accumulati. Ogni I/O al di là delle operazioni di I/O al secondo di base utilizza un credito e, una volta utilizzati tutti i crediti, la condivisione tornerà alle operazioni di I/O al secondo di base.

I crediti di condivisione hanno tre stati:

- Accumulo, quando la condivisione file usa meno operazioni di I/O al secondo di base.
- Riduzione, quando la condivisione file usa più operazioni di I/O al secondo di base e in modalità burst.
- Costante, quando la condivisione file usa esattamente le operazioni di I/O al secondo di base, non sono accumulati o usati crediti.

Le nuove condivisioni file iniziano con il numero completo di crediti nel bucket burst. I crediti burst non verranno accumulati se le operazioni di I/O al secondo di condivisione sono inferiori alle operazioni di I/O al secondo di base a causa della limitazione da parte del server.

## <a name="pay-as-you-go-model"></a>Modello con pagamento in base al modello
File di Azure usa un modello aziendale con pagamento in base al modello per le condivisioni file standard. In un modello aziendale con pagamento in base al prezzo, l'importo pagato è determinato dall'importo effettivamente utilizzato, anziché da un importo di cui è stato effettuato il provisioning. A livello elevato, si paga un costo per la quantità di dati archiviati su disco e quindi un set aggiuntivo di transazioni in base all'utilizzo di questi dati. Un modello con pagamento in base al consumo può essere economicamente efficiente, perché non è necessario eseguire un provisioning overprovision per soddisfare i requisiti futuri di crescita o prestazioni o il deprovisioning se il footprint dei dati del carico di lavoro varia nel tempo. D'altra parte, anche un modello con pagamento in base al consumo può essere difficile da pianificare come parte di un processo di budget, perché il modello di fatturazione con pagamento in base al consumo è basato sul consumo degli utenti finali.

### <a name="differences-in-standard-tiers"></a>Differenze nei livelli Standard
Quando si crea una condivisione file standard, è possibile scegliere tra i livelli ottimizzati per le transazioni, ad accesso più caldo e ad accesso più cool. Tutti e tre i livelli vengono archiviati nello stesso hardware di archiviazione standard. La differenza principale per questi tre livelli è rappresentata dai prezzi di archiviazione dei dati inattivi, che sono più bassi nei livelli ad accesso più sporadico, e dai prezzi delle transazioni, che sono più elevati nei livelli ad accesso più sporadico. Ciò significa:

- Il livello ottimizzato per le transazioni, come suggerisce il nome, ottimizza il prezzo per i carichi di lavoro con un numero di transazioni elevato. A questo livello sono associati i prezzi più alti per l'archiviazione dei dati inattivi, ma i prezzi più bassi per le transazioni.
- Il livello di archiviazione ad accesso frequente è per i carichi di lavoro attivi che non comportano un numero elevato di transazioni e ha un prezzo leggermente inferiore per l'archiviazione dei dati inattivi, ma prezzi leggermente più elevati per le transazioni rispetto al livello ottimizzato per le transazioni. Può essere l'oggetto intermedio tra i livelli ottimizzati per le transazioni e i livelli ad accesso più cool.
- Cool ottimizza il prezzo per i carichi di lavoro che non hanno molta attività, offrendo il prezzo più basso per l'archiviazione dei dati in stato di inquieto, ma i prezzi più elevati per le transazioni.

Se si mette un carico di lavoro a cui si accede raramente nel livello ottimizzato per le transazioni, non si paga quasi nulla per il numero di volte in un mese in cui si effettuano transazioni sulla condivisione, ma si paga un importo elevato per i costi di archiviazione dei dati. Se si spostasse la stessa condivisione nel livello ad accesso più cool, non si pagava quasi nulla per i costi delle transazioni, semplicemente perché si effettuano raramente transazioni per questo carico di lavoro, ma il livello ad accesso più basso ha un prezzo di archiviazione dei dati molto più economico. La selezione del livello appropriato per il caso d'uso consente di ridurre notevolmente i costi. La selezione del livello appropriato per il caso d'uso consente di ridurre notevolmente i costi.

Analogamente, se si mette un carico di lavoro ad accesso elevato nel livello ad accesso elevato, si paga molto di più nei costi delle transazioni, ma meno per i costi di archiviazione dei dati. Ciò può causare una situazione in cui l'aumento dei costi dei prezzi delle transazioni supera i risparmi rispetto al calo del prezzo di archiviazione dei dati, con il rischio di pagare più denaro in caso di raffreddamento rispetto a quanto si farebbe per le transazioni ottimizzate. È possibile per alcuni livelli di utilizzo che, mentre il livello ad accesso rapido sarà il livello più efficiente in termini di costi, il livello ad accesso freddo sarà più costoso rispetto a quello ottimizzato per le transazioni.

Il carico di lavoro e il livello di attività determinano il livello più vantaggioso in termini di costo per la condivisione file standard. In pratica, il modo migliore per scegliere il livello più efficiente in termini di costi consiste nell'analisi dell'effettivo consumo di risorse della condivisione (dati archiviati, transazioni di scrittura e così via).

### <a name="what-are-transactions"></a>Che cosa sono le transazioni?
Le transazioni sono operazioni o richieste File di Azure caricare, scaricare o modificare in altro modo il contenuto della condivisione file. Ogni azione eseguita su una condivisione file si traduce in una o più transazioni e in condivisioni standard che usano il modello di fatturazione con pagamento in base al costo, che si traduce in costi di transazione.

Esistono cinque categorie di transazioni di base: scrittura, elenco, lettura, altro ed eliminazione. Tutte le operazioni eseguite tramite l'API REST o SMB vengono suddivise in bucket in una di queste 4 categorie, come indicato di seguito:

| Tipo di operazione | Scrivere transazioni | Elencare le transazioni | Leggere le transazioni | Altre transazioni | Eliminare transazioni |
|-|-|-|-|-|-|
| Operazioni di gestione | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operazioni sui dati | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 è disponibile solo per le condivisioni file Premium, che usano il modello di fatturazione con provisioning. Le transazioni non influiscono sulla fatturazione per le condivisioni file Premium.

## <a name="see-also"></a>Vedi anche
- [File di Azure prezzi .](https://azure.microsoft.com/pricing/details/storage/files/)
- [Pianificazione di una distribuzione File di Azure e](storage-files-planning.md) pianificazione di una distribuzione Sincronizzazione file di Azure distribuzione [.](../file-sync/file-sync-planning.md)
- [Creare una condivisione file e](storage-how-to-create-file-share.md) distribuire [Sincronizzazione file di Azure](../file-sync/file-sync-deployment-guide.md).