---
title: 'soluzione Azure VMware: migrazione con Azure Data Box'
description: Come usare Azure Data Box eseguire la migrazione bulk dei dati soluzione Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741752"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrazione dei dati soluzione Azure VMware tramite Azure Data Box

La Microsoft Azure Data Box cloud consente di inviare terabyte di dati ad Azure in modo rapido, economico e affidabile. Il trasferimento sicuro dei dati viene accelerato con la spedizione all'utente di un dispositivo di archiviazione Data Box. Ogni dispositivo di archiviazione ha una capacità di archiviazione massima utilizzabile di 80 TB e viene trasportato nel data center da un vettore regionale. Il dispositivo è imballato in un involucro rigido per proteggere i dati durante il trasporto.

Usando Data Box, è possibile eseguire la migrazione bulk dei dati VMware nel cloud privato. I dati dall'ambiente VMware vSphere locale vengono copiati Data Box tramite il protocollo NFS (Network File System). La migrazione bulk dei dati comporta il salvataggio di una copia tempor volta di macchine virtuali, configurazione e dati associati in Data Box e quindi la spedizione manuale in Azure.

In questo articolo vengono trattate le informazioni seguenti:

* Configurazione di Data Box.
* Copia dei dati dall'ambiente VMware locale al Data Box tramite NFS.
* Preparazione per la restituzione di Data Box.
* Preparazione dei dati BLOB per la copia soluzione Azure VMware.
* Copia dei dati da Azure al cloud privato.

## <a name="scenarios"></a>Scenari

Usare Data Box negli scenari seguenti per la migrazione bulk dei dati:

* Per eseguire la migrazione di una grande quantità di dati da locale a soluzione Azure VMware. Questo metodo stabilisce una baseline e sincronizza le differenze in rete.
* Per eseguire la migrazione di un numero elevato di macchine virtuali disattivate (macchine virtuali a freddo).
* Per eseguire la migrazione dei dati delle macchine virtuali per la configurazione degli ambienti di sviluppo e test.
* Per eseguire la migrazione di un numero elevato di modelli di macchina virtuale, file ISO e dischi di macchine virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

* Controllare i prerequisiti e [ordinare Data Box](../databox/data-box-deploy-ordered.md) le portale di Azure. Durante il processo di ordine, è necessario selezionare un account di archiviazione che abilita l'archiviazione BLOB. Dopo aver ricevuto il dispositivo Data Box, connetterlo alla rete [](../databox/data-box-deploy-set-up.md) locale e configurare il dispositivo con un indirizzo IP raggiungibile dalla rete di gestione di vSphere.

* Creare una rete virtuale e un account di archiviazione nella stessa area in cui viene soluzione Azure VMware provisioning dell'istanza.

* Creare una [connessione di](cloudsimple-azure-network-connection.md) rete virtuale di Azure dal cloud privato alla rete virtuale in cui viene creato l'account di archiviazione seguendo la procedura descritta in Connettere la rete virtuale di Azure a [CloudSimple usando ExpressRoute.](virtual-network-connection.md)

## <a name="set-up-data-box-for-nfs"></a>Configurare la Data Box per NFS

Connettersi all Data Box'interfaccia utente Web locale seguendo la procedura descritta nella sezione "Connettersi al dispositivo" di [Esercitazione:](../databox/data-box-deploy-set-up.md)Cablare e connettersi al Azure Data Box .  Configurare Data Box consentire l'accesso ai client NFS:

1. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS selezionare** Accesso client **NFS.** 

    ![Configurare l'accesso dei client NFS 1](media/nfs-client-access.png)

2. Immettere l'indirizzo IP degli host VMware ESXi e selezionare **Aggiungi**. È possibile configurare l'accesso per tutti gli host nel cluster vSphere ripetendo questo passaggio. Selezionare **OK**.

    ![Configurare l'accesso dei client NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare i file direttamente nella *cartella radice* nell'account di archiviazione.

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure, le entità di primo livello sono condivisioni e le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
| BLOB e file | Percorso e URL |
|---------------- | ------------ |
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Usare i BLOB in blocchi di Azure per copiare i dati VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Montare la condivisione NFS come archivio dati nel cluster vCenter locale e copiare i dati

La condivisione NFS dal Data Box deve essere montata come archivio dati nel cluster vCenter locale o nell'host VMware ESXi per copiare i dati nell'archivio dati NFS:

1. Accedere al server vCenter locale.

2. Fare clic con il **pulsante destro** del mouse **su Datacenter, scegliere** Archiviazione, nuovo archivio **dati** e quindi **avanti.**

   ![Aggiungere un nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare **NFS** in **Tipo**.

   ![Aggiungere un nuovo archivio dati : tipo](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione di NFS e quindi **selezionare Avanti.**

   ![Aggiungere un nuovo archivio dati - Versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome dell'archivio dati, il percorso e il server. È possibile usare l'indirizzo IP del Data Box per il server. Il percorso della cartella sarà nel `/<StorageAccountName_BlockBlob>/<ContainerName>/` formato .

   ![Aggiungere un nuovo archivio dati - Configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si vuole montare l'archivio dati e quindi selezionare **Avanti.**  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungere un nuovo archivio dati - Selezionare gli host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e selezionare **Fine**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Copiare i dati nell'Data Box dati NFS

Le macchine virtuali possono essere migrate o clonate nel nuovo archivio dati.  È possibile eseguire la migrazione di tutte le macchine virtuali inutilizzate di cui si vuole eseguire la migrazione Data Box'archivio dati NFS usando l'opzione **vMotion di** archiviazione. Le macchine virtuali attive possono essere clonate nell'archivio Data Box dati NFS.

* Identificare ed elencare le macchine virtuali che possono essere **spostate.**
* Identificare ed elencare le macchine virtuali che devono **essere clonate.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Spostare una macchina virtuale in un archivio Data Box dati

1. Fare clic con il pulsante destro del mouse sulla macchina virtuale da spostare nell'Data Box datastore e quindi scegliere **Esegui migrazione**.

    ![Eseguire la migrazione della macchina virtuale](media/databox-migration-vm-migrate.png)

2. Selezionare **Modifica solo l'archiviazione** per il tipo di migrazione e quindi selezionare **Avanti.**

    ![Eseguire la migrazione di una macchina virtuale - solo archiviazione](media/databox-migration-vm-migrate-change-storage.png)

3. Selezionare **Databox-Datastore** come destinazione e quindi selezionare **Avanti.**

    ![Eseguire la migrazione di una macchina virtuale: selezionare l'archivio dati](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Esaminare le informazioni e selezionare **Fine.**

5. Ripetere i passaggi da 1 a 4 per altre macchine virtuali.

> [!TIP]
> È possibile selezionare più macchine virtuali nello stesso stato di alimentazione (attivate o disattivate) ed eseguirne la migrazione in blocco.

Verrà eseguita la migrazione della macchina virtuale all'archivio dati NFS da Data Box. Dopo aver eseguito la migrazione di tutte le macchine virtuali, è possibile disattivare (arrestare) le macchine virtuali attive in preparazione alla migrazione dei dati soluzione Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Clonare una macchina virtuale o un modello di macchina virtuale nell'archivio Data Box dati

1. Fare clic con il pulsante destro del mouse su una macchina virtuale o su un modello di macchina virtuale da clonare. Selezionare **Clone**  >  **Clone to Virtual Machine (Clona nella macchina virtuale).**

    ![Clonazione macchina virtuale](media/databox-migration-vm-clone.png)

2. Selezionare un nome per la macchina virtuale clonata o il modello di macchina virtuale.

3. Selezionare la cartella in cui si vuole inserire l'oggetto clonato e quindi selezionare **Avanti.**

4. Selezionare il cluster o il pool di risorse in cui si vuole inserire l'oggetto clonato e quindi selezionare **Avanti.**

5. Selezionare **Databox-Datastore come** posizione di archiviazione e quindi selezionare **Avanti.**

    ![Clonazione macchina virtuale: selezionare l'archivio dati](media/databox-migration-vm-clone-select-datastore.png)

6. Se si desidera personalizzare le opzioni per l'oggetto clonato, selezionare le opzioni di personalizzazione e quindi selezionare **Avanti.**

7. Esaminare le configurazioni e selezionare **Fine**.

8. Ripetere i passaggi da 1 a 7 per altre macchine virtuali o modelli di macchina virtuale.

Le macchine virtuali verranno clonate e archiviate nell'archivio dati NFS da Data Box. Dopo aver clonato le macchine virtuali, assicurarsi che siano state arrestate in preparazione alla migrazione dei dati soluzione Azure VMware.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Copiare i file ISO nell'archivio Data Box dati

1. Dall'interfaccia utente Web vCenter locale passare a **Archiviazione**.  Selezionare **Databox-Datastore** e quindi **File**. Creare una nuova cartella per l'archiviazione di file ISO.

    ![CopiaRE ISO - Creare una nuova cartella](media/databox-migration-create-folder.png)

2. Specificare un nome per la cartella in cui verranno archiviati i file ISO.

3. Fare doppio clic sulla cartella appena creata per aprirla.

4. Selezionare **Carica file** e quindi selezionare i file ISO da caricare.
    
    ![Copiare i file ISO - Caricare i file](media/databox-migration-upload-iso.png)

> [!TIP]
> Se nell'archivio dati locale sono già presenti file ISO,  è possibile selezionare i file e Copia in per copiare i file nell'archivio Data Box dati NFS.


## <a name="prepare-data-box-for-return"></a>Preparare Data Box per la restituzione

Dopo aver copiato tutti i dati della macchina virtuale, i dati del modello di macchina virtuale e tutti i file ISO nell'archivio dati NFS di Data Box, è possibile disconnettere l'archivio dati da vCenter. Tutte le macchine virtuali e i modelli di macchina virtuale devono essere rimossi dall'inventario prima di disconnettere l'archivio dati.

### <a name="remove-objects-from-inventory"></a>Rimuovere oggetti dall'inventario

1. Dall'interfaccia utente Web vCenter locale passare a **Archiviazione**. Selezionare **Databox-Datastore** e quindi **selezionare VM**.

    ![Rimuovere le macchine virtuali dall'inventario - disattivata](media/databox-migration-select-databox-vm.png)

2. Assicurarsi che tutte le macchine virtuali siano arrestate.

3. Selezionare tutte le macchine virtuali, fare clic con il pulsante destro del mouse e **quindi scegliere Rimuovi dall'inventario.**

    ![Rimuovere macchine virtuali dall'inventario](media/databox-migration-remove-vm-from-inventory.png)

4. Selezionare **Modelli di macchina virtuale in Cartelle** e quindi ripetere il passaggio 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Rimuovere l'Data Box'archivio dati NFS da vCenter

L Data Box'archivio dati NFS deve essere disconnesso dagli host VMware ESXi prima di prepararsi per la restituzione.

1. Dall'interfaccia utente Web vCenter locale passare a **Archiviazione**.

2. Fare clic con il **pulsante destro del mouse su Databox-Datastore** e selezionare Smonta archivio **dati.**

    ![Smontare Data Box archivio dati](media/databox-migration-unmount-datastore.png)

3. Selezionare tutti gli host ESXi in cui è montato l'archivio dati e selezionare **OK.**

    ![Smontare Data Box'archivio dati: selezionare gli host](media/databox-migration-unmount-datastore-select-hosts.png)

4. Esaminare e accettare eventuali avvisi e selezionare **OK.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Preparare Data Box restituire e quindi restituirlo

Seguire i passaggi descritti nell'articolo Restituire Azure Data Box e verificare il caricamento dei dati [in Azure](../databox/data-box-deploy-picked-up.md) per restituire il Data Box. Controllare lo stato della copia dei dati nell'account di archiviazione di Azure. Quando lo stato viene visualizzato come completato, è possibile verificare i dati nell'account di archiviazione di Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Copiare dati da Archiviazione di Azure a soluzione Azure VMware

I dati copiati nel Data Box saranno disponibili nell'account di archiviazione di Azure dopo che lo stato dell'ordine Data Box visualizzato come completato. I dati possono ora essere copiati nel soluzione Azure VMware. I dati nell'account di archiviazione devono essere copiati nell'archivio dati vSAN del cloud privato usando il protocollo NFS. 

Prima di tutto, copiare i dati di archiviazione BLOB in un disco gestito in una macchina virtuale Linux in Azure usando **AzCopy**. Rendere disponibile il disco gestito tramite NFS, montare la condivisione NFS come archivio dati nel cloud privato e quindi copiare i dati. Questo metodo consente una copia più veloce dei dati nel cloud privato.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Copiare i dati nel cloud privato usando una macchina virtuale Linux e dischi gestiti e quindi esportare come condivisione NFS

1. Creare una [macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md) in Azure nella stessa area in cui viene creato l'account di archiviazione e con una connessione di rete virtuale di Azure al cloud privato.

2. Creare un disco gestito la cui capacità di archiviazione è superiore alla quantità di dati BLOB e [collegarlo alla macchina virtuale Linux.](../virtual-machines/linux/attach-disk-portal.md)  Se la quantità di dati BLOB è maggiore della capacità del disco gestito più grande disponibile, i dati devono essere copiati in più passaggi o usando più dischi gestiti.

3. Connettersi alla macchina virtuale Linux e montare il disco gestito.

4. Installare [AzCopy nella macchina virtuale Linux.](../storage/common/storage-use-azcopy-v10.md)

5. Scaricare i dati dall'archivio BLOB di Azure nel disco gestito usando AzCopy.  Sintassi del comando: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Sostituire con il nome dell'account di archiviazione di Azure e con il `<storage-account-name>` `<container-name>` contenitore che contiene i dati copiati tramite Data Box.

6. Installare il server NFS nella macchina virtuale Linux:

    - In una distribuzione Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - In una distribuzione Enterprise Linux: `sudo yum install nfs-utils` .

7. Modificare l'autorizzazione della cartella nel disco gestito in cui sono stati copiati i dati dall'archivio BLOB di Azure.  Modificare le autorizzazioni per tutte le cartelle da esportare come condivisione NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Assegnare le autorizzazioni per gli indirizzi IP client per accedere alla condivisione NFS modificando il `/etc/exports` file.

    ```bash
    sudo vi /etc/exports
    ```
    
    Immettere le righe seguenti nel file per ogni INDIRIZZO IP host ESXi del cloud privato.  Se si creano condivisioni per più cartelle, aggiungere tutte le cartelle.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Esportare le condivisioni NFS usando il `sudo exportfs -a` comando .

10. Riavviare il server kernel NFS usando il `sudo systemctl restart nfs-kernel-server` comando .


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Montare la condivisione NFS della macchina virtuale Linux come archivio dati in un cluster vCenter del cloud privato e quindi copiare i dati

La condivisione NFS dalla macchina virtuale Linux deve essere montata come archivio dati nel cluster vCenter del cloud privato. Dopo il montaggio, i dati possono essere copiati dall'archivio dati NFS all'archivio dati vSAN del cloud privato.

1. Accedere al server vCenter del cloud privato.

2. Fare clic con il **pulsante destro** del mouse **su Datacenter, scegliere** Archiviazione, nuovo archivio **dati** e quindi **avanti.**

   ![Aggiungere un nuovo archivio dati](media/databox-migration-add-datastore.png)

3. Nel passaggio 1 della procedura guidata Aggiungi archivio dati selezionare il **tipo NFS.**

   ![Aggiungere un nuovo archivio dati - tipo](media/databox-migration-add-datastore-type.png)

4. Nel passaggio 2 della procedura guidata selezionare **NFS 3** come versione NFS e quindi selezionare **Avanti.**

   ![Aggiungere un nuovo archivio dati - Versione NFS](media/databox-migration-add-datastore-nfs-version.png)

5. Nel passaggio 3 della procedura guidata specificare il nome per l'archivio dati, il percorso e il server.  È possibile usare l'indirizzo IP della macchina virtuale Linux per il server.  Il percorso della cartella sarà nel `/<folder>/<subfolder>/` formato .

   ![Aggiungere un nuovo archivio dati - Configurazione NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. Nel passaggio 4 della procedura guidata selezionare gli host ESXi in cui si vuole montare l'archivio dati e quindi selezionare **Avanti.**  In un cluster selezionare tutti gli host per garantire la migrazione delle macchine virtuali.

   ![Aggiungere un nuovo archivio dati - Selezionare gli host](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Nel passaggio 5 della procedura guidata esaminare il riepilogo e quindi selezionare **Fine.**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Aggiungere macchine virtuali e modelli di macchine virtuali da un archivio dati NFS all'inventario

1. Dall'interfaccia utente Web vCenter del cloud privato passare a **Archiviazione**.  Selezionare un archivio dati NFS di una macchina virtuale Linux e quindi selezionare **File**.

    ![Selezionare i file dall'archivio dati NFS](media/databox-migration-datastore-select-files.png)

2. Selezionare una cartella contenente una macchina virtuale o un modello di macchina virtuale.  Nel riquadro dei dettagli selezionare un file vmx per una macchina virtuale o un file vmtx per un modello di macchina virtuale.

3. Selezionare **Registra macchina** virtuale per registrare la macchina virtuale nel vCenter del cloud privato.

    ![Registrare la macchina virtuale](media/databox-migration-datastore-register-vm.png)

4. Selezionare il data center, la cartella e il cluster/pool di risorse in cui si vuole registrare la macchina virtuale.

4. Ripetere i passaggi 3 e 4 per tutte le macchine virtuali e i modelli di macchina virtuale.

5. Passare alla cartella che contiene i file ISO.  Selezionare i file ISO e quindi Copia **in** per copiare i file in una cartella nell'archivio dati vSAN.

Le macchine virtuali e i modelli di macchina virtuale sono ora disponibili nel cloud privato vCenter. Queste macchine virtuali devono essere spostate dall'archivio dati NFS all'archivio dati vSAN prima di attivarle. È possibile usare **l'opzione vMotion di** archiviazione e selezionare l'archivio dati vSAN come destinazione per le macchine virtuali.

I modelli di macchina virtuale devono essere clonati dall'archivio dati NFS della macchina virtuale Linux all'archivio dati vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Pulire la macchina virtuale Linux

Dopo aver copiato tutti i dati nel cloud privato, è possibile rimuovere l'archivio dati NFS dal cloud privato:

1. Assicurarsi che tutte le macchine virtuali e i modelli siano stati spostati e clonati nell'archivio dati vSAN.

2. Rimuovere dall'inventario tutti i modelli di macchina virtuale dall'archivio dati NFS.

3. Smontare l'archivio dati delle macchine virtuali Linux dal vCenter del cloud privato.

4. Eliminare la macchina virtuale e il disco gestito da Azure.

5. Se non si vogliono mantenere i dati trasferiti da Data Box nell'account di archiviazione, eliminare l'account di archiviazione di Azure.  
    


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Data Box](../databox/data-box-overview.md).
* Altre informazioni sulle diverse opzioni per la [migrazione dei carichi di lavoro nel cloud privato.](migrate-workloads.md)
