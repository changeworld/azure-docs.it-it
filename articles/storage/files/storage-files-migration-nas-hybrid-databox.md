---
title: Migrazione di NAS locale a Sincronizzazione file di Azure tramite Azure DataBox
description: Informazioni su come eseguire la migrazione di file da un percorso di archiviazione collegato alla rete locale (NAS) a una distribuzione cloud ibrida con Sincronizzazione file di Azure tramite Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584069"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Usare DataBox per eseguire la migrazione da archiviazione collegata alla rete (NAS) a una distribuzione cloud ibrida con Sincronizzazione file di Azure

Questo articolo di migrazione è uno dei numerosi che coinvolgono le parole chiave NAS, Sincronizzazione file di Azure e Azure DataBox. Controllare se questo articolo è applicabile al proprio scenario:

> [!div class="checklist"]
> * Origine dati: archiviazione collegata alla rete (NAS)
> * Route di migrazione: &rArr; &rArr; la sincronizzazione della condivisione file di Azure databox NAS &rArr; con Windows Server
> * Caching dei file locali: Sì, l'obiettivo finale è una distribuzione Sincronizzazione file di Azure.

Se lo scenario è diverso, esaminare la [tabella delle guide alla migrazione](storage-files-migration-overview.md#migration-guides).

Sincronizzazione file di Azure funziona nei percorsi DAS (Direct Attached Storage) e non supporta la sincronizzazione con i percorsi di archiviazione collegata alla rete (NAS).
Questo fatto rende necessario eseguire la migrazione dei file e in questo articolo viene illustrata la pianificazione e l'esecuzione di una migrazione di questo tipo.

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è spostare le condivisioni presenti sul dispositivo NAS in un server Windows. Usare quindi Sincronizzazione file di Azure per una distribuzione cloud ibrida. Questa migrazione deve essere eseguita in modo da garantire l'integrità dei dati e della disponibilità di produzione durante la migrazione. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="migration-overview"></a>Panoramica della migrazione

Il processo di migrazione è costituito da diverse fasi. Dovrai distribuire gli account di archiviazione di Azure e le condivisioni file, distribuire un'istanza locale di Windows Server, configurare Sincronizzazione file di Azure, eseguire la migrazione con RoboCopy e infine eseguire il taglio. Le sezioni seguenti descrivono in dettaglio le fasi del processo di migrazione.

> [!TIP]
> Se si torna a questo articolo, usare la navigazione sul lato destro per passare alla fase di migrazione in cui è stato interrotto.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificare il numero di condivisioni file di Azure necessarie

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fase 2: distribuire le risorse di archiviazione di Azure

In questa fase, consultare la tabella di mapping della fase 1 e usarla per eseguire il provisioning del numero corretto di account di archiviazione di Azure e delle condivisioni file al suo interno.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fase 3: determinare il numero di appliance DataBox di Azure necessarie

Avviare questo passaggio solo dopo aver completato la fase precedente. A questo punto, è necessario creare le risorse di archiviazione di Azure (account di archiviazione e condivisioni file). Durante l'ordine di DataBox, è necessario specificare in quali account di archiviazione il DataBox sta muovendo i dati.

In questa fase è necessario eseguire il mapping dei risultati del piano di migrazione dalla fase precedente ai limiti delle opzioni DataBox disponibili. Queste considerazioni consentiranno di creare un piano per il quale è necessario scegliere le opzioni di DataBox e il numero di questi elementi necessari per spostare le condivisioni NAS in condivisioni file di Azure.

Per determinare il numero di dispositivi di cui è necessario il tipo, considerare questi limiti importanti:

* Qualsiasi DataBox di Azure può spostare i dati in un massimo di 10 account di archiviazione. 
* Ogni opzione DataBox è dotata di capacità utilizzabile. Vedere le [Opzioni di databox](#databox-options).

Consultare il piano di migrazione per il numero di account di archiviazione che si è deciso di creare e le condivisioni in ognuna di esse. Esaminare quindi le dimensioni di ogni condivisione del NAS. Combinando queste informazioni sarà possibile ottimizzare e decidere quale appliance deve inviare i dati a quali account di archiviazione. È possibile avere due dispositivi DataBox per spostare i file nello stesso account di archiviazione, ma non suddividere il contenuto di una singola condivisione file tra due databox.

### <a name="databox-options"></a>Opzioni di DataBox

Per una migrazione standard, è necessario scegliere una combinazione di queste tre opzioni di DataBox: 

* DataBox disks Microsoft invierà uno e un massimo di cinque dischi SSD con una capacità di 8 TiB ciascuno, per un totale massimo di 40 TiB. La capacità utilizzabile è inferiore del 20%, a causa della crittografia e del sovraccarico file system. Per ulteriori informazioni, vedere la [documentazione di databox disks](../../databox/data-box-disk-overview.md).
* DataBox questa è l'opzione più comune. Un appliance DataBox rinforzato, che funziona in modo simile a un NAS, verrà spedito all'utente. Ha una capacità utilizzabile di 80 TiB. Per ulteriori informazioni, vedere la [documentazione di databox](../../databox/data-box-overview.md).
* DataBox Heavy questa opzione offre un'appliance DataBox Rugged su ruote, che funziona in modo simile a un NAS, con una capacità di 1 PiB. La capacità utilizzabile è inferiore del 20%, a causa della crittografia e del sovraccarico file system. Per ulteriori informazioni, vedere [databox Heavy Documentation](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fase 4: effettuare il provisioning di un Windows Server locale adatto

Durante l'attesa dell'arrivo di DataBox di Azure, è possibile iniziare a esaminare le esigenze di uno o più server Windows che verranno usati con Sincronizzazione file di Azure.

* Creare una macchina virtuale di Windows Server 2019, almeno 2012R2, come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.
* Effettuare il provisioning o aggiungere l'archiviazione collegata direttamente (DAS rispetto a NAS, che non è supportata).

La configurazione della risorsa (calcolo e RAM) del server Windows distribuito dipende principalmente dal numero di elementi (file e cartelle) che verranno sincronizzati. Una configurazione di prestazioni più elevata è consigliata in caso di dubbi.

[Informazioni su come ridimensionare un server Windows in base al numero di elementi (file e cartelle) che è necessario sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> L'articolo collegato in precedenza presenta una tabella con un intervallo di memoria del server (RAM). È possibile orientarsi verso il numero più piccolo per il server, ma si prevede che la sincronizzazione iniziale possa richiedere molto più tempo.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fase 5: copiare i file nella DataBox

Quando arriva il DataBox, è necessario configurare il DataBox in linea di visibilità per il dispositivo NAS. Seguire la documentazione di installazione per il tipo di DataBox ordinato.

* [Configurare Data Box Disk](../../databox/data-box-quickstart-portal.md)
* [Configurare Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Configurare Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

A seconda del tipo di DataBox, è possibile che siano disponibili strumenti di copia DataBox. A questo punto, non sono consigliate per le migrazioni in condivisioni file di Azure perché non copiano i file con la massima fedeltà per la DataBox. In alternativa, usare RoboCopy.

Quando arriva il DataBox, le condivisioni SMB pre-provisioning sono disponibili per ogni account di archiviazione specificato al momento dell'ordinamento.

* Se i file vengono inseriti in una condivisione file di Azure Premium, sarà presente una condivisione SMB per ogni account di archiviazione Premium "archiviazione file".
* Se i file vengono inseriti in un account di archiviazione standard, saranno disponibili tre condivisioni SMB per account di archiviazione standard (utilizzo generico V1 e GPv2). Solo le condivisioni file che terminano con `_AzFiles` sono rilevanti per la migrazione. Ignorare le condivisioni BLOB in blocchi e di pagine.

Attenersi alla procedura descritta nella documentazione di Azure DataBox:

1. [Connettersi al Data Box](../../databox/data-box-deploy-copy-data.md)
1. Copiare i dati nel Data Box
1. [Preparare il DataBox per la partenza ad Azure](../../databox/data-box-deploy-picked-up.md)

La documentazione di DataBox collegata specifica un comando RoboCopy. Tuttavia, il comando non è adatto per mantenere la fedeltà completa di file e cartelle. Usare invece questo comando:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fase 6: distribuire la risorsa Sincronizzazione file di Azure cloud

Prima di continuare con questa guida, attendere che tutti i file siano arrivati alle condivisioni file di Azure corrette. Il processo di spedizione e di inserimento dei dati di DataBox riprenderà tempo.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fase 7: distribuire l'agente di Sincronizzazione file di Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fase 8: configurare Sincronizzazione file di Azure in Windows Server

Per questo processo, è necessario che il server Windows locale registrato sia pronto e connesso a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Attivare la funzionalità di suddivisione in livelli nel cloud e selezionare "solo spazio dei nomi" nella sezione download iniziale.

> [!IMPORTANT]
> La suddivisione in livelli nel cloud è la funzionalità AFS che consente al server locale di avere una capacità di archiviazione inferiore a quella archiviata nel cloud, ma è disponibile lo spazio dei nomi completo. I dati localmente interessanti vengono anche memorizzati nella cache localmente per ottenere prestazioni di accesso rapido. La suddivisione in livelli nel cloud è una funzionalità facoltativa per ogni Sincronizzazione file di Azure "endpoint server". È necessario utilizzare questa funzionalità se non si dispone di una capacità del disco locale sufficiente nel server Windows per contenere tutti i dati del cloud e se si desidera evitare il download di tutti i dati dal cloud.

Ripetere i passaggi per la creazione del gruppo di sincronizzazione e l'aggiunta della cartella del server corrispondente come endpoint server per tutte le condivisioni file o i percorsi server di Azure che devono essere configurati per la sincronizzazione. Attendere il completamento della sincronizzazione dello spazio dei nomi. Nella sezione seguente viene illustrato in dettaglio come è possibile assicurarsi che.

> [!NOTE]
> Dopo la creazione di un endpoint server, la sincronizzazione funziona. Tuttavia, la sincronizzazione deve enumerare (individuare) i file e le cartelle spostati tramite DataBox nella condivisione file di Azure. A seconda delle dimensioni dello spazio dei nomi, l'operazione può richiedere molto tempo prima che lo spazio dei nomi del cloud venga visualizzato sul server.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fase 9: attendere che lo spazio dei nomi venga visualizzato completamente nel server

È necessario attendere i passaggi successivi della migrazione che il server ha scaricato completamente lo spazio dei nomi dalla condivisione cloud. Se si inizia a migrare i file troppo presto sul server, è possibile rischiare i caricamenti non necessari e persino i conflitti di sincronizzazione dei file.

Per stabilire se il server ha completato la sincronizzazione del download iniziale, aprire Visualizzatore eventi nel server Windows di sincronizzazione e usare il registro eventi di telemetria Sincronizzazione file di Azure.
Il registro eventi di telemetria si trova in Visualizzatore eventi in applicazioni e Services\Microsoft\FileSync\Agent.

Cercare l'evento 9102 più recente. L'ID evento 9102 viene registrato una volta completata una sessione di sincronizzazione. Nel testo dell'evento è presente un campo per la direzione di sincronizzazione del download. ( `HResult` deve essere zero e i file scaricati anche).
 
Si desidera visualizzare due eventi consecutivi di questo tipo e di contenuto per indicare che il server ha terminato il download dello spazio dei nomi. È OK se sono presenti eventi diversi che generano eventi tra 2 9102.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fase 10: recupero di RoboCopy dal NAS

Una volta che il server ha completato la sincronizzazione iniziale dell'intero spazio dei nomi dalla condivisione cloud, è possibile procedere con questo passaggio. È fondamentale che questo passaggio sia completo, prima di continuare con questo passaggio. Per informazioni dettagliate, vedere la sezione precedente.

In questo passaggio si eseguiranno processi di RoboCopy per recuperare le condivisioni cloud con le ultime modifiche apportate al NAS dal momento in cui sono state compilate le condivisioni nella DataBox.
Questo recupero di RoboCopy può terminare rapidamente o richiedere un po' di tempo, a seconda della varianza che si è verificata nelle condivisioni NAS.

> [!WARNING]
> A causa di un comportamento di RoboCopy regressione in Windows Server 2019, l'opzione/MIR di RoboCopy non è compatibile con una directory di destinazione a più livelli. Per questa fase della migrazione non è necessario usare il client Windows Server 2019 o Windows 10. Usare RoboCopy in un Windows Server 2016 intermedio.

L'approccio di migrazione di base è RoboCopy dal dispositivo NAS al server Windows e Sincronizzazione file di Azure alle condivisioni file di Azure.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

1. Identificare la prima posizione nel dispositivo NAS.
1. Identificare la cartella corrispondente nel server Windows in cui è già stata configurata Sincronizzazione file di Azure.
1. Avviare la copia con RoboCopy

Il comando RoboCopy seguente consente di copiare solo le differenze (file e cartelle aggiornati) dalla risorsa di archiviazione NAS alla cartella di destinazione di Windows Server. Windows Server li sincronizza quindi con le condivisioni file di Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Se è stato effettuato il provisioning di una quantità minore di spazio di archiviazione nel server Windows rispetto a quella dei file, è stata configurata la suddivisione in livelli nel cloud. Con l'esaurimento del volume locale di Windows Server, la suddivisione in [livelli cloud](storage-sync-cloud-tiering-overview.md) verrà avviata e i file di livello che sono già stati sincronizzati correttamente. La suddivisione in livelli cloud genererà spazio sufficiente per continuare la copia dal dispositivo NAS. La suddivisione in livelli nel cloud viene controllata una volta all'ora per vedere cosa è stato sincronizzato e liberare spazio su disco per raggiungere lo spazio disponibile nel volume del 99%.
È possibile che RoboCopy debba spostare numerosi file, oltre a disporre di una risorsa di archiviazione locale per su Windows Server. In media, è possibile prevedere che RoboCopy si sposti molto più velocemente di quanto Sincronizzazione file di Azure possa caricare i file e distribuirli a livelli dal volume locale. RoboCopy avrà esito negativo. Si consiglia di usare le condivisioni in una sequenza che lo impedisce. Ad esempio, non avviare i processi RoboCopy per tutte le condivisioni contemporaneamente o solo le condivisioni che rientrano nella quantità corrente di spazio disponibile sul server Windows, per citarne alcune. La novità è che l'opzione/MIR sposta solo i Delta e dopo lo spostamento di un Delta, un processo riavviato non dovrà spostare di nuovo il file.

### <a name="user-cut-over"></a>Riduzione utente

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni continuano ad accedere ai file del NAS e potenzialmente modificarli. È possibile che RoboCopy abbia elaborato una directory, passi alla successiva, quindi un utente nel percorso di origine (NAS) aggiunge, modifica o Elimina un file che ora non verrà elaborato in questa esecuzione di RoboCopy corrente. Si tratta di un comportamento previsto.

La prima esecuzione consiste nello trasferire la maggior parte dei dati di varianza in Windows Server e nel cloud tramite Sincronizzazione file di Azure. Questa prima copia può richiedere molto tempo, a seconda di:

* larghezza di banda di caricamento
* la velocità della rete locale e il numero di come il numero di thread RoboCopy corrisponde in modo ottimale
* il numero di elementi (file e cartelle) che devono essere elaborati da RoboCopy e Sincronizzazione file di Azure

Una volta completata l'esecuzione iniziale, eseguire di nuovo il comando.

Una seconda volta che si esegue RoboCopy per la stessa condivisione, l'operazione verrà completata più velocemente, perché è necessario solo trasferire le modifiche apportate dall'ultima esecuzione. È possibile eseguire processi ripetuti per la stessa condivisione.

Quando si considera il tempo di inattività accettabile, è necessario rimuovere l'accesso utente alle condivisioni basate su NAS. Questa operazione può essere eseguita da qualsiasi passaggio che impedisce agli utenti di modificare la struttura di file e cartelle e il contenuto. Un esempio consiste nel puntare la DFS-Namespace a una posizione non esistente o modificare gli ACL radice nella condivisione.

Eseguire un ultimo round RoboCopy. Rileverà le modifiche che potrebbero essere state perse.
Il tempo necessario per il passaggio finale dipende dalla velocità dell'analisi RoboCopy. È possibile stimare l'intervallo di tempo (uguale al tempo di inattività) misurando il tempo impiegato per l'esecuzione precedente.

Creare una condivisione nella cartella di Windows Server ed eventualmente modificare la distribuzione di DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB NAS. Se si dispone di un NAS di livello aziendale aggiunto al dominio, i SID utente corrisponderanno automaticamente mentre gli utenti sono presenti nel Active Directory e RoboCopy copia i file e i metadati con la massima fedeltà. Se sono stati usati utenti locali nel NAS, è necessario ricrearli come utenti locali di Windows Server ed eseguire il mapping dei SID esistenti con RoboCopy spostati nel server Windows ai SID del nuovo utente locale di Windows Server.

È stata completata la migrazione di una condivisione o di un gruppo di condivisioni in una radice o un volume comune. (A seconda del mapping dalla fase 1)

È possibile provare a eseguire alcune di queste copie in parallelo. Si consiglia di elaborare l'ambito di una condivisione file di Azure alla volta.

## <a name="troubleshoot"></a>Risolvere problemi

Il problema più probabile in cui è possibile eseguire è che il comando RoboCopy ha esito negativo con *"volume full"* sul lato server di Windows. La suddivisione in livelli nel cloud funziona una volta ogni ora per l'evacuazione del contenuto dal disco locale di Windows Server, sincronizzato. Il suo obiettivo è raggiungere il 99% di spazio disponibile nel volume.

Consentire la sincronizzazione dello stato di avanzamento e la suddivisione in livelli nel cloud liberare spazio su disco. È possibile osservare che in Esplora file in Windows Server.

Quando Windows Server dispone di sufficiente capacità disponibile, la riesecuzione del comando risolverà il problema. Niente si interrompe quando si entra in questa situazione ed è possibile procedere con fiducia. L'unica conseguenza è l'inconveniente di eseguire di nuovo il comando.

Per la risoluzione dei problemi Sincronizzazione file di Azure, vedere il collegamento nella sezione seguente.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni sulle condivisioni file di Azure e Sincronizzazione file di Azure. Gli articoli seguenti consentono di comprendere le opzioni avanzate, le procedure consigliate e contengono anche la guida alla risoluzione dei problemi. Questi articoli sono collegati alla [documentazione di condivisione file di Azure](storage-files-introduction.md) nel modo appropriato.

* [Panoramica sulla migrazione](storage-files-migration-overview.md)
* [Panoramica di AFS](./storage-sync-files-planning.md)
* [Guida alla distribuzione di AFS](./storage-how-to-create-file-share.md)
* [Risoluzione dei problemi di AFS](storage-sync-files-troubleshoot.md)
