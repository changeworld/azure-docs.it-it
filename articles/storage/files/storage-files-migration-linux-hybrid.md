---
title: Migrazione di Linux a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di file da un percorso server Linux a una distribuzione cloud ibrida con Sincronizzazione file di Azure e condivisioni file di Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564255"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Eseguire la migrazione da Linux a una distribuzione cloud ibrida con Sincronizzazione file di Azure

Questo articolo di migrazione è uno dei numerosi che coinvolgono le parole chiave NFS e Sincronizzazione file di Azure. Controllare se questo articolo è applicabile al proprio scenario:

> [!div class="checklist"]
> * Origine dati: archiviazione collegata alla rete (NAS)
> * Route di migrazione: server Linux con SAMBA &rArr; Windows Server 2012R2 o versione successiva &rArr; con le condivisioni file di Azure
> * Caching dei file locali: Sì, l'obiettivo finale è una distribuzione Sincronizzazione file di Azure.

Se lo scenario è diverso, esaminare la [tabella delle guide alla migrazione](storage-files-migration-overview.md#migration-guides).

Sincronizzazione file di Azure funziona nelle istanze di Windows Server con l'archiviazione diretta (DAS). Non supporta la sincronizzazione da e verso client Linux o una condivisione SMB (Server Message Block) remota o NFS (Network File System).

Di conseguenza, la trasformazione dei servizi file in una distribuzione ibrida rende necessario eseguire una migrazione a Windows Server. Questo articolo illustra la pianificazione e l'esecuzione di una migrazione di questo tipo.

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è spostare le condivisioni presenti sul server Samba Linux in un'istanza di Windows Server. Usare quindi Sincronizzazione file di Azure per una distribuzione cloud ibrida. Questa migrazione deve essere eseguita in modo da garantire l'integrità dei dati e della disponibilità di produzione durante la migrazione. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="migration-overview"></a>Panoramica della migrazione

Come indicato nell' [articolo Panoramica della migrazione](storage-files-migration-overview.md)di file di Azure, è importante usare lo strumento di copia e l'approccio corretti. Il server Samba Linux espone le condivisioni SMB direttamente nella rete locale. Robocopy, integrato in Windows Server, è il modo migliore per spostare i file in questo scenario di migrazione.

Se non si esegue Samba sul server Linux e si vuole eseguire la migrazione delle cartelle in una distribuzione ibrida in Windows Server, è possibile usare gli strumenti di copia di Linux anziché Robocopy. Tenere presente le funzionalità di fedeltà dello strumento di copia. Vedere la [sezione Nozioni di base sulla migrazione](storage-files-migration-overview.md#migration-basics) nell'articolo Panoramica della migrazione per informazioni su cosa cercare in uno strumento di copia.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: identificare il numero di condivisioni file di Azure necessarie

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 2: effettuare il provisioning di un'istanza di Windows Server appropriata in locale

* Creare un'istanza di Windows Server 2019 come macchina virtuale o server fisico. Windows Server 2012 R2 è il requisito minimo. È supportato anche un cluster di failover di Windows Server.
* Effettuare il provisioning o aggiungere l'archiviazione diretta (DAS). L'archiviazione NAS (Network Attached Storage) non è supportata.

  La quantità di spazio di archiviazione di cui si esegue il provisioning può essere inferiore a quella attualmente in uso nel server Samba di Linux, se si usa la funzionalità di suddivisione in [livelli sincronizzazione file di Azure cloud](storage-sync-cloud-tiering-overview.md) . 

La quantità di spazio di archiviazione di cui si esegue il provisioning può essere inferiore a quella attualmente in uso nel server Samba Linux. Questa scelta di configurazione richiede anche l'uso della funzionalità di suddivisione in [livelli cloud](storage-sync-cloud-tiering-overview.md) di sincronizzazione file di Azure. Tuttavia, quando si copiano i file dal più ampio spazio del server Samba Linux al volume di Windows Server più piccolo in una fase successiva, sarà necessario lavorare in batch:

  1. Spostare un set di file che si integrano sul disco.
  2. Consente di attivare la sincronizzazione file e la suddivisione in livelli nel cloud.
  3. Quando viene creato più spazio libero nel volume, procedere con il batch successivo di file. In alternativa, rivedere il comando RoboCopy nella prossima [sezione di Robocopy](#phase-7-robocopy) per l'uso del nuovo `/LFSM` Switch. L'uso di `/LFSM` può semplificare notevolmente i processi di Robocopy, ma non è compatibile con altri commutatori Robocopy da cui dipende.
    
  È possibile evitare questo approccio di invio in batch eseguendo il provisioning dello spazio equivalente nell'istanza di Windows Server che i file occupano sul server Samba Linux. Si consiglia di abilitare la deduplicazione in Windows. Se non si vuole eseguire il commit permanente di questa quantità elevata di memoria nell'istanza di Windows Server, è possibile ridurre le dimensioni del volume dopo la migrazione e prima di modificare i criteri di suddivisione in livelli nel cloud. Che consente di creare una cache locale più piccola delle condivisioni file di Azure.

La configurazione della risorsa (calcolo e RAM) dell'istanza di Windows Server distribuita dipende principalmente dal numero di elementi (file e cartelle) che verranno sincronizzati. Se si verificano problemi, è consigliabile procedere con una configurazione con prestazioni superiori.

[Informazioni su come ridimensionare un'istanza di Windows Server in base al numero di elementi (file e cartelle) che è necessario sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> L'articolo collegato in precedenza presenta una tabella con un intervallo di memoria del server (RAM). È possibile orientarsi verso il numero più piccolo per il server, ma si prevede che la sincronizzazione iniziale possa richiedere molto più tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: distribuire la risorsa Sincronizzazione file di Azure cloud

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: distribuire le risorse di archiviazione di Azure

In questa fase, consultare la tabella di mapping della fase 1 e usarla per eseguire il provisioning del numero corretto di account di archiviazione di Azure e delle condivisioni file al suo interno.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: distribuire l'agente di Sincronizzazione file di Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fase 6: configurare Sincronizzazione file di Azure nella distribuzione di Windows Server

Per questo processo, è necessario che l'istanza di Windows Server locale registrata sia pronta e connessa a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> La suddivisione in livelli nel cloud è la funzionalità Sincronizzazione file di Azure che consente al server locale di avere una capacità di archiviazione inferiore a quella archiviata nel cloud, ma è disponibile lo spazio dei nomi completo. I dati localmente interessanti vengono anche memorizzati nella cache localmente per ottenere prestazioni di accesso rapido. La suddivisione in livelli nel cloud è una funzionalità facoltativa per ogni endpoint del server Sincronizzazione file di Azure.

> [!WARNING]
> Se è stato effettuato il provisioning di meno spazio di archiviazione sui volumi di Windows Server rispetto ai dati usati nel server Samba Linux, la suddivisione in livelli nel cloud è obbligatoria. Se non si attiva la suddivisione in livelli nel cloud, il server non renderà disponibile spazio per archiviare tutti i file. Impostare i criteri di suddivisione in livelli, temporaneamente per la migrazione, fino al 99% di spazio disponibile per un volume. Assicurarsi di tornare alle impostazioni di suddivisione in livelli nel cloud al termine della migrazione e impostare i criteri su un livello più utile a lungo termine.

Ripetere i passaggi per la creazione del gruppo di sincronizzazione e l'aggiunta della cartella del server corrispondente come endpoint server per tutte le condivisioni file di Azure e i percorsi del server che devono essere configurati per la sincronizzazione.

Dopo la creazione di tutti gli endpoint server, la sincronizzazione funziona. È possibile creare un file di test e visualizzarne la sincronizzazione dal percorso del server alla condivisione file di Azure connessa (come descritto dall'endpoint cloud nel gruppo di sincronizzazione).

Entrambi i percorsi, le cartelle del server e le condivisioni file di Azure, sono altrimenti vuoti e in attesa di dati. Nel passaggio successivo si inizierà a copiare i file nell'istanza di Windows Server per Sincronizzazione file di Azure per spostarli nel cloud. Se è stata abilitata la suddivisione in livelli nel cloud, il server inizierà a eseguire il livello dei file se si esaurisce la capacità nei volumi locali.

## <a name="phase-7-robocopy"></a>Fase 7: Robocopy

L'approccio di migrazione di base consiste nell'utilizzare Robocopy per copiare i file e utilizzare Sincronizzazione file di Azure per eseguire la sincronizzazione.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

1. Identificare la prima posizione nel server Samba Linux.
1. Identificare la cartella corrispondente nell'istanza di Windows Server in cui è già stata configurata Sincronizzazione file di Azure.
1. Avviare la copia utilizzando Robocopy.

Il comando Robocopy seguente consente di copiare i file dalla risorsa di archiviazione del server Samba Linux alla cartella di destinazione di Windows Server. Windows Server lo sincronizza con le condivisioni file di Azure. 

Se è stato effettuato il provisioning di una quantità minore di spazio di archiviazione nell'istanza di Windows Server rispetto a quella dei file sul server Samba Linux, è stata configurata la suddivisione in livelli nel cloud. Con l'esaurimento del volume locale di Windows Server, la suddivisione in [livelli cloud](storage-sync-cloud-tiering-overview.md) viene avviata e i file di livello già sincronizzati. La suddivisione in livelli cloud genererà spazio sufficiente per continuare la copia dal server Samba Linux. La suddivisione in livelli nel cloud viene verificata una volta all'ora per vedere cosa è stato sincronizzato e liberare spazio su disco per raggiungere i criteri del 99% di spazio disponibile per un volume.

È possibile che Robocopy sposti i file più velocemente di quanto sia possibile sincronizzare al cloud e al livello localmente, causando un esaurimento dello spazio su disco locale. Robocopy avrà quindi esito negativo. Si consiglia di usare le condivisioni in una sequenza che impedisce il problema. Si consideri, ad esempio, di non avviare i processi Robocopy per tutte le condivisioni contemporaneamente. In alternativa, provare a trasferire le condivisioni che rientrano nella quantità corrente di spazio disponibile nell'istanza di Windows Server. Se il processo Robocopy ha esito negativo, è sempre possibile eseguire di nuovo il comando fino a quando si usa l'opzione di mirroring/ripulitura seguente:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: riduzione dell'utente

Quando si esegue il comando Robocopy per la prima volta, gli utenti e le applicazioni continuano ad accedere ai file nel server Samba Linux e potenzialmente modificarli. È possibile che Robocopy abbia elaborato una directory e passi alla successiva, quindi un utente nel percorso di origine (Linux) aggiunge, modifica o Elimina un file che ora non verrà elaborato in questa esecuzione di Robocopy corrente. Si tratta di un comportamento previsto.

La prima esecuzione consiste nello trasferire la maggior parte dei dati nell'istanza di Windows Server e nel cloud tramite Sincronizzazione file di Azure. Questa prima copia può richiedere molto tempo, a seconda di:

* Larghezza di banda per il download.
* Larghezza di banda di caricamento.
* La velocità della rete locale e il numero di come il numero di thread Robocopy corrisponda in modo ottimale.
* Il numero di elementi (file e cartelle) che Robocopy e Sincronizzazione file di Azure devono elaborare.

Al termine dell'esecuzione iniziale, eseguire di nuovo il comando.

Si conclude più velocemente la seconda volta, perché deve trasportare solo le modifiche che si sono verificate dall'ultima esecuzione. Durante questo secondo, l'esecuzione di nuove modifiche può comunque essere accumulata.

Ripetere questa procedura fino a quando non si è soddisfatti del tempo necessario per completare un'operazione di Robocopy per una posizione specifica all'interno di una finestra accettabile per i tempi di inattività.

Quando si considera il tempo di inattività accettabile e si è pronti a portare offline la località di Linux, è possibile modificare gli ACL nella radice della condivisione in modo che gli utenti non possano più accedere al percorso. In alternativa, è possibile eseguire qualsiasi altro passaggio appropriato che impedisce la modifica del contenuto in questa cartella sul server Linux.

Eseguire un ultimo round Robocopy. Rileverà tutte le modifiche che potrebbero essere state perse. Il tempo necessario per il passaggio finale dipende dalla velocità dell'analisi Robocopy. È possibile stimare l'intervallo di tempo (uguale al tempo di inattività) misurando il tempo impiegato per l'esecuzione precedente.

Creare una condivisione nella cartella di Windows Server ed eventualmente modificare la distribuzione di DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione delle condivisioni SMB del server Samba Linux. Se sono stati usati utenti locali nel server Samba Linux, è necessario ricreare questi utenti come utenti locali di Windows Server. È inoltre necessario eseguire il mapping dei SID esistenti che Robocopy ha spostato nell'istanza di Windows Server nei SID dei nuovi utenti locali di Windows Server. Se sono stati usati account Active Directory e ACL, Robocopy li sposterà così come sono e non sarà necessaria alcuna azione aggiuntiva.

È stata completata la migrazione di una condivisione o di un gruppo di condivisioni in una radice o un volume comune (a seconda del mapping della fase 1).

È possibile provare a eseguire alcune di queste copie in parallelo. Si consiglia di elaborare l'ambito di una condivisione file di Azure alla volta.

> [!WARNING]
> Dopo aver spostato tutti i dati dal server Samba Linux all'istanza di Windows Server e aver completato la migrazione, tornare a *tutti*  i gruppi di sincronizzazione nella portale di Azure. Modificare la percentuale di spazio disponibile per il volume di suddivisione in livelli nel cloud in modo più appropriato per l'utilizzo della cache, ad esempio il 20%. 

I criteri per lo spazio disponibile nel volume di suddivisione in livelli cloud agiscono su un livello di volume con potenzialmente più endpoint server sincronizzati. Se si dimentica di regolare lo spazio libero su un solo endpoint server, la sincronizzazione continuerà ad applicare la regola più restrittiva e tenterà di mantenere lo spazio libero su disco al 99%. La cache locale potrebbe non funzionare come previsto. È possibile che le prestazioni siano accettabili se l'obiettivo è quello di ottenere lo spazio dei nomi per un volume che contiene solo i dati di archivio a cui si accede raramente e si sta riservando il resto dello spazio di archiviazione per un altro scenario.

## <a name="troubleshoot"></a>Risolvere problemi

Il problema più comune è che il comando Robocopy ha esito negativo e il **volume è pieno** sul lato server di Windows. La suddivisione in livelli nel cloud funziona una volta ogni ora per l'evacuazione del contenuto dal disco locale di Windows Server sincronizzato. Il suo obiettivo è raggiungere lo spazio disponibile del 99% nel volume.

Consentire la sincronizzazione dello stato di avanzamento e la suddivisione in livelli nel cloud liberare spazio su disco. È possibile osservare che in Esplora file in Windows Server.

Quando l'istanza di Windows Server dispone di sufficiente capacità disponibile, la riesecuzione del comando risolverà il problema. Non si verificano interruzioni quando si entra in questa situazione ed è possibile procedere con fiducia. L'inconveniente di eseguire nuovamente il comando è l'unica conseguenza.

Per la risoluzione dei problemi Sincronizzazione file di Azure, vedere il collegamento nella sezione seguente.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni sulle condivisioni file di Azure e Sincronizzazione file di Azure. Gli articoli seguenti contengono opzioni avanzate, procedure consigliate e guida alla risoluzione dei problemi. Questi articoli sono collegati alla [documentazione di condivisione file di Azure](storage-files-introduction.md) nel modo appropriato.

* [Panoramica di Sincronizzazione file di Azure](./storage-sync-files-planning.md)
* [Guida alla distribuzione di Sincronizzazione file di Azure](./storage-how-to-create-file-share.md)
* [Risoluzione dei problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)