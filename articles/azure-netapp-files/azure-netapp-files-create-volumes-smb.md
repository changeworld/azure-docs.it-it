---
title: Creare un volume SMB per Azure NetApp Files | Microsoft Docs
description: Questo articolo illustra come creare un volume SMB3 in Azure NetApp Files. Informazioni sui requisiti per le connessioni Active Directory e i servizi del dominio.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: c673f7a9556193fb05e05ea372bfccd17cd3c5ed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868512"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Creare un volume SMB per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi tramite NFS (NFSv3 e NFSv 4.1), SMB3 o il protocollo Dual (NFSv3 e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. Questo articolo illustra come creare un volume SMB3.

## <a name="before-you-begin"></a>Prima di iniziare 

* È necessario avere già configurato un pool di capacità. Vedere [configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).     
* È necessario delegare una subnet ad Azure NetApp Files. Vedere [delegare una subnet a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurare connessioni di Active Directory 

Prima di creare un volume SMB, è necessario creare una connessione Active Directory. Se non sono state configurate Active Directory connessioni per i file di Azure NetApp, seguire le istruzioni descritte in [creare e gestire connessioni Active Directory](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Scegliere il pannello **Volumi** nel pannello Pool di capacità. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume fare clic su **Crea** e fornire informazioni per i campi seguenti nella scheda nozioni di base:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile usare qualsiasi carattere alfanumerico.   

        Non è possibile usare `default` o `bin` come nome del volume.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si vuole creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Velocità effettiva (MiB/S)**   
        Se il volume viene creato in un pool di capacità QoS manuale, specificare la velocità effettiva desiderata per il volume.   

        Se il volume viene creato in un pool di capacità QoS automatica, il valore visualizzato in questo campo è (quota x velocità effettiva del livello di servizio).   

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere al volume dalla rete locale tramite ExpressRoute.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina Crea un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale è possibile delegare una sola subnet ad Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se si desidera applicare un criterio snapshot esistente al volume, fare clic su **Mostra sezione avanzata** per espanderlo, specificare se si desidera nascondere il percorso dello snapshot e selezionare un criterio di snapshot nel menu a discesa. 

        Per informazioni sulla creazione di un criterio snapshot, vedere [Manage snapshot Policies](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostra selezione avanzata](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Fare clic su **Protocollo** e completare le informazioni seguenti:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare la connessione **Active Directory** nell'elenco a discesa.
    * Specificare il nome del volume condiviso in **Nome condivisione**.
    * Se si vuole abilitare la disponibilità continua per il volume SMB, selezionare **Abilita disponibilità continua**.    

        > [!IMPORTANT]   
        > La funzionalità disponibilità continua SMB è attualmente disponibile in anteprima pubblica. È necessario inviare una richiesta di attesa per l'accesso alla funzionalità tramite la **[pagina di invio di Azure NetApp files SMB per le condivisioni di disponibilità continua di anteprima pubblica](https://aka.ms/anfsmbcasharespreviewsignup)**. Attendere un messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files prima di usare la funzionalità di disponibilità continua.   
        > 
        > È necessario abilitare la disponibilità continua solo per i carichi di lavoro SQL. L'uso di condivisioni di disponibilità continue SMB per carichi di lavoro diversi da SQL Server *non* è supportato. Questa funzionalità è attualmente supportata in Windows SQL Server. Linux SQL Server attualmente non è supportato. Se si usa un account non amministratore (dominio) per installare SQL Server, assicurarsi che all'account sia assegnato il privilegio di sicurezza richiesto. Se l'account di dominio non dispone dei privilegi di sicurezza richiesti ( `SeSecurityPrivilege` ) e il privilegio non può essere impostato a livello di dominio, è possibile concedere il privilegio all'account utilizzando il campo **privilegi di sicurezza utenti** di Active Directory connessioni. Vedere [creare una connessione Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Screenshot che descrive la scheda protocollo per la creazione di un volume SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume.  Quindi fare clic su **Crea** per creare il volume SMB.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="control-access-to-an-smb-volume"></a>Controllare l'accesso a un volume SMB  

L'accesso a un volume SMB viene gestito tramite le autorizzazioni.  

### <a name="share-permissions"></a>Autorizzazioni di condivisione  

Per impostazione predefinita, un nuovo volume dispone delle autorizzazioni di condivisione **Tutti/Controllo completo**. I membri del gruppo Domain Admins possono modificare le autorizzazioni di condivisione mediante Gestione computer nell'account computer usato per il volume Azure NetApp Files.

![Percorso di montaggio SMB](../media/azure-netapp-files/smb-mount-path.png) 
![Impostare le autorizzazioni di condivisione](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Autorizzazioni per file e cartelle NTFS  

È possibile impostare le autorizzazioni per un file o una cartella usando la scheda **Sicurezza** delle proprietà dell'oggetto nel client SMB di Windows.
 
![Impostare le autorizzazioni per file e cartelle](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Domande frequenti su SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Risolvere i problemi relativi ai volumi SMB o Dual Protocol](troubleshoot-dual-protocol-volumes.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
