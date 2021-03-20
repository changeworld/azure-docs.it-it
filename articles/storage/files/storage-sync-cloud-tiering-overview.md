---
title: Informazioni Sincronizzazione file di Azure suddivisione in livelli nel cloud | Microsoft Docs
description: Comprendere la suddivisione in livelli nel cloud, una funzionalità Sincronizzazione file di Azure facoltativa. I file a cui si accede di frequente vengono memorizzati nella cache localmente nel server. altri sono suddivisi in livelli per la File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcd58e966da7ca596a14ca1b2839cbeb6399a855
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576459"
---
# <a name="cloud-tiering-overview"></a>Panoramica di suddivisione in livelli nel cloud
La suddivisione in livelli nel cloud, una funzionalità facoltativa di Sincronizzazione file di Azure, riduce la quantità di spazio di archiviazione locale necessario mantenendo le prestazioni di un file server locale.

Se abilitata, questa funzionalità archivia solo i file a cui si accede di frequente nel server locale. I file ad accesso sporadico sono suddivisi in spazi dei nomi (struttura di file e cartelle) e contenuto del file. Lo spazio dei nomi viene archiviato localmente e il contenuto del file archiviato in una condivisione file di Azure nel cloud. 

Quando un utente apre un file a livelli, Sincronizzazione file di Azure richiama facilmente i dati del file dalla condivisione file in Azure.

## <a name="how-cloud-tiering-works"></a>Come funziona la suddivisione in livelli nel cloud

### <a name="cloud-tiering-policies"></a>Criteri di suddivisione in livelli cloud
Quando si Abilita la suddivisione in livelli nel cloud, sono disponibili due criteri che è possibile impostare per informare Sincronizzazione file di Azure quando si desidera eseguire il livello dei file ad accesso sporadico: i **criteri di spazio disponibile del volume** e i **criteri di data**. 

#### <a name="volume-free-space-policy"></a>Criterio spazio disponibile volume
Il **criterio di spazio disponibile nel volume** indica sincronizzazione file di Azure di eseguire il livello dei file sporadici nel cloud quando viene rilevata una certa quantità di spazio sul disco locale. 

Se, ad esempio, la capacità del disco locale è di 200 GB e si desidera che almeno 40 GB della capacità del disco locale rimangano sempre disponibili, è necessario impostare il criterio spazio disponibile sul volume sul 20%. Lo spazio disponibile nel volume si applica a livello di volume anziché a livello di singole directory o endpoint server. 

Per informazioni sul modo in cui i criteri di spazio libero del volume influiscono sui file scaricati inizialmente quando viene aggiunto un nuovo endpoint server, vedere la sezione [criteri di sincronizzazione che interessano](#sync-policies-that-affect-cloud-tiering)la suddivisione in livelli nel cloud.

#### <a name="date-policy"></a>Criteri di data
Con i **criteri di data**, i file sporadici vengono suddivisi in livelli nel cloud se non sono stati accessibili, ovvero letti o scritti, per un numero di giorni pari a x. Se, ad esempio, si è notato che i file che hanno superato i 15 giorni senza essere accessibili sono in genere file di archivio, è necessario impostare i criteri di data su 15 giorni. 

Per altri esempi sull'interazione tra i criteri di data e lo spazio libero del volume, vedere [scegliere Sincronizzazione file di Azure criteri](storage-sync-choose-cloud-tiering-policies.md)di suddivisione in livelli nel cloud.

### <a name="windows-server-data-deduplication"></a>Deduplicazione dati di Windows Server
La deduplicazione dei dati è supportata nei volumi per i quali è abilitata la suddivisione in livelli nel cloud a partire da Windows Server 2016. Per informazioni dettagliate, vedere [pianificazione di una distribuzione di sincronizzazione file di Azure](./storage-sync-files-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Suddivisione in livelli cloud mappa termica
Sincronizzazione file di Azure monitora l'accesso ai file (operazioni di lettura e scrittura) nel tempo e, in base alla frequenza e all'accesso recenti, assegna un punteggio di calore a ogni file. Usa questi punteggi per creare un "mappa termica" dello spazio dei nomi in ogni endpoint server. Questo mappa termica è un elenco di tutti i file di sincronizzazione in un percorso con la suddivisione in livelli nel cloud abilitata, ordinata in base al Punteggio di calore. I file ad accesso frequente aperti di recente sono considerati attivi, mentre i file che sono stati appena modificati e non sono stati utilizzati per un certo periodo di tempo sono considerati interessanti. 

Per determinare la posizione relativa di un singolo file in tale mappa termica, il sistema usa il numero massimo di timestamp, nell'ordine seguente: massimo (ora dell'ultimo accesso, ora dell'Ultima modifica, ora di creazione). 

In genere, l'ora dell'ultimo accesso viene rilevata e disponibile. Tuttavia, quando viene creato un nuovo endpoint server, con la suddivisione in livelli nel cloud abilitata, il tempo trascorso non è sufficiente per osservare l'accesso ai file. Se non è disponibile l'ora dell'ultimo accesso valido, viene invece utilizzata l'ora dell'Ultima modifica per valutare la posizione relativa in mappa termica.  

Il criterio di data funziona allo stesso modo. Senza l'ora dell'ultimo accesso, il criterio di data agirà sull'ora dell'Ultima modifica. Se non è disponibile, verrà eseguito il fallback all'ora di creazione di un file. Nel corso del tempo, il sistema osserverà più richieste di accesso ai file e inizierà automaticamente a usare l'ora dell'ultimo accesso con rilevamento automatico.

> [!Note]
> La suddivisione in livelli cloud non dipende dalla funzionalità NTFS per il rilevamento dell'ora dell'ultimo accesso. Questa funzionalità NTFS è disattivata per impostazione predefinita e, a causa delle considerazioni sulle prestazioni, non è consigliabile abilitare manualmente questa funzionalità. La suddivisione in livelli nel cloud tiene traccia dell'ora dell'ultimo accesso separatamente.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Sincronizzare i criteri che influiscono sulla suddivisione in livelli nel cloud

Con Sincronizzazione file di Azure Agent versione 11, è possibile impostare due criteri Sincronizzazione file di Azure aggiuntivi che influiscono sulla suddivisione in livelli nel cloud: il **download iniziale** e il **richiamo proattivo**.

#### <a name="initial-download"></a>Download iniziale

Quando un server si connette a una condivisione file di Azure con file al suo interno, è ora possibile decidere come si vuole che il server scarichi inizialmente i dati della condivisione file. Quando è abilitata la suddivisione in livelli nel cloud, sono disponibili due opzioni. 

![Screenshot del download iniziale quando è abilitata la suddivisione in livelli nel cloud](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

La prima opzione consiste nel richiamare prima lo spazio dei nomi e quindi richiamare il contenuto del file in base al timestamp dell'Ultima modifica, fino a quando non viene raggiunta la capacità del disco locale. Se lo spazio su disco è sufficiente e si è certi che i file modificati per ultimi devono essere memorizzati nella cache in locale, questa opzione è ideale. I file che non possono essere archiviati localmente a causa di spazio su disco insufficiente verranno suddivisi in livelli.        

La seconda opzione consiste nel richiamare inizialmente lo spazio dei nomi e richiamare il contenuto del file solo quando vi si accede. Questa opzione è consigliata se si desidera ridurre al minimo la capacità utilizzata nel disco locale e si desidera che gli utenti decidano quali file devono essere memorizzati nella cache in locale. Tutti i file vengono avviati come file a livelli con questa opzione.

![Screenshot del download iniziale quando la suddivisione in livelli cloud è disabilitata](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Quando la suddivisione in livelli nel cloud è disabilitata, è disponibile una terza opzione che consente di evitare tutti i file a livelli. In questa situazione, i file verranno visualizzati nel server solo dopo che sono stati scaricati completamente. Se si dispone di applicazioni che richiedono la presenza di file completi e che non possono tollerare i file a livelli nello spazio dei nomi, questo è l'ideale.      

#### <a name="proactive-recalling"></a>Richiamo proattivo

Quando si crea o si modifica un file, è possibile richiamare in modo proattivo un file nei server specificati. In questo modo il file nuovo o modificato risulta immediatamente disponibile per l'utilizzo in ogni server specificato. 

Una società distribuita a livello globale, ad esempio, ha filiali negli Stati Uniti e in India. Al mattino (US Time) gli Information Worker creano una nuova cartella e nuovi file per un nuovo progetto e lavorano tutto il giorno. Sincronizzazione file di Azure sincronizza la cartella e i file nella condivisione file di Azure (endpoint cloud). Gli Information Worker in India continueranno a lavorare sul progetto nel fuso orario. Quando arrivano al mattino, il server locale Sincronizzazione file di Azure abilitato in India deve avere i nuovi file disponibili localmente, in modo che il team India possa lavorare in modo efficiente da una cache locale. L'abilitazione di questa modalità impedisce che l'accesso al file iniziale risulti più lento a causa del richiamo su richiesta e consente al server di richiamare in modo proattivo i file non appena sono stati modificati o creati nella condivisione file di Azure.

Se i file richiamati nel server non sono effettivamente necessari in locale, il richiamo non necessario può aumentare il traffico in uscita e i costi. Pertanto, abilitare il richiamo proattivo solo quando si è certi che il prepopolamento della cache di un server con modifiche recenti dal cloud avrà un effetto positivo sugli utenti o sulle applicazioni che utilizzano i file in tale server. 

L'abilitazione del richiamo proattivo può inoltre comportare un aumento dell'utilizzo della larghezza di banda nel server e può causare un livello aggressivo di altri contenuti sul server locale a seconda dell'aumento dei file richiamati. A sua volta, è possibile che si verifichino più richiami se i file a livelli sono considerati sensibili dai server. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Immagine che mostra il comportamento di download della condivisione file di Azure per un endpoint server attualmente attivo e un pulsante per aprire un menu che consente di modificarlo.":::

Per ulteriori informazioni sul richiamo proattivo, vedere la pagina relativa alla [distribuzione di sincronizzazione file di Azure](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Comportamento dei file a livelli e memorizzati nella cache locale

La suddivisione in livelli nel cloud è la separazione tra lo spazio dei nomi (la gerarchia di file e cartelle, nonché le proprietà del file) e il contenuto del file. 

#### <a name="tiered-file"></a>File a livelli

Per i file a livelli, le dimensioni su disco sono pari a zero poiché il contenuto del file non viene archiviato localmente. Quando un file è a livelli, il filtro Sincronizzazione file di Azure file system (StorageSync.sys) sostituisce il file in locale con un puntatore (reparse point). Il reparse point rappresenta un URL del file nella condivisione file di Azure. Un file archiviato a livelli include sia l'attributo "offline" sia l'attributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS impostato in NTFS, in modo che le applicazioni di terze parti possano identificare in modo sicuro questo tipo di file.   

![Screenshot delle proprietà di un file quando è a livelli: solo spazio dei nomi.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>File memorizzato localmente nella cache

D'altra parte, per un file archiviato in un file server locale, le dimensioni su disco sono pari alla dimensione logica del file perché l'intero file (attributi file + contenuto file) viene archiviato localmente.     

![Screenshot delle proprietà di un file quando non è a livelli-spazio dei nomi e contenuto del file.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

È anche possibile che un file sia parzialmente a livelli (o parzialmente richiamato). In un file parzialmente a livelli, parte del file è su disco. Questo problema può verificarsi quando i file vengono letti parzialmente da applicazioni come i lettori multimediali o le utilità zip che supportano l'accesso in streaming ai file. Sincronizzazione file di Azure è intelligente e richiama solo le informazioni richieste dalla condivisione file di Azure connessa.

> [!NOTE]
> Size rappresenta le dimensioni logiche del file. Dimensioni su disco rappresenta le dimensioni fisiche del flusso di file archiviato sul disco.

## <a name="next-steps"></a>Passaggi successivi
* [Scegliere Sincronizzazione file di Azure criteri di suddivisione in livelli cloud](storage-sync-choose-cloud-tiering-policies.md)
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)