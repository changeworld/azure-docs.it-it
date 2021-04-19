---
title: Migrazione di StorSimple serie 8000 a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di un'appliance StorSimple 8100 o 8600 Sincronizzazione file di Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717352"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migrazione di StorSimple 8100 e 8600 a Sincronizzazione file di Azure

StorSimple serie 8000 è rappresentato dalle appliance locali 8100 o 8600 fisiche e dai relativi componenti del servizio cloud. È possibile eseguire la migrazione dei dati da una di queste appliance a un Sincronizzazione file di Azure virtuale. Sincronizzazione file di Azure è il servizio di Azure a lungo termine predefinito e strategico in cui è possibile eseguire la migrazione delle appliance StorSimple.

La serie StorSimple 8000 [](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) terminerà nel dicembre 2022. È importante iniziare a pianificare la migrazione il prima possibile. Questo articolo fornisce le conoscenze di base necessarie e i passaggi di migrazione per una corretta migrazione a Sincronizzazione file di Azure.

## <a name="phase-1-prepare-for-migration"></a>Fase 1: Preparare la migrazione

Questa sezione contiene i passaggi da eseguire all'inizio della migrazione dai volumi StorSimple alle condivisioni file di Azure.

### <a name="inventory"></a>Argomento

Quando si inizia a pianificare la migrazione, identificare innanzitutto tutti i volumi e le appliance StorSimple di cui è necessario eseguire la migrazione. Dopo aver eseguito questa operazione, è possibile scegliere il percorso di migrazione migliore.

* Le appliance fisiche StorSimple (serie 8000) usano questa guida alla migrazione.
* Le appliance virtuali, [StorSimple serie 1200, usano una guida alla migrazione diversa.](storage-files-migration-storsimple-1200.md)

### <a name="migration-cost-summary"></a>Riepilogo dei costi di migrazione

Le migrazioni a condivisioni file di Azure da volumi StorSimple tramite processi di migrazione in StorSimple Data Manager risorse sono gratuite. Altri costi potrebbero essere sostenuti durante e dopo una migrazione:

* **Traffico in uscita di rete:** I file di StorSimple si trova in un account di archiviazione all'interno di un'area di Azure specifica. Se si effettua il provisioning delle condivisioni file di Azure di cui si esegue la migrazione in un account di archiviazione che si trova nella stessa area di Azure, non si verificheranno costi in uscita. È possibile spostare i file in un account di archiviazione in un'area diversa come parte di questa migrazione. In tal caso, i costi in uscita verranno applicati all'utente.
* **Transazioni di condivisione file di Azure:** Quando i file vengono copiati in una condivisione file di Azure (come parte di una migrazione o all'esterno di una condivisione), i costi delle transazioni si applicano durante la scrittura di file e metadati. Come procedura consigliata, avviare la condivisione file di Azure nel livello ottimizzato per le transazioni durante la migrazione. Passare al livello desiderato al termine della migrazione. Le fasi seguenti lo chiameranno nel punto appropriato.
* **Modificare un livello di condivisione file di Azure:** La modifica del livello di una condivisione file di Azure costa le transazioni. Nella maggior parte dei casi, sarà più efficiente in termini di costi seguire i consigli del punto precedente.
* **Costo di archiviazione:** Quando questa migrazione inizia a copiare i file in una condivisione file di Azure, File di Azure l'archiviazione viene utilizzata e fatturata. I backup di cui è stata eseguita la migrazione diventeranno [snapshot di condivisione file di Azure.](storage-snapshots-files.md) Gli snapshot di condivisione file utilizzano solo la capacità di archiviazione per le differenze che contengono.
* **StorSimple:** Finché non si ha la possibilità di eseguire il deprovisioning dei dispositivi e degli account di archiviazione StorSimple, il costo di StorSimple per l'archiviazione, i backup e le appliance continuerà a verificarsi.

### <a name="direct-share-access-vs-azure-file-sync"></a>Confronto tra direct share-access e Sincronizzazione file di Azure

Le condivisioni file di Azure aprono un nuovo mondo di opportunità per strutturare la distribuzione dei servizi file. Una condivisione file di Azure è solo una condivisione SMB nel cloud che è possibile configurare per consentire agli utenti di accedere direttamente tramite il protocollo SMB con l'autenticazione Kerberos familiare e le autorizzazioni NTFS esistenti (ACL di file e cartelle) che funzionano in modo nativo. Altre informazioni [sull'accesso basato sull'identità alle condivisioni file di Azure.](storage-files-active-directory-overview.md)

Un'alternativa all'accesso diretto [è Sincronizzazione file di Azure](./storage-sync-files-planning.md). Sincronizzazione file di Azure è un'analogia diretta per la capacità di StorSimple di memorizzare nella cache i file usati di frequente in locale.

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:

* Sincronizzazione dei file e cloud a livelli per creare una cache di accesso alle prestazioni in qualsiasi server Windows.
* Condivisioni file come archiviazione nativa in Azure a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file.

Le condivisioni file di Azure mantengono importanti aspetti di fedeltà dei file nei file archiviati, ad esempio attributi, autorizzazioni e timestamp. Con le condivisioni file di Azure non è più necessario che un'applicazione o un servizio interpreti i file e le cartelle archiviati nel cloud. È possibile accedervi in modo nativo tramite protocolli e client familiari, ad esempio Windows Esplora file. Le condivisioni file di Azure consentono di archiviare dati di file server e dati dell'applicazione per utilizzo generico nel cloud. Il backup di una condivisione file di Azure è una funzionalità incorporata e può essere ulteriormente migliorato Backup di Azure.

Questo articolo è in particolare in particolare sui passaggi per la migrazione. Per altre informazioni sull'Sincronizzazione file di Azure prima della migrazione, vedere gli articoli seguenti:

* [Sincronizzazione file di Azure panoramica](./storage-sync-files-planning.md "Panoramica")
* [Sincronizzazione file di Azure distribuzione](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Chiave di crittografia dei dati del servizio StorSimple

Quando si configura l'appliance StorSimple per la prima volta, viene generata una "chiave di crittografia dei dati del servizio" e viene richiesto di archiviare in modo sicuro la chiave. Questa chiave viene usata per crittografare tutti i dati nell'account di archiviazione di Azure associato in cui l'appliance StorSimple archivia i file.

La "chiave di crittografia dei dati del servizio" è necessaria per una corretta migrazione. È ora possibile recuperare questa chiave dai record, una per ogni appliance nell'inventario.

Se non è possibile trovare le chiavi nei record, è possibile generare una nuova chiave dall'appliance. Ogni appliance ha una chiave di crittografia univoca.

#### <a name="change-the-service-data-encryption-key"></a>Modificare la chiave DEK del servizio

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Quando si decide come connettersi all'appliance StorSimple, tenere presente quanto segue:
>
> * La connessione tramite una sessione HTTPS è l'opzione più sicura e consigliata.
> * La connessione diretta alla console seriale del dispositivo è protetta ma la connessione alla console seriale sugli switch di rete non lo è.
> * Le connessioni di sessione HTTP sono un'opzione ma *non sono crittografate.* Non sono consigliate a meno che non vengano usate all'interno di una rete chiusa e attendibile.

### <a name="storsimple-volume-backups"></a>Backup di volumi StorSimple

StorSimple offre backup differenziali a livello di volume. Le condivisioni file di Azure hanno anche questa possibilità, denominata snapshot di condivisione.
I processi di migrazione possono spostare solo i backup, non i dati dal volume live. Il backup più recente deve quindi essere sempre in elenco di backup spostati in una migrazione.

Decidere se è necessario spostare i backup meno recenti durante la migrazione.
La procedura consigliata consiste nel mantenere l'elenco il più piccolo possibile, in modo che i processi di migrazione completino più velocemente.

Per identificare i backup critici di cui è necessario eseguire la migrazione, creare un elenco di controllo dei criteri di backup. Ad esempio:
* Backup più recente. Nota: il backup più recente deve sempre far parte di questo elenco.
* Un backup al mese per 12 mesi.
* Un backup all'anno per tre anni. 

In un secondo momento, quando si creano i processi di migrazione, è possibile usare questo elenco per identificare i backup dei volumi StorSimple esatti di cui è necessario eseguire la migrazione per soddisfare i requisiti nell'elenco.

> [!CAUTION]
> La selezione di **più di 50** backup di volumi StorSimple non è supportata.
> I processi di migrazione possono solo spostare i backup, mai i dati dal volume live. Di conseguenza, il backup più recente è più vicino ai dati in tempo reale e pertanto deve sempre far parte dell'elenco di backup da spostare in una migrazione.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Eseguire il mapping dei volumi StorSimple esistenti alle condivisioni file di Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Numero di account di archiviazione

La migrazione trarrà probabilmente vantaggio da una distribuzione di più account di archiviazione, ognuno dei quali contiene un numero minore di condivisioni file di Azure.

Se le condivisioni file sono altamente attive (utilizzate da molti utenti o applicazioni), due condivisioni file di Azure potrebbero raggiungere il limite di prestazioni dell'account di archiviazione. Per questo, la procedura consigliata consiste nel eseguire la migrazione a più account di archiviazione, ognuno con singole condivisioni file e in genere non più di due o tre condivisioni per ogni account di archiviazione.

Una procedura consigliata consiste nel distribuire gli account di archiviazione con una condivisione file ciascuno. È possibile eseguire il pool di più condivisioni file di Azure nello stesso account di archiviazione, se sono presenti condivisioni di archiviazione.

Queste considerazioni si applicano più [all'accesso diretto al cloud](#direct-share-access-vs-azure-file-sync) (tramite una macchina virtuale o un servizio di Azure) che a Sincronizzazione file di Azure. Se si prevede di usare esclusivamente Sincronizzazione file di Azure in queste condivisioni, il raggruppamento di più in un singolo account di archiviazione di Azure è possibile. In futuro, potrebbe essere necessario spostare in modalità lift-and-shift un'app nel cloud che quindi accederebbe direttamente a una condivisione file. Questo scenario potrebbe trarre vantaggio dalla presenza di operazioni di I/O al secondo e velocità effettiva più elevate. Oppure è possibile iniziare a usare un servizio in Azure che potrebbe anche trarre vantaggio da operazioni di I/O al secondo e velocità effettiva più elevate.

Se è stato creato un elenco delle condivisioni, eseguire il mapping di ogni condivisione all'account di archiviazione in cui risiederà.

> [!IMPORTANT]
> Decidere un'area di Azure e assicurarsi che ogni account di archiviazione e Sincronizzazione file di Azure risorsa corrisponda all'area selezionata.
> Non configurare ora le impostazioni di rete e firewall per gli account di archiviazione. L'esecuzione di queste configurazioni a questo punto renderebbe impossibile la migrazione. Configurare queste impostazioni di archiviazione di Azure al termine della migrazione.

### <a name="phase-1-summary"></a>Riepilogo della fase 1

Al termine della fase 1:

* Si dispone di una buona panoramica dei dispositivi e dei volumi StorSimple.
* Il Data Manager è pronto per accedere ai volumi StorSimple nel cloud perché è stata recuperata la "chiave di crittografia dei dati del servizio" per ogni dispositivo StorSimple.
* Si dispone di un piano per il quale è necessario eseguire la migrazione di volumi e backup (se presenti oltre il più recente).
* Si sa come eseguire il mapping dei volumi al numero appropriato di condivisioni file e account di archiviazione di Azure.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fase 2: Distribuire risorse di archiviazione e migrazione di Azure

Questa sezione illustra le considerazioni relative alla distribuzione dei diversi tipi di risorse necessari in Azure. Alcuni conteneranno i dati dopo la migrazione e altri sono necessari esclusivamente per la migrazione. Non iniziare a distribuire le risorse fino a quando non è stato finalizzato il piano di distribuzione. È difficile, a volte impossibile, modificare determinati aspetti delle risorse di Azure dopo che sono state distribuite.

### <a name="deploy-storage-accounts"></a>Distribuire gli account di archiviazione

È probabile che sia necessario distribuire diversi account di archiviazione di Azure. Ognuna contenerà un numero inferiore di condivisioni file di Azure, in base al piano di distribuzione, completate nella sezione precedente di questo articolo. Passare al portale di Azure per [distribuire gli account di archiviazione pianificati.](../common/storage-account-create.md#create-a-storage-account) Prendere in considerazione l'aderenza alle impostazioni di base seguenti per qualsiasi nuovo account di archiviazione.

> [!IMPORTANT]
> Non configurare ora le impostazioni di rete e firewall per gli account di archiviazione. Rendere queste configurazioni a questo punto renderebbe impossibile una migrazione. Configurare queste impostazioni di archiviazione di Azure al termine della migrazione.

#### <a name="subscription"></a>Subscription

È possibile usare la stessa sottoscrizione usata per la distribuzione StorSimple o una sottoscrizione diversa. L'unica limitazione è che la sottoscrizione deve essere nello stesso tenant Azure Active Directory tenant della sottoscrizione StorSimple. Provare a spostare la sottoscrizione StorSimple nel tenant appropriato prima di avviare una migrazione. È possibile spostare solo l'intera sottoscrizione. Le singole risorse StorSimple non possono essere spostate in un tenant o in una sottoscrizione diversa.

#### <a name="resource-group"></a>Resource group

I gruppi di risorse sono utili per l'organizzazione delle risorse e le autorizzazioni di gestione dell'amministratore. Altre informazioni sui gruppi [di risorse in Azure.](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)

#### <a name="storage-account-name"></a>Nome account di archiviazione

Il nome dell'account di archiviazione diventerà parte di un URL e presenta alcune limitazioni di caratteri. Nella convenzione di denominazione si consideri che i nomi degli account di archiviazione devono essere univoci nel mondo, consentire solo lettere minuscole e numeri, richiedere da 3 a 24 caratteri e non consentire caratteri speciali come trattini o caratteri di sottolineatura. Per altre informazioni, vedere Regole [di denominazione delle risorse di archiviazione di Azure.](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)

#### <a name="location"></a>Località

La località o l'area di Azure di un account di archiviazione è molto importante. Se si usa Sincronizzazione file di Azure, tutti gli account di archiviazione devono essere nella stessa area della risorsa del servizio di sincronizzazione archiviazione. L'area di Azure selezionata deve essere vicina o centrale ai server e agli utenti locali. Dopo aver distribuito la risorsa, non è possibile modificarne l'area.

È possibile scegliere un'area diversa da cui si trovano attualmente i dati di StorSimple (account di archiviazione).

> [!IMPORTANT]
> Se si sceglie un'area diversa dalla posizione corrente dell'account di archiviazione StorSimple, [durante](https://azure.microsoft.com/pricing/details/bandwidth) la migrazione verranno applicati i costi in uscita. I dati lasciano l'area di StorSimple e immettono la nuova area dell'account di archiviazione. Non vengono applicati costi per la larghezza di banda se si rimane all'interno della stessa area di Azure.

#### <a name="performance"></a>Prestazioni

È possibile scegliere l'archiviazione Premium (SSD) per le condivisioni file di Azure o l'archiviazione Standard. L'archiviazione Standard [include diversi livelli per una condivisione file](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share). L'archiviazione Standard è l'opzione giusta per la maggior parte dei clienti che esegnere la migrazione da StorSimple.

Non si è ancora sicuri?

* Scegliere Archiviazione Premium se sono necessarie le [prestazioni di una condivisione file premium di Azure.](understanding-billing.md#provisioned-model)
* Scegliere l'archiviazione standard per i carichi di lavoro file server per utilizzo generico, inclusi i dati ad accesso elevato e i dati di archiviazione. Scegliere anche l'archiviazione standard se l'unico carico di lavoro nella condivisione nel cloud verrà Sincronizzazione file di Azure.

#### <a name="account-kind"></a>Tipologia account

* Per l'archiviazione Standard, *scegliere Archiviazione V2 (utilizzo generico v2).*
* Per le condivisioni file Premium, scegliere *FileStorage*.

#### <a name="replication"></a>Replica

Sono disponibili diverse impostazioni di replica. Altre informazioni sui diversi tipi di replica.

Scegliere solo una delle due opzioni seguenti:

* *Archiviazione con ridondanza* locale .
* *Archiviazione con ridondanza della* zona , che non è disponibile in tutte le aree di Azure.

> [!NOTE]
> Solo i tipi di ridondanza LRS e ZRS sono compatibili con le condivisioni file di Azure con capacità di 100 TiB di grandi dimensioni.

L'archiviazione con ridondanza geografica in tutte le varianti non è attualmente supportata. È possibile cambiare il tipo di ridondanza in un secondo momento e passare all'archiviazione con ridondanza geografica quando il supporto per il tipo arriva in Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Abilitare condivisioni file con capacità 100 TiB

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Immagine che mostra la scheda Avanzate nel portale di Azure per la creazione di un account di archiviazione.":::
    :::column-end:::
    :::column:::
        Nella sezione **Avanzate della** creazione guidata nuovo account di archiviazione nel portale di Azure è possibile abilitare il supporto di condivisioni **file** di grandi dimensioni in questo account di archiviazione. Se questa opzione non è disponibile, è molto probabile che sia stato selezionato il tipo di ridondanza errato. Assicurarsi di selezionare solo LRS o ZRS perché questa opzione diventi disponibile.
    :::column-end:::
:::row-end:::

La scelta delle condivisioni file con capacità 100 TiB di grandi dimensioni offre diversi vantaggi:

* Le prestazioni sono notevolmente aumentate rispetto alle condivisioni file con capacità 5 TiB più piccole, ad esempio 10 volte le operazioni di I/O al secondo.
* La migrazione verrà completata in modo notevolmente più veloce.
* Assicurarsi che una condivisione file abbia capacità sufficiente per contenere tutti i dati di cui si eseguirà la migrazione, inclusi i backup differenziali della capacità di archiviazione necessari.
* Viene trattata la crescita futura.

### <a name="azure-file-shares"></a>Condivisioni file di Azure

Dopo aver creato gli account di archiviazione, passare alla sezione **Condivisione file** dell'account di archiviazione e distribuire il numero appropriato di condivisioni file di Azure in base al piano di migrazione dalla fase 1. Prendere in considerazione l'aderenza alle impostazioni di base seguenti per le nuove condivisioni file in Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Screenshot portale di Azure che mostra la nuova interfaccia utente della condivisione file.":::
    :::column-end:::
    :::column:::
        </br>**Nome**</br>Sono supportate lettere minuscole, numeri e trattini.</br></br>**Quota**</br>La quota qui è paragonabile a una quota rigida SMB in un'istanza di Windows Server. La procedura consigliata consiste nel non impostare una quota in questo caso perché la migrazione e gli altri servizi avranno esito negativo quando viene raggiunta la quota.</br></br>**Livelli**</br>Selezionare **Transazione ottimizzata** per la nuova condivisione file. Durante la migrazione, si verificheranno molte transazioni. È più efficiente dal punto di vista dei costi modificare il livello in un secondo momento al livello più adatto al carico di lavoro.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

La risorsa di Azure che contenerà i processi di migrazione è denominata **StorSimple Data Manager**. Selezionare **Nuova risorsa** e cercarla. Quindi selezionare **Crea**

Questa risorsa temporanea viene usata per l'orchestrazione. È possibile effettuare il deprovisioning al termine della migrazione. Deve essere distribuito nella stessa sottoscrizione, gruppo di risorse e area dell'account di archiviazione StorSimple.

### <a name="azure-file-sync"></a>Sincronizzazione file di Azure

Con Sincronizzazione file di Azure, è possibile aggiungere la memorizzazione nella cache locale dei file a cui si accede più di frequente. Analogamente alle capacità di memorizzazione nella cache di StorSimple, la funzionalità di cloud a livelli Sincronizzazione file di Azure offre latenza di accesso locale in combinazione con un maggiore controllo sulla capacità della cache disponibile nell'istanza di Windows Server e nella sincronizzazione multis sito. Se l'obiettivo è avere una cache locale, nella rete locale preparare una macchina virtuale Windows Server (sono supportati anche server fisici e cluster di failover) con capacità di archiviazione collegata direttamente sufficiente.

> [!IMPORTANT]
> Non configurare ancora Sincronizzazione file di Azure. È meglio configurare i Sincronizzazione file di Azure al termine della migrazione della condivisione. La distribuzione Sincronizzazione file di Azure non deve iniziare prima della fase 4 di una migrazione.

### <a name="phase-2-summary"></a>Riepilogo della fase 2

Alla fine della fase 2, saranno stati distribuiti gli account di archiviazione e tutte le condivisioni file di Azure tra di essi. Sarà anche disponibile una risorsa StorSimple Data Manager locale. Si userà quest'ultimo nella fase 3 quando si configurano i processi di migrazione.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: Creare ed eseguire un processo di migrazione

Questa sezione descrive come configurare un processo di migrazione ed eseguire con attenzione il mapping delle directory in un volume StorSimple che deve essere copiato nella condivisione file di Azure di destinazione selezionata. Per iniziare, passare al StorSimple Data Manager, trovare **Definizioni processo** nel menu e selezionare **+ Definizione processo**. Il tipo di archiviazione di destinazione corretto è il valore predefinito: **Condivisione file di Azure.**

![Tipi di processo di migrazione storSimple serie 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Screenshot della finestra di dialogo Definizioni portale di Azure con una nuova finestra di dialogo Definizioni processo in cui viene chiesto il tipo di processo: Copia in una condivisione file o in un contenitore BLOB.")

:::row:::
    :::column:::
        ![Processo di migrazione StorSimple serie 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Screenshot del modulo di creazione del nuovo processo per un processo di migrazione.")
    :::column-end:::
    :::column:::
        **Nome definizione processo**</br>Questo nome dovrebbe indicare il set di file da spostare. È consigliabile assegnargli un nome simile a quello della condivisione file di Azure. </br></br>**Posizione in cui viene eseguito il processo**</br>Quando si seleziona un'area, è necessario selezionare la stessa area dell'account di archiviazione StorSimple o, se non è disponibile, un'area vicina. </br></br><h3>Source (Sorgente)</h3>**Sottoscrizione di origine**</br>Selezionare la sottoscrizione in cui archiviare la risorsa Gestione dispositivi StorSimple. </br></br>**Risorsa StorSimple**</br>Selezionare Gestione dispositivi StorSimple con cui è registrata l'appliance. </br></br>**Chiave di crittografia dei dati del servizio**</br>Controllare questa [sezione precedente in questo articolo](#storsimple-service-data-encryption-key) nel caso in cui non sia possibile individuare la chiave nei record. </br></br>**Dispositivo**</br>Selezionare il dispositivo StorSimple che contiene il volume di cui si vuole eseguire la migrazione. </br></br>**Volume**</br>Selezionare il volume di origine. In seguito si deciderà se si vuole eseguire la migrazione dell'intero volume o sottodirectory nella condivisione file di Azure di destinazione.</br></br> **Backup di volumi**</br>È possibile selezionare *Seleziona backup del volume* per scegliere backup specifici da spostare come parte di questo processo. Una prossima sezione [dedicata di questo articolo](#selecting-volume-backups-to-migrate) illustra in dettaglio il processo.</br></br><h3>Destinazione</h3>Selezionare la sottoscrizione, l'account di archiviazione e la condivisione file di Azure come destinazione del processo di migrazione.</br></br><h3>Mapping delle directory</h3>[Una sezione dedicata di questo articolo](#directory-mapping)illustra tutti i dettagli pertinenti.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Selezione dei backup del volume di cui eseguire la migrazione

Esistono aspetti importanti per la scelta dei backup di cui è necessario eseguire la migrazione:

- I processi di migrazione possono solo spostare i backup, non i dati da un volume live. Il backup più recente è quindi il più vicino ai dati in tempo reale e deve essere sempre presente nell'elenco dei backup spostati in una migrazione.
- Assicurarsi che il backup più recente sia recente per mantenere il delta nella condivisione live il più piccolo possibile. Potrebbe essere opportuno attivare e completare manualmente un altro backup del volume prima di creare un processo di migrazione. Un piccolo delta della condivisione in tempo reale migliorerà l'esperienza di migrazione. Se questo delta può essere zero = non sono state apportate altre modifiche al volume StorSimple dopo l'esecuzione del backup più recente nell'elenco, la fase 5: il cut-over dell'utente verrà notevolmente semplificato e accelerato.
- I backup devono essere riprodotti nella condivisione file di Azure dal meno recente al **più recente.** Non è possibile ordinare un backup precedente nell'elenco dei backup nella condivisione file di Azure dopo l'esecuzione di un processo di migrazione. È pertanto necessario assicurarsi che l'elenco dei backup sia completo *prima* di creare un processo. 
- Questo elenco di backup in un processo non può essere modificato dopo la creazione del processo, anche se il processo non è mai stato eseguito. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Screenshot del modulo di creazione del nuovo processo che illustra in dettaglio la parte in cui vengono selezionati i backup StorSimple per la migrazione." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Per selezionare i backup del volume StorSimple per il processo di migrazione, selezionare il modulo *Selezionare i backup del volume* nel modulo di creazione del processo.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Immagine che mostra che nella metà superiore del pannello per la selezione dei backup sono elencati tutti i backup disponibili. Un backup selezionato verrà disattivato in questo elenco e aggiunto a un secondo elenco nella metà inferiore del pannello. Può anche essere eliminato di nuovo." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Quando si apre il pannello di selezione del backup, questo viene separato in due elenchi. Nel primo elenco vengono visualizzati tutti i backup disponibili. È possibile espandere e restringere il set di risultati filtrando per un intervallo di tempo specifico. (vedere la sezione successiva) </br></br>Un backup selezionato verrà visualizzato in grigio e verrà aggiunto a un secondo elenco nella metà inferiore del pannello. Nel secondo elenco vengono visualizzati tutti i backup selezionati per la migrazione. È anche possibile rimuovere di nuovo un backup selezionato in errore.
        > [!CAUTION]
        > È necessario selezionare **tutti i** backup di cui si vuole eseguire la migrazione. Non è possibile aggiungere backup meno recenti in un secondo momento. Non è possibile modificare il processo per modificare la selezione dopo la creazione del processo.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Screenshot che mostra la selezione di un intervallo di tempo del pannello di selezione del backup." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Per impostazione predefinita, l'elenco viene filtrato per visualizzare i backup del volume StorSimple negli ultimi sette giorni per semplificare la selezione del backup più recente. Per i backup più avanti nel passato, usare il filtro dell'intervallo di tempo nella parte superiore del pannello. È possibile eseguire una selezione da un filtro esistente o impostare un intervallo di tempo personalizzato per filtrare solo i backup evasi durante questo periodo.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> La selezione di più di 50 backup di volumi StorSimple non è supportata. I processi con un numero elevato di backup potrebbero non riuscire.

### <a name="directory-mapping"></a>Mapping di directory

Il mapping della directory è facoltativo per il processo di migrazione. Se si lascia vuota la *sezione,* tutti i file e le cartelle nella radice del volume StorSimple verranno spostati nella radice della condivisione file di Azure di destinazione. Nella maggior parte dei casi, l'archiviazione del contenuto di un intero volume in una condivisione file di Azure non è l'approccio migliore. Spesso è meglio suddividere il contenuto di un volume tra più condivisioni file in Azure. Se non è già stato fatto un piano, vedere Eseguire prima il [mapping del volume StorSimple alle condivisioni file di Azure.](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Come parte del piano di migrazione, è possibile che si sia deciso che le cartelle in un volume StorSimple devono essere suddivise tra più condivisioni file di Azure. In questo caso, è possibile eseguire questa suddivisione per:

1. Definizione di più processi per la migrazione delle cartelle in un volume. Ognuno di essi avrà la stessa origine del volume StorSimple, ma una condivisione file di Azure diversa come destinazione.
1. Specificare con precisione le cartelle del volume StorSimple di cui eseguire la migrazione nella condivisione file specificata usando la sezione **Mapping** directory del modulo di creazione del processo e seguendo la [semantica di mapping specifica](#semantic-elements).

> [!IMPORTANT]
> I percorsi e le espressioni di mapping in questo modulo non possono essere convalidati quando viene inviato il modulo. Se i mapping vengono specificati in modo non corretto, un processo potrebbe non riuscire completamente o produrre un risultato indesiderato. In tal caso, è in genere meglio eliminare la condivisione file di Azure, crearla di nuovo e quindi correggere le istruzioni di mapping in un nuovo processo di migrazione per la condivisione. L'esecuzione di un nuovo processo con istruzioni di mapping fisse può correggere le cartelle omesse e portarle nella condivisione esistente. Tuttavia, in questo modo è possibile risolvere solo le cartelle omesse a causa di errori di ortografia del percorso.

#### <a name="semantic-elements"></a>Elementi semantici

Un mapping è espresso da sinistra a destra: [\source path] \> [\target path].

|Carattere semantico          | Significato  |
|:---------------------------|:---------|
| **\\**                     | Indicatore del livello radice.       |
| **\>**                     | Operatore [Source] e [target-mapping].     |
|**\|** o RETURN (nuova riga) | Separatore di due istruzioni di mapping delle cartelle. </br>In alternativa, è possibile omettere questo carattere e selezionare **INVIO** per ottenere l'espressione di mapping successiva nella propria riga.        |

### <a name="examples"></a>Esempio
Sposta il contenuto della cartella *Dati utente* nella radice della condivisione file di destinazione:
``` console
\User data > \
```
Sposta l'intero contenuto del volume in un nuovo percorso nella condivisione file di destinazione:
``` console
\ > \Apps\HR tracker
```
Sposta il contenuto della cartella di origine in un nuovo percorso nella condivisione file di destinazione:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Ordina più percorsi di origine in una nuova struttura di directory:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Regole semantiche

* Specificare sempre i percorsi di cartella relativi al livello radice.
* Iniziare ogni percorso di cartella con un indicatore di livello radice " \\ ".
* Non includere lettere di unità.
* Quando si specificano più percorsi, i percorsi di origine o di destinazione non possono sovrapporsi:</br>
   Esempio di sovrapposizione del percorso di origine non valido:</br>
    *\\cartella\1 > \\ cartella*</br>
    *\\cartella \\ 1 \\ 2 > \\ cartella2*</br>
   Esempio di sovrapposizione del percorso di destinazione non valido:</br>
   *\\cartella > \\*</br>
   *\\folder2 > \\*</br>
* Le cartelle di origine che non esistono verranno ignorate.
* Verranno create strutture di cartelle che non esistono nella destinazione.
* Analogamente a Windows, i nomi delle cartelle non eseguono la distinzione tra maiuscole e minuscole, mantendo la distinzione tra maiuscole e minuscole.

> [!NOTE]
> Il contenuto della *cartella \System Volume Information* e del *$Recycle.Bin* nel volume StorSimple non verrà copiato dal processo di migrazione.

### <a name="run-a-migration-job"></a>Eseguire un processo di migrazione

I processi di migrazione sono elencati in *Definizioni* dei processi Data Manager risorsa distribuita in un gruppo di risorse.
Nell'elenco delle definizioni di processo selezionare il processo che si vuole eseguire.

Nel pannello del processo visualizzato è possibile visualizzare le esecuzioni del processo nell'elenco inferiore. Inizialmente, questo elenco sarà vuoto. Nella parte superiore del pannello è presente un comando denominato *Esegui processo*. Questo comando non eseguirà immediatamente il processo, ma apre il pannello **Esecuzione processo:**

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Immagine che mostra il pannello di esecuzione del processo con un controllo a discesa aperto, che mostra i backup selezionati di cui eseguire la migrazione. Il backup meno recente è evidenziato e deve essere selezionato per primo." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        In questa versione ogni processo deve essere eseguito più volte. </br></br>**È necessario iniziare con il backup meno recente dall'elenco dei backup di cui si vuole eseguire la migrazione.** (evidenziato nell'immagine)</br></br>Eseguire di nuovo il processo, tutte le volte che sono stati selezionati backup, ogni volta su un backup progressivamente più recente.
        </br></br>
        > [!CAUTION]
        > È fondamentale eseguire il processo di migrazione con il backup meno recente selezionato prima e poi di nuovo, ogni volta con un backup progressivamente più recente. È sempre necessario mantenere manualmente l'ordine dei backup, dal meno recente al più recente.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Riepilogo della fase 3

Al termine della fase 3, sarà stato eseguito almeno uno dei processi di migrazione dai volumi StorSimple alle condivisie file di Azure. Lo stesso processo di migrazione verrà eseguito più volte, dai backup meno recenti ai più recenti di cui è necessario eseguire la migrazione. È ora possibile concentrarsi sulla configurazione di Sincronizzazione file di Azure per la condivisione (al termine dei processi di migrazione per una condivisione) o sull'indirizzamento dell'accesso alla condivisione per information worker e app alla condivisione file di Azure.

## <a name="phase-4-access-your-azure-file-shares"></a>Fase 4: Accedere alle condivisioni file di Azure

Esistono due strategie principali per accedere alle condivisioni file di Azure:

* **Sincronizzazione file di Azure:** distribuire [Sincronizzazione file di Azure](#deploy-azure-file-sync) in un'istanza di Windows Server locale. Sincronizzazione file di Azure offre tutti i vantaggi di una cache locale, proprio come StorSimple.
* **Direct-share-access**: [distribuire direct-share-access.](#deploy-direct-share-access) Usare questa strategia se lo scenario di accesso per una determinata condivisione file di Azure non trarrà vantaggio dalla memorizzazione nella cache locale o non sarà più possibile ospitare un'istanza di Windows Server locale. In questo caso, gli utenti e le app continueranno ad accedere alle condivisioni SMB tramite il protocollo SMB. Queste condivisioni non sono più in un server locale, ma direttamente nel cloud.

Nella fase [1](#phase-1-prepare-for-migration) di questa guida è già stata scelta l'opzione più adatta.

La parte restante di questa sezione è incentrata sulle istruzioni di distribuzione.

### <a name="deploy-azure-file-sync"></a>Distribuire Sincronizzazione file di Azure

È il momento di distribuire una parte del Sincronizzazione file di Azure.

1. Creare la Sincronizzazione file di Azure cloud.
1. Distribuire Sincronizzazione file di Azure'agente nel server locale.
1. Registrare il server con la risorsa cloud.

Non creare ancora gruppi di sincronizzazione. La configurazione della sincronizzazione con una condivisione file di Azure deve essere eseguita solo dopo il completamento dei processi di migrazione a una condivisione file di Azure. Se si è iniziato a usare Sincronizzazione file di Azure prima del completamento della migrazione, la migrazione risultava inutilmente difficile perché non era facile capire quando era il momento di avviare un cut-over.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuire la Sincronizzazione file di Azure cloud

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se si vuole modificare l'area di Azure in cui si trovano i dati al termine della migrazione, distribuire il servizio di sincronizzazione archiviazione nella stessa area degli account di archiviazione di destinazione per questa migrazione.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Distribuire un'istanza di Windows Server locale

* Creare Windows Server 2019 (almeno 2012R2) come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server. Non riutilizzare il server che si sta frontendo a StorSimple 8100 o 8600.
* Effettuare il provisioning o aggiungere risorse di archiviazione collegate direttamente. L'archiviazione collegata alla rete non è supportata.

È consigliabile assegnare alla nuova istanza di Windows Server una quantità di archiviazione uguale o maggiore rispetto a quella dell'appliance StorSimple 8100 o 8600 disponibile localmente per la memorizzazione nella cache. L'istanza di Windows Server verrà usata nello stesso modo in cui è stata usata l'appliance StorSimple. Se ha la stessa quantità di spazio di archiviazione dell'appliance, l'esperienza di memorizzazione nella cache dovrebbe essere simile, se non uguale. È possibile aggiungere o rimuovere spazio di archiviazione dall'istanza di Windows Server in base alle proprie impostazioni. Questa funzionalità consente di ridimensionare le dimensioni del volume locale e la quantità di spazio di archiviazione locale disponibile per la memorizzazione nella cache.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Preparare l'istanza di Windows Server per la sincronizzazione file

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Configurare Sincronizzazione file di Azure nell'istanza di Windows Server

L'istanza di Windows Server locale registrata deve essere pronta e connessa a Internet per questo processo.

> [!IMPORTANT]
> Prima di procedere, è necessario completare la migrazione storSimple di file e cartelle nella condivisione file di Azure. Assicurarsi che non siano state apportate altre modifiche alla condivisione file.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Assicurarsi di attivare il cloud a livelli. Il cloud a livelli è Sincronizzazione file di Azure funzionalità che consente al server locale di avere meno capacità di archiviazione rispetto a quella archiviata nel cloud, ma di avere lo spazio dei nomi completo disponibile. Anche i dati interessanti in locale vengono memorizzati nella cache in locale per prestazioni di accesso locale veloci. Un altro motivo per attivare il cloud a livelli in questo passaggio è che non si vuole sincronizzare il contenuto dei file in questa fase. Solo lo spazio dei nomi deve essere in fase di spostamento.

### <a name="deploy-direct-share-access"></a>Distribuire direct-share-access

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Questo video è una guida e una demo per esporre in modo sicuro le condivisioni file di Azure direttamente agli information worker e alle app in cinque semplici passaggi.</br>
        Il video fa riferimento alla documentazione dedicata per alcuni argomenti:

* [Panoramica dell'identità](storage-files-active-directory-overview.md)
* [Come aggiungere un dominio a un account di archiviazione](storage-files-identity-auth-active-directory-enable.md)
* [Panoramica della rete per le condivisioni file di Azure](storage-files-networking-overview.md)
* [Come configurare gli endpoint pubblici e privati](storage-files-networking-endpoints.md)
* [Come configurare una VPN S2S](storage-files-configure-s2s-vpn.md)
* [Come configurare una VPN Windows P2S](storage-files-configure-p2s-vpn-windows.md)
* [Come configurare una VPN P2S Linux](storage-files-configure-p2s-vpn-linux.md)
* [Come configurare l'inoltro DNS](storage-files-networking-dns.md)
* [Configurare DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Riepilogo della fase 4

In questa fase sono stati creati ed eseguiti più processi di migrazione nell'StorSimple Data Manager. Tali processi hanno eseguito la migrazione di file e cartelle a condivisioni file di Azure. Sono stati anche distribuiti Sincronizzazione file di Azure o preparato gli account di rete e di archiviazione per l'accesso diretto alla condivisione.

## <a name="phase-5-user-cut-over"></a>Fase 5: Cut-over dell'utente

Questa fase riguarda il wrapping della migrazione:

* Pianificare il tempo di inattività.
* Recuperare le modifiche apportate agli utenti e alle app prodotte sul lato StorSimple mentre i processi di migrazione nella fase 3 sono in esecuzione.
* Eseguire il failover degli utenti nella nuova istanza di Windows Server con Sincronizzazione file di Azure o le condivisioni file di Azure tramite direct-share-access.

### <a name="plan-your-downtime"></a>Pianificare il tempo di inattività

Questo approccio di migrazione richiede tempo di inattività per utenti e app. L'obiettivo è quello di mantenere al minimo i tempi di inattività. Le considerazioni seguenti possono essere utili:

* Mantenere i volumi StorSimple disponibili durante l'esecuzione dei processi di migrazione.
* Al termine dell'esecuzione dei processi di migrazione dei dati per una condivisione, è possibile rimuovere l'accesso utente (almeno l'accesso in scrittura) dai volumi o dalle condivisioni StorSimple. Un roboCopy finale recoglierà la condivisione file di Azure. È quindi possibile eseguire il cut over degli utenti. La posizione in cui si esegue RoboCopy varia a seconda che si sia scelto di usare Sincronizzazione file di Azure o direct-share-access. La sezione successiva di RoboCopy illustra tale argomento.
* Dopo aver completato il recupero di RoboCopy, è possibile esporre il nuovo percorso agli utenti direttamente tramite la condivisione file di Azure o una condivisione SMB in un'istanza di Windows Server con Sincronizzazione file di Azure. Spesso una distribuzione DFS-N consente di eseguire un cut-over in modo rapido ed efficiente. Mantenerà coerenti gli indirizzi di condivisione esistenti e punti a un nuovo percorso che contiene i file e le cartelle migrati.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Determinare quando lo spazio dei nomi è stato completamente sincronizzato con il server

Quando si usa Sincronizzazione file di Azure per una condivisione file di Azure, è importante determinare che l'intero spazio dei nomi ha completato il download nel *server* prima di avviare qualsiasi RoboCopy locale. Il tempo necessario per scaricare lo spazio dei nomi dipende dal numero di elementi nella condivisione file di Azure. Esistono due metodi per determinare se lo spazio dei nomi è completamente arrivato sul server.

#### <a name="azure-portal"></a>Portale di Azure

È possibile usare il portale di Azure per vedere quando lo spazio dei nomi è completamente arrivato.

* Accedere al portale di Azure e passare al gruppo di sincronizzazione. Controllare lo stato di sincronizzazione del gruppo di sincronizzazione e dell'endpoint server.
* La direzione interessante è il download. Se viene appena effettuato il provisioning dell'endpoint server, verrà visualizzata la sincronizzazione **iniziale**, che indica che lo spazio dei nomi è ancora in fase di incesto.
Dopo aver modificato qualsiasi elemento, ad esempio **Sincronizzazione iniziale,** lo spazio dei nomi verrà popolato completamente nel server. È ora possibile procedere con una RoboCopy locale.

#### <a name="windows-server-event-viewer"></a>Windows Server Visualizzatore eventi

È anche possibile usare il Visualizzatore eventi nell'istanza di Windows Server per indicare quando lo spazio dei nomi è completamente arrivato.

1. Aprire il **Visualizzatore eventi** e passare ad **Applicazioni e servizi**.
1. Passare a e aprire **Microsoft\FileSync\Agent\Telemetry.**
1. Cercare l'evento **9102** più recente, che corrisponde a una sessione di sincronizzazione completata.
1. Selezionare **Dettagli** e confermare che si sta esaminando un evento in cui il **valore syncDirection** è **Download**.
1. Per il momento in cui lo spazio dei nomi ha completato il download nel server, sarà presente un singolo evento con **Scenario**, il **valore FullGhostedSync** e **HResult**  =  **0**.
1. Se si perde tale evento, è anche possibile cercare altri **eventi 9102** con **SyncDirection** Download e  =   **Scenario**  =  **"RegularSync".** La ricerca di uno di questi eventi indica anche che lo spazio dei nomi ha terminato il download e la sincronizzazione è stata completata in sessioni di sincronizzazione regolari, indipendentemente dal fatto che ci sia qualcosa da sincronizzare o meno in questo momento.

### <a name="a-final-robocopy"></a>RoboCopy finale

A questo punto, esistono differenze tra l'istanza di Windows Server locale e l'appliance StorSimple 8100 o 8600.

1. È necessario recuperare le modifiche che gli utenti o le app hanno prodotto sul lato StorSimple mentre la migrazione era in corso.
1. Per i casi in cui si usa Sincronizzazione file di Azure: l'appliance StorSimple ha una cache popolata rispetto all'istanza di Windows Server con solo uno spazio dei nomi senza contenuto di file archiviato in locale al momento. La roboCopy finale consente di avviare la cache Sincronizzazione file di Azure locale tramite il pull del contenuto dei file memorizzati nella cache locale quanto disponibile e può adattarsi al server Sincronizzazione file di Azure locale.
1. Alcuni file potrebbero essere stati lasciati dal processo di migrazione a causa di caratteri non validi. In questo caso, copiarli nell'Sincronizzazione file di Azure di Windows Server abilitata. In un secondo momento, è possibile regolarli in modo che possano essere sincronizzati. Se non si usa Sincronizzazione file di Azure per una determinata condivisione, è meglio rinominare i file con caratteri non validi nel volume StorSimple. Eseguire quindi RoboCopy direttamente nella condivisione file di Azure.

> [!WARNING]
> Robocopy in Windows Server 2019 attualmente si verifica un problema che causa la ricopia dei file a livelli da Sincronizzazione file di Azure nel server di destinazione dall'origine e il nuovo caricamento in Azure quando si usa la funzione /MIR di robocopy. È fondamentale usare Robocopy in un server Windows diverso da 2019. Una scelta preferita è Windows Server 2016. Questa nota verrà aggiornata se il problema viene risolto tramite Windows Update.

> [!WARNING]
> Non *è necessario avviare* RoboCopy prima che il server abbia lo spazio dei nomi per una condivisione file di Azure scaricata completamente. Per altre informazioni, vedere [Determinare quando lo spazio dei nomi è stato scaricato completamente nel server.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 Si vogliono copiare solo i file modificati dopo l'ultima esecuzione del processo di migrazione e i file che non sono stati spostati in precedenza in questi processi. È possibile risolvere il problema in base al motivo per cui non sono stati spostati in un secondo momento nel server, al termine della migrazione. Per altre informazioni, vedere risoluzione [Sincronizzazione file di Azure risoluzione dei problemi.](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

RoboCopy ha diversi parametri. L'esempio seguente illustra un comando completato e un elenco di motivi per la scelta di questi parametri.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Sfondo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di eseguire multi-thread. Il valore predefinito è 8 e il valore massimo è 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Restituisce lo stato nel file LOG come UNICODE (sovrascrive il log esistente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Output nella finestra della console. Utilizzato in combinazione con l'output in un file di log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Omette la registrazione dello stato di avanzamento per mantenere il log leggibile.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / B
   :::column-end:::
   :::column span="1":::
      Esegue RoboCopy nella stessa modalità utilizzata da un'applicazione di backup. Consente a RoboCopy di spostare i file per cui l'utente corrente non dispone delle autorizzazioni.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di considerare solo i differenziali tra l'origine (appliance StorSimple) e la destinazione (directory di Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Garantisce che la fedeltà sia mantenuta in determinati scenari mirror.</br>Esempio: tra due esecuzioni Robocopy un file verifica una modifica ACL e un aggiornamento dell'attributo, ad esempio è contrassegnato *anche come hidden*. Senza /IT, la modifica ACL può essere persa da Robocopy e quindi non trasferita nel percorso di destinazione.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fedeltà della copia del file (l'impostazione predefinita è /COPY:DAT), flag di copia: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACL, O=Owner information, U=aUditing information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIA TUTTE le informazioni sul file (equivalente a /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fedeltà per la copia delle directory (il valore predefinito è /DCOPY:DA), flag di copia: D=Data, A=Attributes, T=Timestamps.
   :::column-end:::
:::row-end:::

Quando si configurano i percorsi di origine e di destinazione del comando RoboCopy, assicurarsi di esaminare la struttura dell'origine e della destinazione per assicurarsi che corrispondano. Se è stata usata la funzionalità di mapping delle directory del processo di migrazione, la struttura della directory radice potrebbe essere diversa dalla struttura del volume StorSimple. In questo caso, potrebbero essere necessari più processi RoboCopy, uno per ogni sottodirectory. Se non si è certi che il comando verrà eseguito come previsto, è possibile usare il *parametro /L,* che simula il comando senza apportare effettivamente modifiche.

Questo comando RoboCopy usa /MIR, quindi non sposta i file uguali (ad esempio, i file a livelli). Tuttavia, se il percorso di origine e di destinazione non è corretto, /MIR elimina anche le strutture di directory nell'istanza di Windows Server o nella condivisione file di Azure che non sono presenti nel percorso di origine StorSimple. Devono corrispondere esattamente perché il processo RoboCopy raggiunga l'obiettivo previsto di aggiornare il contenuto migrato con le modifiche più recenti apportate mentre la migrazione è in corso.

Consultare il file di log di RoboCopy per verificare se i file sono rimasti. Se si verificano problemi, correggerli ed eseguire di nuovo il comando RoboCopy. Non eseguire il deprovisioning delle risorse StorSimple prima di risolvere i problemi in sospeso per i file o le cartelle a cui si è affezionati.

Se non si usa Sincronizzazione file di Azure per memorizzare nella cache la condivisione file di Azure specifica in questione, ma si è scelto l'accesso diretto alla condivisione:

1. [Montare la condivisione file di Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) come unità di rete in un computer Windows locale.
1. Eseguire RoboCopy tra StorSimple e la condivisione file di Azure montata. Se i file non vengono copiati, correggerne i nomi sul lato StorSimple per rimuovere i caratteri non validi. Quindi riprovare RoboCopy. Il comando RoboCopy elencato in precedenza può essere eseguito più volte senza causare inutili richiami a StorSimple.

### <a name="user-cut-over"></a>Cut-over utente

Se si usa Sincronizzazione file di Azure, è probabile che sia necessario creare le condivisioni SMB in tale istanza di Windows Server abilitata per Sincronizzazione file di Azure che corrispondono alle condivisioni disponibili nei volumi StorSimple. È possibile caricare in anticipo questo passaggio ed eseguire questa operazione in precedenza per non perdere tempo qui. Prima di questo punto, tuttavia, è necessario assicurarsi che nessuno abbia accesso per apportare modifiche all'istanza di Windows Server.

Se si dispone di una distribuzione DFS-N, è possibile fare in modo che il DFN-Namespaces ai nuovi percorsi delle cartelle del server. Se non si dispone di una distribuzione DFS-N e l'appliance 8100 o 8600 è stata creata localmente con un'istanza di Windows Server, è possibile rimuovere il server dal dominio. Aggiungere quindi il dominio alla nuova Sincronizzazione file di Azure di Windows Server abilitata per l'accesso remoto. Durante questo processo, assegnare al server lo stesso nome del server e i nomi di condivisione del server precedente in modo che il cut-over rimanga trasparente per gli utenti, i criteri di gruppo e gli script.

Altre informazioni su [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Effettuare il deprovisioning

Quando si esegue il deprovisioning di una risorsa, si perde l'accesso alla configurazione della risorsa e dei relativi dati. Il deprovisioning non può essere annullato. Non procedere fino a quando non si è confermato che:

* La migrazione è stata completata.
* Non sono presenti dipendenze per i file, le cartelle o i backup del volume StorSimple di cui si sta per eseguire il deprovisioning.

Prima di iniziare, è consigliabile osservare la nuova distribuzione Sincronizzazione file di Azure nell'ambiente di produzione per un po'. Questo tempo offre la possibilità di risolvere eventuali problemi che potrebbero verificarsi. Dopo aver osservato la distribuzione Sincronizzazione file di Azure per almeno alcuni giorni, è possibile iniziare a eseguire il deprovisioning delle risorse nell'ordine seguente:

1. Deprovisioning StorSimple Data Manager risorsa tramite il portale di Azure. Tutti i processi DTS verranno eliminati con esso. Non sarà possibile recuperare facilmente i log di copia. Se sono importanti per i record, recuperarli prima di eseguire il deprovisioning.
1. Assicurarsi che sia stata eseguita la migrazione delle appliance fisiche StorSimple e quindi annullarne la registrazione. Se non si è completamente certi che sia stata eseguita la migrazione, non procedere. Se si deprovisioning di queste risorse mentre sono ancora necessarie, non sarà possibile ripristinare i dati o la relativa configurazione.<br>Facoltativamente, è possibile eseguire prima il deprovisioning della risorsa volume StorSimple, che pulirà i dati nell'appliance. Questa operazione può richiedere diversi giorni **e non azzererà** forensemente i dati nell'appliance. Se questo è importante per l'utente, gestire l'azzeramento del disco separatamente dal deprovisioning delle risorse e in base ai criteri.
1. Se non sono più presenti altri dispositivi registrati in Gestione dispositivi StorSimple, è possibile procedere con la rimozione di tale risorsa di Gestione dispositivi.
1. È ora possibile eliminare l'account di archiviazione StorSimple in Azure. Anche in questo caso, arrestare e verificare che la migrazione sia stata completata e che nulla e nessuno dipenda da questi dati prima di procedere.
1. Scollegare l'appliance fisica StorSimple dal data center.
1. Se si è proprietari dell'appliance StorSimple, è possibile riciclarlo da PC. Se il dispositivo è stato noleggiato, informare il minore e restituire il dispositivo in base alle esigenze.

La migrazione è stata completata.

> [!NOTE]
> Sono ancora presenti domande o si sono verificati problemi?</br>
> Per assistenza, vedere AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Passaggi successivi

* Acquisire maggiore familiarità con [Sincronizzazione file di Azure: aka.ms/AFS](./storage-sync-files-planning.md).
* Comprendere la flessibilità dei criteri [di cloud a livelli.](storage-sync-cloud-tiering-overview.md)
* [Abilitare Backup di Azure](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) nelle condivisioni file di Azure per pianificare gli snapshot e definire le pianificazioni di conservazione dei backup.
* Se nel portale di Azure alcuni file non vengono sincronizzati in modo [](storage-sync-files-troubleshoot.md) permanente, vedere la guida alla risoluzione dei problemi per i passaggi per risolvere questi problemi.
