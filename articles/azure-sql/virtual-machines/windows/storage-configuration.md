---
title: Configurare l'archiviazione per le macchine virtuali SQL Server | Microsoft Docs
description: Questo argomento descrive in che modo Azure configura l'archiviazione per le macchine virtuali SQL Server durante il provisioning (Azure Resource Manager modello di distribuzione). Viene inoltre spiegato come è possibile configurare l'archiviazione per le VM di SQL Server esistenti.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565426"
---
# <a name="configure-storage-for-sql-server-vms"></a>Configurare l'archiviazione per le macchine virtuali SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come configurare l'archiviazione per la SQL Server in macchine virtuali (VM) di Azure.

SQL Server le macchine virtuali distribuite tramite immagini del Marketplace seguono automaticamente le [procedure consigliate](performance-guidelines-best-practices-storage.md) per l'archiviazione predefinite che possono essere modificate durante la distribuzione. Alcune di queste impostazioni di configurazione possono essere modificate dopo la distribuzione. 


## <a name="prerequisites"></a>Prerequisiti

Per usare le impostazioni di configurazione automatica dell'archiviazione, la macchina virtuale deve avere le caratteristiche seguenti:

* Con provisioning con un' [immagine della raccolta SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) o registrata con l' [estensione SQL IaaS]().
* Uso del [modello di distribuzione Azure Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Uso di [unità SSD Premium](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nuove VM

Le sezioni seguenti descrivono come configurare l'archiviazione per le nuove macchine virtuali di SQL Server.

### <a name="azure-portal"></a>Portale di Azure

Quando si esegue il provisioning di una VM di Azure usando un'immagine della raccolta di SQL Server, selezionare **Cambia la configurazione** nella scheda **Impostazioni di SQL Server** per aprire la pagina di configurazione dell'archiviazione ottimizzata per le prestazioni. È possibile lasciare i valori predefiniti o modificare il tipo di configurazione del disco più adatto alle proprie esigenze in base al carico di lavoro. 

![Screenshot che evidenzia la scheda Impostazioni SQL Server e l'opzione modifica configurazione.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selezionare il tipo di carico di lavoro per cui si distribuisce SQL Server in **Ottimizzazione dell'archiviazione**. Con l'opzione di ottimizzazione **Generale**, per impostazione predefinita si otterrà un solo disco dati con max 5000 operazioni di I/O al secondo e si userà la stessa unità per i dati, il log delle transazioni e l'archiviazione TempDB. 

Se si seleziona **Elaborazione transazionale** o **Data warehousing**, viene creato un disco separato per i dati, un disco separato per il log delle transazioni e viene usata l'unità SSD locale per TempDB. Non esistono differenze in termini di archiviazione tra l'**elaborazione transazionale** e il **data warehousing**, ma cambiano la [configurazione di striping e i flag di traccia](#workload-optimization-settings). Se si sceglie l'archiviazione Premium, la memorizzazione nella cache viene impostata su *ReadOnly* per l'unità dati e su *None* per l'unità log in base alle [procedure consigliate per le prestazioni delle VM di SQL Server](performance-guidelines-best-practices.md). 

![Configurazione dell'archiviazione per le VM di SQL Server durante il provisioning](./media/storage-configuration/sql-vm-storage-configuration.png)

La configurazione del disco è completamente personalizzabile, in modo che sia possibile configurare la topologia di archiviazione, il tipo di disco e le operazioni di I/O al secondo necessari per il carico di lavoro della VM di SQL Server. È anche possibile usare UltraSSD (anteprima) come opzione per il **tipo di disco** se la VM di SQL Server si trova in una delle aree supportate (Stati Uniti orientali 2, Asia sudorientale ed Europa settentrionale) e sono stati abilitati [dischi Ultra per la sottoscrizione](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Inoltre, è possibile impostare la memorizzazione nella cache per i dischi. Le macchine virtuali di Azure hanno una tecnologia di memorizzazione nella cache a più livelli denominata [cache di dati BLOB](../../../virtual-machines/premium-storage-performance.md#disk-caching) se usate con [dischi Premium](../../../virtual-machines/disks-types.md#premium-ssd). La cache di dati BLOB usa una combinazione della RAM della macchina virtuale e dell'unità SSD locale per la memorizzazione nella cache. 

La memorizzazione nella cache del disco per SSD Premium può essere *ReadOnly*, *ReadWrite* o *None*. 

- La memorizzazione nella cache *ReadOnly* è molto utile per i file di dati di SQL Server archiviati in Archiviazione Premium. Il tipo *ReadOnly* implica una bassa latenza di lettura e valori elevati per le operazioni di I/O al secondo e la velocità effettiva di lettura, poiché le letture vengono eseguite dalla cache, che si trova all'interno della memoria della VM e dell'unità SSD locale. Queste letture sono molto più veloci delle letture dal disco dati, ovvero dall'archiviazione BLOB di Azure. L'archiviazione Premium non include le operazioni di lettura servite dalla cache nel calcolo dei valori di operazioni di I/O al secondo e velocità effettiva del disco. Si è quindi in grado di ottenere valori totali di operazioni di I/O al secondo e velocità effettiva più elevati. 
- La configurazione della cache *None* deve essere usata per i dischi che ospitano il file di log di SQL Server, poiché tale file viene scritto in sequenza e non sfrutta la memorizzazione nella cache di tipo *ReadOnly*. 
- La memorizzazione nella cache *ReadWrite* non deve essere usata per ospitare i file di SQL Server perché SQL Server non supporta la coerenza dei dati con la cache *ReadWrite*. Le scritture sprecano la capacità della cache BLOB *ReadOnly* e le latenze aumentano leggermente se le scritture passano attraverso i livelli della cache BLOB *ReadOnly*. 


   > [!TIP]
   > Assicurarsi che la configurazione dell'archiviazione corrisponda alle limitazioni imposte dalle dimensioni della macchina virtuale selezionata. Se si scelgono i parametri di archiviazione che superano il limite di prestazioni delle dimensioni della macchina virtuale, verrà generato un avviso: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Ridurre le operazioni di I/O al secondo cambiando il tipo di disco o aumentare il limite massimo per le prestazioni aumentando le dimensioni della macchina virtuale. Il provisioning non verrà interrotto. 


A seconda delle scelte effettuate, Azure esegue le seguenti attività di configurazione dell'archiviazione dopo la creazione della VM:

* Crea e connette unità SSD Premium alla macchina virtuale.
* Configura i dischi dati in modo che siano accessibili per SQL Server.
* Configura i dischi dati in un pool di archiviazione in base ai requisiti specificati per dimensioni e prestazioni (operazioni di I/O al secondo e velocità effettiva).
* Associa il pool di archiviazione a una nuova unità nella macchina virtuale.
* Ottimizza la nuova unità in base al tipo di carico di lavoro specificato (data warehousing, elaborazione transazionale o generale).

Per istruzioni complete su come creare una VM di SQL Server nel portale di Azure, vedere l'[esercitazione sul provisioning](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Se si usano i modelli di Resource Manager seguenti, vengono collegati per impostazione predefinita due dischi dati Premium, senza configurare un pool di archiviazione. È comunque possibile personalizzare questi modelli per modificare il numero di dischi di dati Premium collegati alla macchina virtuale.

* [Creare una VM con il backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Creare una VM con l'applicazione automatica delle patch](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Creare una VM con l'integrazione di AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modello di avvio rapido

È possibile usare il modello di avvio rapido seguente per distribuire una VM di SQL Server usando l'ottimizzazione dell'archiviazione. 

* [Creare una VM con ottimizzazione dell'archiviazione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Creare una VM con UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VM esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le VM di SQL Server esistenti, è possibile modificare alcune impostazioni di archiviazione nel portale di Azure. Aprire la [risorsa della macchina virtuale SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selezionare **Panoramica**. La pagina Panoramica di SQL Server illustra l'utilizzo corrente dell'archiviazione della VM. In questo grafico vengono visualizzate tutte le unità esistenti nella VM. Per ogni unità, lo spazio di archiviazione viene visualizzato in quattro sezioni:

* SQL data
* Log SQL
* Altro (archiviazione non SQL)
* Disponibile

Per modificare le impostazioni di archiviazione, selezionare **Configura** in **Impostazioni**. 

![Screenshot che evidenzia l'opzione Configura e la sezione utilizzo archiviazione.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

È possibile modificare le impostazioni del disco per le unità configurate durante il processo di creazione della VM di SQL Server. Selezionando **Estendi unità** viene visualizzata la pagina di modifica dell'unità, che consente di modificare il tipo di disco, nonché di aggiungere altri dischi. 

![Configurare l'archiviazione per le VM di SQL Server esistenti](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Modifiche automatiche

Questa sezione fornisce un riferimento per le modifiche di configurazione dell'archiviazione eseguite automaticamente da Azure durante SQL Server provisioning o configurazione della macchina virtuale nel portale di Azure.

* Azure configura un pool di archiviazione dallo spazio di archiviazione selezionato dalla VM. La sezione successiva di questo argomento contiene informazioni dettagliate sulla configurazione del pool di archiviazione.
* Per la configurazione automatica dell'archiviazione vengono sempre usati dischi dati P30 [SSD Premium](../../../virtual-machines/disks-types.md). Esiste quindi una corrispondenza 1:1 tra il numero selezionato di terabyte e il numero di dischi dati collegati alla VM.

Per informazioni sui prezzi, vedere la pagina [Prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage) nella scheda **Archiviazione su disco** .

### <a name="creation-of-the-storage-pool"></a>Creazione del pool di archiviazione

Azure usa le impostazioni seguenti per creare il pool di archiviazione nelle VM di SQL.

| Impostazione | valore |
| --- | --- |
| Dimensioni di striping |256 KB (data warehousing); 64 KB (transazionale) |
| Dimensione disco |1 TB ciascuno |
| Cache |Lettura |
| Dimensioni allocazione |Dimensioni delle unità di allocazione NTFS = 64 KB |
| Ripristino | Recupero con registrazione minima (nessuna resilienza) |
| Numero di colonne |Numero di dischi dati fino a 8<sup>1</sup> |


<sup>1</sup> Dopo aver creato il pool di archiviazione non è possibile modificare il numero di colonne nel pool.


### <a name="workload-optimization-settings"></a>Impostazioni di ottimizzazione del carico di lavoro

La tabella seguente descrive le tre opzioni disponibili per il tipo di carico di lavoro e le ottimizzazioni corrispondenti:

| Tipo di carico di lavoro | Descrizione | Ottimizzazioni |
| --- | --- | --- |
| **Generale** |Impostazione predefinita che supporta la maggior parte dei carichi di lavoro |nessuno |
| **Elaborazione transazionale** |Ottimizza l'archiviazione per carichi di lavoro OLTP di database tradizionali |Flag di traccia 1117<br/>Flag di traccia 1118 |
| **Data warehousing** |Ottimizza l'archiviazione per i carichi di lavoro di analisi e creazione di report |Flag di traccia 610<br/>Flag di traccia 1117 |

> [!NOTE]
> È possibile specificare il tipo di carico di lavoro solo quando si effettua il provisioning di una macchina virtuale SQL Server selezionandolo nel passaggio configurazione archiviazione.

## <a name="enable-caching"></a>Abilitazione della memorizzazione nella cache. 

Modificare i criteri di memorizzazione nella cache a livello di disco. Questa operazione può essere eseguita usando il portale di Azure, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk)o l' [interfaccia](/cli/azure/vm/disk)della riga di comando di Azure. 

Per modificare i criteri di memorizzazione nella cache nella portale di Azure, attenersi alla procedura seguente:

1. Arrestare il servizio SQL Server. 
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla macchina virtuale, selezionare **dischi** in **Impostazioni**. 
   
   ![Screenshot che illustra il pannello configurazione del disco della macchina virtuale nella portale di Azure.](./media/storage-configuration/disk-in-portal.png)

1. Scegliere i criteri di memorizzazione nella cache appropriati per il disco dall'elenco a discesa. 

   ![Screenshot che mostra la configurazione dei criteri di memorizzazione nella cache del disco nel portale di Azure.](./media/storage-configuration/azure-disk-config.png)

1. Al termine della modifica, riavviare la macchina virtuale SQL Server e avviare il servizio di SQL Server. 


## <a name="enable-write-accelerator"></a>Abilitare l'acceleratore di scrittura

L'accelerazione di scrittura è una funzionalità del disco disponibile solo per le macchine virtuali (VM) della serie M. Lo scopo dell'accelerazione di scrittura è quello di migliorare la latenza di I/O delle Scritture nell'archiviazione Premium di Azure quando è necessaria una latenza di I/O a singola cifra a causa di carichi di lavoro OLTP di importanza strategica per volumi elevati o ambienti data warehouse. 

Arrestare tutte le attività di SQL Server e arrestare il servizio SQL Server prima di apportare modifiche ai criteri di accelerazione della scrittura. 

Se i dischi sono con striping, abilitare l'accelerazione di scrittura per ogni disco singolarmente e la macchina virtuale di Azure deve essere arrestata prima di apportare qualsiasi modifica. 

Per abilitare l'accelerazione della scrittura usando il portale di Azure, attenersi alla procedura seguente:

1. Arrestare il servizio SQL Server. Se i dischi sono con striping, arrestare la macchina virtuale. 
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alla macchina virtuale, selezionare **dischi** in **Impostazioni**. 
   
   ![Screenshot che illustra il pannello configurazione del disco della macchina virtuale nella portale di Azure.](./media/storage-configuration/disk-in-portal.png)

1. Scegliere l'opzione cache con **acceleratore di scrittura** per il disco dall'elenco a discesa. 

   ![Screenshot che mostra i criteri della cache dell'acceleratore di scrittura.](./media/storage-configuration/write-accelerator.png)

1. Una volta applicata la modifica, avviare la macchina virtuale e il servizio SQL Server. 

## <a name="disk-striping"></a>Striping del disco

Per una maggiore velocità effettiva, è possibile aggiungere altri dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati, analizzare la velocità effettiva e la larghezza di banda necessaria per i file di dati SQL Server, inclusi il log e tempdb. I limiti di velocità effettiva e larghezza di banda variano in base alle dimensioni della VM Per altre informazioni, vedere [dimensioni della macchina virtuale](../../../virtual-machines/sizes.md)


* Per Windows 8 e Windows Server 2012 o versioni successive, usare [Spazi di archiviazione](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) applicando le indicazioni seguenti:

  1. Impostare interfoliazione (dimensioni stripe) su 64 KB (65.536 byte) per evitare l'effetto sulle prestazioni a causa del mancato allineamento delle partizioni. Questo valore deve essere impostato con PowerShell.

  2. Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell (e non l'interfaccia utente di Server Manager) per configurare più di 8 dischi.

Ad esempio, il seguente PowerShell crea un nuovo pool di archiviazione con le dimensioni di interfoliazione a 64 KB e il numero di colonne uguale alla quantità di disco fisico nel pool di archiviazione:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Questa opzione è deprecata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio dischi virtuali all'API di gestione archiviazione di Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Se si usa [Spazi di archiviazione diretta (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) con [Istanze del cluster di failover di SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), è necessario configurare un singolo pool. Anche se si possono creare volumi diversi nel pool singolo, tutti condivideranno le stesse caratteristiche, ad esempio gli stessi criteri di memorizzazione nella cache.
 
  * Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per ulteriori informazioni, vedere [dimensioni per le macchine virtuali](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
