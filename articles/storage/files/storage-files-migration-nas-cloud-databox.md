---
title: Migrazione di NAS locale a condivisioni file di Azure
description: Informazioni su come eseguire la migrazione di file da un percorso di archiviazione collegato alla rete locale (NAS) a condivisioni file di Azure con Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491679"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Usare DataBox per eseguire la migrazione da archiviazione collegata alla rete (NAS) a condivisioni file di Azure

Questo articolo di migrazione è uno dei numerosi che coinvolgono le parole chiave NAS e Azure DataBox. Controllare se questo articolo è applicabile al proprio scenario:

> [!div class="checklist"]
> * Origine dati: archiviazione collegata alla rete (NAS)
> * Route di migrazione: &rArr; &rArr; condivisione file di Azure databox NAS
> * Memorizzazione nella cache dei file in locale: No, l'obiettivo finale consiste nell'usare le condivisioni file di Azure direttamente nel cloud. Non esiste alcun piano per utilizzare Sincronizzazione file di Azure.

Se lo scenario è diverso, esaminare la [tabella delle guide alla migrazione](storage-files-migration-overview.md#migration-guides).

Questo articolo illustra le configurazioni end-to-end tramite la pianificazione, la distribuzione e le configurazioni di rete necessarie per eseguire la migrazione dal dispositivo NAS a condivisioni file di Azure funzionali. Questa guida USA Azure DataBox per il trasporto bulk dei dati (trasporto dati offline).

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è spostare le condivisioni presenti sul dispositivo NAS in Azure e fare in modo che diventino condivisioni file di Azure native che è possibile usare senza necessità di Windows Server. Questa migrazione deve essere eseguita in modo da garantire l'integrità dei dati e della disponibilità di produzione durante la migrazione. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="migration-overview"></a>Panoramica della migrazione

Il processo di migrazione è costituito da diverse fasi. Dovrai distribuire gli account di archiviazione di Azure e le condivisioni file, configurare la rete, eseguire la migrazione tramite DataBox di Azure, recuperare le modifiche tramite RoboCopy e infine passare gli utenti alle condivisioni file di Azure appena create. Le sezioni seguenti descrivono in dettaglio le fasi del processo di migrazione.

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>Fase 4: effettuare il provisioning di un server Windows temporaneo

Durante l'attesa dell'arrivo di DataBox di Azure, è possibile distribuire uno o più server Windows necessari per l'esecuzione di processi RoboCopy. 

- Il primo utilizzo di questi server consiste nel copiare i file nella DataBox.
- Il secondo uso di questi server sarà quello di recuperare le modifiche apportate nell'appliance NAS mentre DataBox era in trasporto. Questo approccio mantiene almeno il tempo di inattività sul lato di origine.

La velocità con cui i processi RoboCopy funzionano dipende principalmente da questi fattori:

* IOPS nell'archiviazione di origine e di destinazione
* larghezza di banda di rete disponibile tra di essi </br> Per ulteriori informazioni, vedere la sezione relativa alla risoluzione dei problemi: [considerazioni su IOPS e larghezza di banda](#iops-and-bandwidth-considerations)
* possibilità di elaborare rapidamente file e cartelle in uno spazio dei nomi </br> Per ulteriori informazioni, vedere la sezione risoluzione dei problemi: [velocità di elaborazione](#processing-speed)
* numero di modifiche tra le esecuzioni di RoboCopy </br> Per ulteriori informazioni, vedere la sezione risoluzione dei problemi: [evitare operazioni non necessarie](#avoid-unnecessary-work)

È importante tenere presente i dettagli a cui si fa riferimento quando si decide in merito alla RAM e al numero di thread da fornire ai server Windows temporanei.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Fase 5: preparazione all'uso delle condivisioni file di Azure

Per risparmiare tempo, è consigliabile procedere con questa fase durante l'attesa dell'arrivo del DataBox. Con le informazioni in questa fase, sarà possibile decidere in che modo i server e gli utenti in Azure e all'esterno di Azure verranno abilitati per l'uso delle condivisioni file di Azure. Le decisioni più importanti sono:

- **Rete:** Abilitare le reti per il routing del traffico SMB.
- **Autenticazione:** Configurare gli account di archiviazione di Azure per l'autenticazione Kerberos. AdConnect e l'aggiunta di un dominio all'account di archiviazione consentiranno alle app e agli utenti di usare la propria identità di Active Directory per l'autenticazione
- **Autorizzazione:** Gli ACL a livello di condivisione per ogni condivisione file di Azure consentiranno a utenti e gruppi di Active Directory di accedere a una determinata condivisione e all'interno di una condivisione file di Azure. gli ACL NTFS nativi verranno sostituiti. L'autorizzazione basata sugli ACL di file e cartelle funziona come per le condivisioni SMB locali.
- **Continuità aziendale:** L'integrazione delle condivisioni file di Azure in un ambiente esistente spesso comporta la conservazione degli indirizzi di condivisione esistenti. Se non si usa già spazio dei nomi DFS, provare a stabilirlo nell'ambiente in uso. Si sarà in grado di tenere gli indirizzi di condivisione usati dagli utenti e dagli script, senza modifiche. È possibile utilizzare DFS-N come servizio di routing dello spazio dei nomi per SMB, reindirizzando DFS-Namespace destinazioni alle condivisioni file di Azure dopo la migrazione.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Questo video è una guida e una demo su come esporre in modo sicuro le condivisioni file di Azure direttamente agli Information Worker e alle app in cinque semplici passaggi.</br>
        Il video fa riferimento alla documentazione dedicata per alcuni argomenti:

* [Panoramica dell'identità](storage-files-active-directory-overview.md)
* [Come aggiungere un account di archiviazione a un dominio](storage-files-identity-auth-active-directory-enable.md)
* [Panoramica della rete per le condivisioni file di Azure](storage-files-networking-overview.md)
* [Come configurare gli endpoint pubblici e privati](storage-files-networking-endpoints.md)
* [Come configurare una VPN S2S](storage-files-configure-s2s-vpn.md)
* [Come configurare una VPN P2S Windows](storage-files-configure-p2s-vpn-windows.md)
* [Come configurare una VPN P2S Linux](storage-files-configure-p2s-vpn-linux.md)
* [Come configurare l'invio DNS](storage-files-networking-dns.md)
* [Configurare DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Fase 6: copiare i file nella DataBox

Quando arriva il DataBox, è necessario configurare il DataBox in una linea di visione del dispositivo NAS. Seguire la documentazione di installazione per il tipo di DataBox ordinato.

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

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* Per ulteriori informazioni sui singoli flag RoboCopy, vedere la tabella nella [sezione relativa a Robocopy](#robocopy)imminente.
* Per ulteriori informazioni su come ridimensionare in modo appropriato il numero di thread `/MT:n` , ottimizzare la velocità di Robocopy e fare in modo che Robocopy sia un elemento adiacente ottimale nel Data Center, vedere la [sezione relativa alla risoluzione dei problemi di Robocopy](#troubleshoot).


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Fase 7: recupero di RoboCopy dal NAS

Quando DataBox segnala che tutti i file e le cartelle sono stati inseriti nelle condivisioni file di Azure pianificate, è possibile continuare con questa fase.
Un RoboCopy di recupero è necessario solo se i dati del NAS potrebbero essere stati modificati dall'avvio della copia DataBox. In alcuni scenari in cui si usa una condivisione a scopo di archiviazione, è possibile arrestare le modifiche apportate alla condivisione sul NAS fino al completamento della migrazione. Potrebbe inoltre essere possibile soddisfare i requisiti aziendali impostando le condivisioni NAS come di sola lettura durante la migrazione.

Nei casi in cui è necessario che una condivisione sia di lettura/scrittura durante la migrazione ed è in grado di assorbire solo una piccola finestra di tempo di inattività, questo passaggio di recupero di RoboCopy sarà importante per il completamento prima del failover dell'accesso utente direttamente alla condivisione file di Azure.

In questo passaggio si eseguiranno processi di RoboCopy per recuperare le condivisioni cloud con le ultime modifiche apportate al NAS dal momento in cui sono state compilate le condivisioni nella DataBox.
Questo recupero di RoboCopy può terminare rapidamente o richiedere un po' di tempo, a seconda della varianza che si è verificata nelle condivisioni NAS.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

1. Identificare la prima posizione nel dispositivo NAS.
1. Identificare la condivisione file di Azure corrispondente.
1. Montare la condivisione file di Azure come unità di rete locale nel server Windows temporaneo
1. Avviare la copia con RoboCopy come descritto

### <a name="mounting-an-azure-file-share"></a>Montaggio di una condivisione file di Azure

Prima di poter usare RoboCopy, è necessario rendere accessibile la condivisione file di Azure tramite SMB. Il modo più semplice è montare la condivisione come unità di rete locale al server Windows che si prevede di usare per RoboCopy. 

> [!IMPORTANT]
> Prima di poter montare correttamente una condivisione file di Azure in un server Windows locale, è necessario aver completato la fase di preparazione per l'uso delle condivisioni file di Azure.

Quando si è pronti, vedere l' [articolo usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md) e montare la condivisione file di Azure per cui si vuole avviare la procedura di recupero di Robocop per.

### <a name="robocopy"></a>RoboCopy

Il comando RoboCopy seguente consente di copiare solo le differenze (file e cartelle aggiornati) dalla risorsa di archiviazione NAS alla condivisione file di Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Vedere la sezione relativa alla risoluzione dei problemi](#troubleshoot) se Robocopy ha un impatto sull'ambiente di produzione, segnala molti errori o non avanza nel modo più rapido previsto.

### <a name="user-cut-over"></a>Riduzione utente

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni continuano ad accedere ai file del NAS e potenzialmente modificarli. È possibile che RoboCopy abbia elaborato una directory, passi alla successiva, quindi un utente nel percorso di origine (NAS) aggiunge, modifica o Elimina un file che ora non verrà elaborato in questa esecuzione di RoboCopy corrente. Si tratta di un comportamento previsto.

La prima esecuzione consiste nello trasferire la maggior parte dei dati con varianza nella condivisione file di Azure. Questa prima copia può richiedere qualche minuto. Vedere la sezione relativa alla [risoluzione dei problemi](#troubleshoot) per altre informazioni su ciò che può influire sulle velocità di Robocopy.

Una volta completata l'esecuzione iniziale, eseguire di nuovo il comando.

Una seconda volta che si esegue RoboCopy per la stessa condivisione, l'operazione verrà completata più velocemente, perché è necessario solo trasferire le modifiche apportate dall'ultima esecuzione. È possibile eseguire processi ripetuti per la stessa condivisione.

Quando si considera il tempo di inattività accettabile, è necessario rimuovere l'accesso utente alle condivisioni basate su NAS. Questa operazione può essere eseguita da qualsiasi passaggio che impedisce agli utenti di modificare la struttura di file e cartelle e il contenuto. Un esempio consiste nel puntare la DFS-Namespace a una posizione non esistente o modificare gli ACL radice nella condivisione.

Eseguire un ultimo round RoboCopy. Rileverà le modifiche che potrebbero essere state perse.
Il tempo necessario per il passaggio finale dipende dalla velocità dell'analisi RoboCopy. È possibile stimare l'intervallo di tempo (uguale al tempo di inattività) misurando il tempo impiegato per l'esecuzione precedente.

Creare una condivisione nella cartella di Windows Server ed eventualmente modificare la distribuzione di DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB NAS. Se si dispone di un NAS di livello aziendale aggiunto al dominio, i SID utente corrisponderanno automaticamente mentre gli utenti sono presenti nel Active Directory e RoboCopy copia i file e i metadati con la massima fedeltà. Se sono stati usati utenti locali nel NAS, è necessario ricrearli come utenti locali di Windows Server ed eseguire il mapping dei SID esistenti con RoboCopy spostati nel server Windows ai SID del nuovo utente locale di Windows Server.

È stata completata la migrazione di una condivisione o di un gruppo di condivisioni in una radice o un volume comune. (A seconda del mapping dalla fase 1)

È possibile provare a eseguire alcune di queste copie in parallelo. Si consiglia di elaborare l'ambito di una condivisione file di Azure alla volta.

## <a name="troubleshoot"></a>Risolvere problemi

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni sulle condivisioni file di Azure. Gli articoli seguenti consentono di comprendere le opzioni avanzate, le procedure consigliate e contengono anche la guida alla risoluzione dei problemi. Questi articoli sono collegati alla [documentazione di condivisione file di Azure](storage-files-introduction.md) nel modo appropriato.

* [Panoramica sulla migrazione](storage-files-migration-overview.md)
* [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Considerazioni sulla rete per l'accesso diretto](storage-files-networking-overview.md)
* [Backup: snapshot di condivisione file di Azure](storage-snapshots-files.md)
