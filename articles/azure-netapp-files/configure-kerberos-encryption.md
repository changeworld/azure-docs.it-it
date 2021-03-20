---
title: Configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files | Microsoft Docs
description: Viene descritto come configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files e l'effetto sulle prestazioni.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740563"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Configurare la crittografia Kerberos NFS v4.1 per Azure NetApp Files

Azure NetApp Files supporta la crittografia client NFS in modalità Kerberos (krb5, Krb5i e krb5p) con crittografia AES-256. Questo articolo descrive le configurazioni necessarie per l'uso di un volume NFSv 4.1 con la crittografia Kerberos.

## <a name="requirements"></a>Requisiti

I requisiti seguenti si applicano alla crittografia client NFSv 4.1: 

* Connessione Active Directory Domain Services (AD DS) per facilitare la emissione di ticket Kerberos 
* Creazione di record DNS A/PTR per gli indirizzi IP del server client e Azure NetApp Files NFS
* Un client Linux  
    Questo articolo fornisce informazioni aggiuntive per i client RHEL e Ubuntu.  Altri client funzioneranno con passaggi di configurazione analoghi. 
* Accesso al server NTP  
    È possibile utilizzare uno dei controller di dominio di Dominio di Active Directory controller (DC) usati comunemente.

## <a name="create-an-nfs-kerberos-volume"></a>Creare un volume NFS Kerberos

1.  Seguire i passaggi in [creare un volume NFS per Azure NetApp files](azure-netapp-files-create-volumes.md) per creare il volume NFSv 4.1.   

    Nella pagina Crea volume impostare la versione NFS su **NFSv 4.1** e impostare Kerberos su **abilitato**.

    > [!IMPORTANT] 
    > Non è possibile modificare la selezione dell'abilitazione Kerberos dopo la creazione del volume.

    ![Crea volume Kerberos NFSv 4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Selezionare **Esporta criteri** in modo che corrisponda al livello di accesso e all'opzione di sicurezza desiderati (Kerberos 5, Kerberos 5i o Kerberos 5p) per il volume.   

    Per l'effetto sulle prestazioni di Kerberos, vedere l' [effetto sulle prestazioni di Kerberos in NFSv 4.1](#kerberos_performance).  

    È anche possibile modificare i metodi di sicurezza Kerberos per il volume facendo clic su Esporta criterio nel riquadro di spostamento Azure NetApp Files.

3.  Fare clic su **Verifica + crea** per creare il volume NFSv 4.1.

## <a name="configure-the-azure-portal"></a>Verrà configurato il portale di Azure 

1.  Seguire le istruzioni riportate in [creare una Active Directory connessione](create-active-directory-connections.md).  

    Per Kerberos è necessario creare almeno un account del computer in Active Directory. Le informazioni sull'account fornite vengono usate per creare gli account per i volumi Kerberos SMB *e* NFSv 4.1. Questo computer viene creato automaticamente durante la creazione del volume.

2.  In **area di autenticazione Kerberos** immettere il **nome del server ad** e l'indirizzo **IP KDC** .

    Il server AD e l'IP KDC possono essere lo stesso server. Queste informazioni vengono utilizzate per creare l'account del computer SPN utilizzato da Azure NetApp Files. Dopo aver creato l'account del computer, Azure NetApp Files utilizzerà i record del server DNS per individuare i server KDC aggiuntivi in base alle esigenze. 

    ![Area di autenticazione Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Fare clic su **Aggiungi** per salvare la configurazione.

## <a name="configure-active-directory-connection"></a>Configurare la connessione Active Directory 

La configurazione di NFSv 4.1 Kerberos crea due account computer in Active Directory:
* Un account computer per le condivisioni SMB
* Un account computer per NFSv 4.1: è possibile identificare questo account tramite il prefisso `NFS-` . 

Dopo aver creato il primo volume Kerberos NFSv 4.1, impostare il tipo di crittografia per l'account computer usando il comando PowerShell seguente:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Configurare il client NFS 

Per configurare il client NFS, seguire le istruzioni riportate in [configurare un client NFS per Azure NetApp files](configure-nfs-clients.md) .  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Montare il volume Kerberos NFS

1. Nella pagina **volumi** selezionare il volume NFS che si desidera montare.

2. Selezionare **istruzioni di montaggio** dal volume per visualizzare le istruzioni.

    Ad esempio: 

    ![Istruzioni di montaggio per i volumi Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Creare la directory (punto di montaggio) per il nuovo volume.  

4. Impostare il tipo di crittografia predefinito su AES 256 per l'account computer:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * È necessario eseguire questo comando solo una volta per ogni account computer.
    * È possibile eseguire questo comando da un controller di dominio o da un PC in cui è installato strumenti di [amministrazione remota](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) del server. 
    * La `$NFSCOMPUTERACCOUNT` variabile è l'account computer creato in Active Directory quando si distribuisce il volume Kerberos. Si tratta dell'account con prefisso `NFS-` . 
    * La `$ANFSERVICEACCOUNT` variabile è un account utente Active Directory senza privilegi con controlli delegati sull'unità organizzativa in cui è stato creato l'account computer. 

5. Montare il volume nell'host: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * La `$ANFEXPORT` variabile è il `host:/export` percorso trovato nelle istruzioni di montaggio.
    * La `$ANFMOUNTPOINT` variabile è la cartella creata dall'utente nell'host Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Conseguenze sulle prestazioni di Kerberos in NFSv 4.1 

È necessario comprendere le opzioni di sicurezza disponibili per i volumi NFSv 4.1, i vettori di prestazioni testati e l'effetto sulle prestazioni previsto di Kerberos. Per informazioni dettagliate, vedere l' [effetto sulle prestazioni di Kerberos nei volumi NFSv 4.1](performance-impact-kerberos.md) .  

## <a name="next-steps"></a>Passaggi successivi  

* [Conseguenze sulle prestazioni di Kerberos nei volumi NFSv 4.1](performance-impact-kerberos.md)
* [Risolvere i problemi del volume Kerberos di NFSv 4.1](troubleshoot-nfsv41-kerberos-volumes.md)
* [Domande frequenti sulla Azure NetApp Files](azure-netapp-files-faqs.md)
* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare una connessione Active Directory](create-active-directory-connections.md)
* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md) 
