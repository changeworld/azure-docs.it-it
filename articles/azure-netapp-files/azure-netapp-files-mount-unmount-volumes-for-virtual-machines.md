---
title: Montare volumi Azure NetApp Files per le macchine virtuali
description: Informazioni su come montare o smontare un volume per macchine virtuali Windows o macchine virtuali Linux in Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842258"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montare o smontare un volume per macchine virtuali Windows o Linux 

È possibile montare o smontare un volume per macchine virtuali Windows o Linux a seconda delle esigenze.  Le istruzioni di montaggio per le macchine virtuali Linux sono disponibili in Azure NetApp Files.  

## <a name="requirements"></a>Requisiti 

* È necessario disporre di almeno un criterio di esportazione per poter accedere a un volume NFS.
* Per montare correttamente un volume NFS, verificare che le seguenti porte NFS siano aperte tra il client e i volumi NFS:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (solo NFSv3)
    * 4046 TCP/UDP = `status` (solo NFSv3)

## <a name="steps"></a>Passaggi

1. Fare clic sul pannello **volumi** e quindi selezionare il volume per il quale si desidera montare. 
2. Fare clic su **istruzioni di montaggio** dal volume selezionato, quindi seguire le istruzioni per montare il volume. 

    ![Istruzioni di montaggio NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Istruzioni di montaggio SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Se si sta montando un volume NFS, assicurarsi di usare l' `vers` opzione nel `mount` comando per specificare la versione del protocollo NFS corrispondente al volume che si desidera montare. 
    * Se si usa NFSv 4.1, usare il comando seguente per montare i file system:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Se si usa NFSv 4.1, assicurarsi che tutte le macchine virtuali che montano l'esportazione usino nomi host univoci.

3. Se si vuole che un volume NFS venga montato automaticamente quando una macchina virtuale di Azure viene avviata o riavviata, aggiungere una voce al `/etc/fstab` file nell'host. 

    Ad esempio: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` è l'indirizzo IP del volume Azure NetApp Files trovato nel pannello delle proprietà del volume.
    * `$FILEPATH` percorso di esportazione del volume Azure NetApp Files.
    * `$MOUNTPOINT` è la directory creata nell'host Linux usato per montare l'esportazione NFS.

4. Se si desidera montare il volume in Windows utilizzando NFS:

    a. Montare prima il volume in una VM UNIX o Linux.  
    b. Eseguire un `chmod 777` `chmod 775` comando o sul volume.  
    c. Montare il volume tramite il client NFS in Windows.
    
5. Per montare un volume Kerberos NFS, vedere [configurare la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md) per altri dettagli. 

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il dominio predefinito di NFS v4.1 per Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Domande frequenti su NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Panoramica di Network File System](/windows-server/storage/nfs/nfs-overview)
* [Montare un volume Kerberos NFS](configure-kerberos-encryption.md#kerberos_mount)
