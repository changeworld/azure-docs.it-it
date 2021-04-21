---
title: Informazioni Sincronizzazione file di Azure cloud a livelli | Microsoft Docs
description: Informazioni sul cloud a livelli, una funzionalità Sincronizzazione file di Azure facoltativa. I file a cui si accede di frequente vengono memorizzati nella cache locale nel server. altri sono a livelli per File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797052"
---
# <a name="cloud-tiering-overview"></a>Panoramica della distribuzione a livelli nel cloud
La creazione di livelli cloud, una funzionalità facoltativa di Sincronizzazione file di Azure, riduce la quantità di spazio di archiviazione locale necessario mantenendo le prestazioni di un'istanza file server.

Se abilitata, questa funzionalità archivia solo i file ad accesso frequente (ad accesso frequente) nel server locale. I file a cui si accede raramente (cool) sono suddivisi in spazio dei nomi (struttura di file e cartelle) e contenuto dei file. Lo spazio dei nomi viene archiviato in locale e il contenuto del file archiviato in una condivisione file di Azure nel cloud. 

Quando un utente apre un file a livelli, Sincronizzazione file di Azure facilmente i dati del file dalla condivisione file in Azure.

## <a name="how-cloud-tiering-works"></a>Funzionamento della creazione di livelli nel cloud

### <a name="cloud-tiering-policies"></a>Criteri di livelli cloud
Quando si abilita la distribuzione a livelli nel cloud, è possibile impostare due criteri per informare Sincronizzazione file di Azure livello dei file ad accesso remoto: i criteri di spazio libero del **volume** e i criteri **di data.** 

#### <a name="volume-free-space-policy"></a>Criteri di spazio disponibile nel volume
I **criteri di spazio libero** del volume Sincronizzazione file di Azure di livelli di file ad accesso fisso al cloud quando una determinata quantità di spazio viene presa sul disco locale. 

Ad esempio, se la capacità del disco locale è di 200 GB e si vuole che almeno 40 GB della capacità del disco locale rimangano sempre liberi, è necessario impostare i criteri di spazio libero del volume sul 20%. Lo spazio disponibile nel volume si applica a livello di volume anziché a livello di singole directory o endpoint server. 

Per informazioni su come i criteri di spazio disponibile nel volume influiscono sui file scaricati inizialmente quando viene aggiunto un nuovo endpoint server, vedere la sezione Criteri di sincronizzazione che influiscono sulla [livellistica cloud.](#sync-policies-that-affect-cloud-tiering)

#### <a name="date-policy"></a>Criteri di data
Con i **criteri di data,** i file ad accesso vano vengono a livelli nel cloud se non sono stati accessibili (ovvero, letti o scritti in) per x numero di giorni. Ad esempio, se si è notato che i file che sono passati più di 15 giorni senza accedere sono in genere file di archivio, è necessario impostare i criteri di data su 15 giorni. 

Per altri esempi sull'interazione tra i criteri di data e i criteri di spazio disponibile nel volume, vedere Scegliere i Sincronizzazione file di Azure [cloud a livelli.](file-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Deduplicazione dati di Windows Server
La deduplicazione dei dati è supportata nei volumi con cloud a livelli abilitato a partire da Windows Server 2016. Per altri dettagli, vedere [Planning for an Sincronizzazione file di Azure deployment](file-sync-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Mappa termica di cloud a livelli
Sincronizzazione file di Azure monitora l'accesso ai file (operazioni di lettura e scrittura) nel tempo e, in base alla frequenza e alla frequenza di accesso recente, assegna un punteggio termico a ogni file. Usa questi punteggi per creare una "mappa termica" dello spazio dei nomi in ogni endpoint server. Questa mappa termica è un elenco di tutti i file di sincronizzazione in una posizione con cloud a livelli abilitato, ordinati in base al punteggio di calore. I file a cui si accede di frequente aperti di recente sono considerati ad accesso frequente, mentre i file che sono stati toccati e che non sono stati usati per un certo periodo di tempo sono considerati ad accesso frequente. 

Per determinare la posizione relativa di un singolo file in tale mappa termica, il sistema usa il numero massimo di timestamp, nell'ordine seguente: MAX(Ora ultimo accesso, Ora ultima modifica, Ora creazione). 

In genere, l'ora dell'ultimo accesso viene rilevata e disponibile. Tuttavia, quando viene creato un nuovo endpoint server, con il cloud a livelli abilitato, non è passato tempo sufficiente per osservare l'accesso ai file. Se non esiste un'ora dell'ultimo accesso valida, viene usata l'ora dell'ultima modifica per valutare la posizione relativa nella mappa termica.  

Il criterio di data funziona allo stesso modo. Senza un'ora dell'ultimo accesso, i criteri di data agiranno in base all'ora dell'ultima modifica. Se non è disponibile, verrà visualizzato il fall back all'ora di creazione di un file. Nel corso del tempo, il sistema osserverà sempre più richieste di accesso ai file e inizierà automaticamente a usare l'ora dell'ultimo accesso con rilevamento automatico.

> [!Note]
> Il cloud a livelli non dipende dalla funzionalità NTFS per tenere traccia dell'ora dell'ultimo accesso. Questa funzionalità NTFS è disattivata per impostazione predefinita e, a causa di considerazioni sulle prestazioni, non è consigliabile abilitare manualmente questa funzionalità. La distribuzione a livelli nel cloud tiene traccia separatamente dell'ora dell'ultimo accesso.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Criteri di sincronizzazione che influiscono sulla livellistica cloud

Con Sincronizzazione file di Azure agent versione 11, sono disponibili due criteri Sincronizzazione file di Azure aggiuntivi che influiscono sulla livellistica cloud: il **download** iniziale e il richiamo **proattivo** di .

#### <a name="initial-download"></a>Download iniziale

Quando un server si connette a una condivisione file di Azure con i file in esso presenti, è ora possibile decidere in che modo il server deve scaricare inizialmente i dati della condivisione file. Quando la livellistica cloud è abilitata, sono disponibili due opzioni. 

![Screenshot del download iniziale quando è abilitata la livellistica cloud](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

La prima opzione è richiamare prima lo spazio dei nomi e quindi richiamare il contenuto del file in base al timestamp dell'ultima modifica, fino a quando non viene raggiunta la capacità del disco locale. Se si dispone di spazio su disco sufficiente e si sa che i file che sono stati modificati per ultima devono essere memorizzati nella cache locale, questa opzione è ideale. I file che non possono essere archiviati in locale a causa della mancanza di spazio su disco verranno a livelli.        

La seconda opzione è richiamare inizialmente solo lo spazio dei nomi e richiamare il contenuto del file solo quando si accede. Questa opzione è consigliata se si vuole ridurre al minimo la capacità usata nel disco locale e si vuole che gli utenti decidano quali file memorizzare nella cache in locale. Tutti i file verranno avviati come file a livelli con questa opzione.

![Screenshot del download iniziale quando la distribuzione a livelli nel cloud è disabilitata](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Quando la distribuzione a livelli nel cloud è disabilitata, è disponibile una terza opzione, che consente di evitare tutti i file a livelli. In questo caso, i file verranno visualizzati nel server solo dopo che sono stati scaricati completamente. Se si dispone di applicazioni che richiedono la visualizzazione di file completi e non possono tollerare file a livelli nel relativo spazio dei nomi, questo è l'ideale.      

#### <a name="proactive-recalling"></a>Richiamo proattivo

Quando un file viene creato o modificato, è possibile richiamare in modo proattivo un file nei server specificati. In questo modo il file nuovo o modificato è immediatamente disponibile per l'utilizzo in ogni server specificato. 

Ad esempio, una società distribuita a livello globale ha succursali negli Stati Uniti e in India. Nella mattina (ora degli Stati Uniti) gli information worker creano una nuova cartella e nuovi file per un progetto completamente nuovo e lavorano tutto il giorno su di esso. Sincronizzazione file di Azure la cartella e i file verranno sincronizzati con la condivisione file di Azure (endpoint cloud). Gli Information Worker in India continueranno a lavorare al progetto nel fuso orario. Quando arrivano la mattina, il server abilitato per l'Sincronizzazione file di Azure locale in India deve avere questi nuovi file disponibili in locale, in modo che il team India possa lavorare in modo efficiente da una cache locale. L'abilitazione di questa modalità impedisce che l'accesso ai file iniziale sia più lento a causa del richiamo su richiesta e consente al server di richiamare in modo proattivo i file non appena sono stati modificati o creati nella condivisione file di Azure.

Se i file richiamati al server non sono effettivamente necessari in locale, il richiamo non necessario può aumentare il traffico in uscita e i costi. Pertanto, abilitare il richiamo proattivo solo quando si sa che il pre-popolamento della cache di un server con le modifiche recenti dal cloud avrà un effetto positivo sugli utenti o sulle applicazioni che usano i file in tale server. 

L'abilitazione del richiamo proattivo può anche comportare un aumento dell'utilizzo della larghezza di banda nel server e può causare il ritiro aggressivo di altri contenuti relativamente nuovi nel server locale a causa dell'aumento dei file richiamati. A sua volta, ciò può comportare più richiami se i file a livelli sono considerati ad accesso più caldo dai server. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Immagine che mostra il comportamento di download della condivisione file di Azure per un endpoint server attualmente attivo e un pulsante per aprire un menu che consente di modificarlo.":::

Per altri dettagli sul richiamo proattivo, vedere [Distribuire Sincronizzazione file di Azure](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Comportamento dei file memorizzati nella cache locale e a livelli

Il cloud a livelli è la separazione tra lo spazio dei nomi (la gerarchia di file e cartelle, nonché le proprietà dei file) e il contenuto del file. 

#### <a name="tiered-file"></a>File a livelli

Per i file a livelli, le dimensioni su disco sono pari a zero perché il contenuto del file stesso non viene archiviato in locale. Quando un file è a livelli, il filtro Sincronizzazione file di Azure file system (StorageSync.sys) sostituisce il file in locale con un puntatore (reparse point). Il reparse point rappresenta un URL del file nella condivisione file di Azure. Un file archiviato a livelli include sia l'attributo "offline" sia l'attributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS impostato in NTFS, in modo che le applicazioni di terze parti possano identificare in modo sicuro questo tipo di file.   

![Screenshot delle proprietà di un file quando è a livelli, solo per lo spazio dei nomi.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>File memorizzato nella cache locale

D'altra parte, per un file archiviato in un file server locale, le dimensioni su disco sono circa uguali alle dimensioni logiche del file poiché l'intero file (attributi di file e contenuto del file) viene archiviato in locale.     

![Screenshot delle proprietà di un file quando non è a livelli: spazio dei nomi e contenuto del file.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

È anche possibile che un file sia parzialmente a livelli (o parzialmente richiamato). In un file parzialmente a livelli, parte del file è su disco. Ciò può verificarsi quando i file vengono letti parzialmente da applicazioni come lettori multimediali o utilità ZIP che supportano l'accesso in streaming ai file. Sincronizzazione file di Azure è intelligente e richiama solo le informazioni richieste dalla condivisione file di Azure connessa.

> [!NOTE]
> Size rappresenta le dimensioni logiche del file. Dimensioni su disco rappresenta le dimensioni fisiche del flusso di file archiviato sul disco.

## <a name="next-steps"></a>Passaggi successivi

* [Scegliere i Sincronizzazione file di Azure a livelli cloud](file-sync-choose-cloud-tiering-policies.md)
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)