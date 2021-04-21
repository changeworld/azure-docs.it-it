---
title: Scegliere Sincronizzazione file di Azure di livelli cloud | Microsoft Docs
description: Informazioni dettagliate su cosa tenere presente quando si scelgono i Sincronizzazione file di Azure cloud a livelli.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797058"
---
# <a name="choose-cloud-tiering-policies"></a>Scegliere i criteri di livelli cloud

Questo articolo fornisce indicazioni per gli utenti che selezionano e modificano i criteri di livelli cloud. Prima di leggere questo articolo, assicurarsi di comprendere il funzionamento della distribuzione a livelli nel cloud. Per informazioni di base sulla creazione di livelli nel cloud, vedere [Informazioni Sincronizzazione file di Azure cloud a livelli.](file-sync-cloud-tiering-overview.md) Per una spiegazione approfondita dei criteri di a livelli del cloud con esempi, vedere Sincronizzazione file di Azure [di livelli cloud.](file-sync-cloud-tiering-policy.md)

## <a name="limitations"></a>Limitazioni
- La distribuzione a livelli cloud non è supportata nel volume di sistema Windows.

- Se si dispone di una quota FSRM a livello di volume, è comunque possibile abilitare la gestione a livelli del cloud. Dopo aver impostato una quota FSRM, le API di query sullo spazio libero chiamate segnalano automaticamente lo spazio disponibile nel volume in base all'impostazione della quota. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Dimensioni minime del file da livello a livello

Per le versioni 9 e successive dell'agente, le dimensioni minime del file da livello a livello si basano sulle dimensioni del cluster file system cluster. Le dimensioni minime dei file idonei per la creazione di livelli cloud vengono calcolate di 2x le dimensioni del cluster e di almeno 8 KB. La tabella seguente illustra le dimensioni minime dei file che possono essere a livelli, in base alle dimensioni del cluster del volume:

|Dimensioni cluster del volume (byte) |I file di queste dimensioni o superiori possono essere a livelli  |
|----------------------------|---------|
|4 KB o inferiori (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Le dimensioni del cluster fino a 2 MB sono supportate con Sincronizzazione file di Azure agent versione 12, ma, per dimensioni maggiori, il cloud a livelli non funziona.

Tutti i file system usati da Windows organizzano il disco rigido in base alle dimensioni del cluster (note anche come dimensioni delle unità di allocazione). Le dimensioni del cluster rappresentano la quantità minima di spazio su disco che può essere usata per contenere un file. Quando le dimensioni dei file non hanno un multiplo pari delle dimensioni del cluster, è necessario usare spazio aggiuntivo per contenere il file, fino al multiplo successivo delle dimensioni del cluster.

Sincronizzazione file di Azure è supportato nei volumi NTFS con Windows Server 2012 R2 e versioni più recente. La tabella seguente descrive le dimensioni predefinite del cluster quando si crea un nuovo volume NTFS con Windows Server 2019.

|Dimensioni del volume    |Windows Server 2019             |
|---------------|--------------------------------|
|Da 7 MB a 16 TB   | 4 KB                |
|Da 16 TB a 32 TB   | 8 KB                |
|Da 32 TB a 64 TB   | 16 KB               |
|64 TB - 128 TB  | 32 KB               |
|Da 128 TB a 256 TB | 64 KB (in precedenza, dimensione max) |
|256 TB - 512 TB| 128 KB              |
|512 TB - 1 PB  | 256 KB              |
|1 PB - 2 PB    | 512 KB              |
|2 TB - 4 PB    | 1024 KB             |
|4 TB - 8 TB    | 2048 KB (dimensione max)  |
|> 8 TB         | non supportato       |

È possibile che al momento della creazione del volume il volume sia stato formattato manualmente con dimensioni del cluster diverse. Se il volume deriva da una versione precedente di Windows, anche le dimensioni predefinite del cluster potrebbero essere diverse. [Questo articolo contiene altri dettagli sulle dimensioni predefinite del cluster.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Anche se si sceglie una dimensione del cluster inferiore a 4 KB, viene comunque applicato un limite di 8 KB come dimensioni di file più piccole che possono essere a livelli. Anche se tecnicamente le dimensioni del cluster 2x equiterebbero a meno di 8 KB.

Il motivo del minimo assoluto si trova nel modo in cui NTFS archivia file di dimensioni estremamente ridotte, da 1 KB a 4 KB. A seconda degli altri parametri del volume, è possibile che i file di piccole dimensioni non siano archiviati in un cluster su disco. È possibile che sia più efficiente archiviare tali file direttamente nella tabella file master del volume o nel "record MFT". Il reparse point di a livelli cloud viene sempre archiviato su disco e occupa esattamente un cluster. La distribuzione a livelli di file di piccole dimensioni potrebbe non risparmiare spazio. I casi estremi potrebbero persino usare più spazio con la livellistica cloud abilitata. A tale fine, le dimensioni minime di un file che verrà a livelli nel cloud sono di 8 KB con dimensioni del cluster di 4 KB o inferiori. 

## <a name="selecting-your-initial-policies"></a>Selezione dei criteri iniziali

In genere, quando si abilita la distribuzione a livelli cloud in un endpoint server, è necessario creare un'unità virtuale locale per ogni singolo endpoint server. L'isolamento dell'endpoint server semplifica la comprensione del funzionamento della divisione a livelli del cloud e la modifica dei criteri di conseguenza. Tuttavia, Sincronizzazione file di Azure anche se sono presenti più endpoint server nella stessa unità, per informazioni dettagliate, vedere la sezione Più [endpoint server nel volume](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) locale. Quando si abilita per la prima volta il cloud a livelli, è anche consigliabile mantenere i criteri di data disabilitati e i criteri di spazio libero del volume tra il 10% e il 20%. Per la maggior file server volumi, il 20% di spazio disponibile nel volume è in genere l'opzione migliore.

Per semplicità e per avere una comprensione chiara del modo in cui gli elementi verranno a livelli, è consigliabile modificare principalmente i criteri di spazio disponibile nel volume e mantenere i criteri di data disabilitati, a meno che non sia necessario. È consigliabile farlo perché la maggior parte dei clienti trova utile riempire la cache locale con il maggior numero possibile di file ad accesso caldo e impostare il resto nel cloud. Tuttavia, i criteri relativi alla data possono essere utili se si vuole liberare in modo proattivo spazio su disco locale e si sa che i file in tale endpoint server a cui si accede dopo il numero di giorni specificato nei criteri data non devono essere conservati in locale. L'impostazione dei criteri di data libera una capacità utile del disco locale per altri endpoint nello stesso volume per memorizzare nella cache più file.

Dopo aver impostato i criteri, monitorare i dati in uscita e modificare entrambi i criteri di conseguenza. È consigliabile esaminando in modo specifico le dimensioni di **richiamo per il cloud** a livelli e le dimensioni di richiamo a livelli del cloud in base alle **metriche** dell'applicazione Monitoraggio di Azure. Per informazioni su come monitorare i dati in uscita, vedere [Monitorare il cloud a livelli.](file-sync-monitor-cloud-tiering.md)

## <a name="adjusting-your-policies"></a>Modifica dei criteri

Se la quantità di file richiamati costantemente da Azure è superiore a quella desiderata, è possibile che si abbia più file ad accesso elevato rispetto allo spazio disponibile per salvarli nel volume del server locale. Aumentare le dimensioni del volume locale, se possibile, e/o ridurre la percentuale dei criteri di spazio disponibile nel volume in piccoli incrementi. Anche la riduzione della percentuale di spazio disponibile nel volume può avere conseguenze negative. Una varianza più elevata nel set di dati richiede più spazio disponibile, per i nuovi file e il richiamo di file "a freddo". La distribuzione a livelli inizia con un ritardo massimo di un'ora e quindi richiede tempo di elaborazione, motivo per cui è necessario avere sempre ampio spazio libero nel volume.

Mantenere più dati in locale significa ridurre i costi di uscita, in quanto un numero inferiore di file verrà richiamato da Azure, ma richiede anche una maggiore quantità di spazio di archiviazione locale, a proprio costo. 

Quando si modificano i criteri di spazio disponibile nel volume, la quantità di dati da mantenere locale è determinata dai fattori seguenti: larghezza di banda, modello di accesso del set di dati e budget. Con una connessione a larghezza di banda ridotta, è possibile che si vogliano più dati locali per garantire un ritardo minimo per gli utenti. Altrimenti è possibile basarsi sulla percentuale di varianza durante un determinato periodo. Ad esempio, se si sa che il 10% del set di dati da 1 TB cambia o si accede attivamente ogni mese, è possibile mantenere 100 GB locali in modo da non richiamare spesso i file. Se il volume è di 2 TB, è necessario mantenere il 5% (o 100 GB) locale, ovvero il restante 95% è la percentuale di spazio disponibile nel volume. Tuttavia, è consigliabile aggiungere un buffer per periodi di varianza superiore, in altre parole, iniziando con una percentuale di spazio disponibile del volume maggiore e quindi regolando il buffer se necessario in un secondo momento.

## <a name="standard-operating-procedures"></a>Procedure operative standard

- Quando si esegue per la prima volta la File di Azure tramite Sincronizzazione file di Azure, il cloud a livelli dipende dal caricamento iniziale
- La distribuzione a livelli nel cloud verifica la conformità con i criteri di data e spazio disponibile nel volume ogni 60 minuti
- L'uso dell'opzione /LFSM in Robocopy durante la migrazione dei file consentirà la sincronizzazione dei file e la sincronizzazione a livelli cloud per creare spazio durante il caricamento iniziale 
- Se si verifica la creazione di livelli prima della formazione di una mappa termica, i file verranno a livelli in base al timestamp dell'ultima modifica

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)
