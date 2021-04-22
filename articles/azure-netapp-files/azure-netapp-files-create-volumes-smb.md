---
title: Creare un volume SMB per Azure NetApp Files | Microsoft Docs
description: Questo articolo illustra come creare un volume SMB3 in Azure NetApp Files. Informazioni sui requisiti per le connessioni Active Directory e Servizi di dominio.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: d3ca94524c334a20f5ee75e5300ad419fa1542c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873272"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Creare un volume SMB per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi usando NFS (NFSv3 e NFSv4.1), SMB3 o dual protocol (NFSv3 e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. 

Questo articolo illustra come creare un volume SMB3. Per i volumi NFS, vedere [Creare un volume NFS.](azure-netapp-files-create-volumes.md) Per i volumi a doppio protocollo, vedere [Creare un volume dual-protocol](create-volumes-dual-protocol.md).

## <a name="before-you-begin"></a>Prima di iniziare 

* È necessario avere già configurato un pool di capacità. Vedere [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).     
* È necessario delegare una subnet ad Azure NetApp Files. Vedere [Delegare una subnet Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Configurare le connessioni di Active Directory 

Prima di creare un volume SMB, è necessario creare una connessione Active Directory. Se non sono state configurate connessioni Active Directory per i file di Azure NetApp, seguire le istruzioni descritte in Creare e [gestire connessioni Active Directory.](create-active-directory-connections.md)

## <a name="add-an-smb-volume"></a>Aggiungere un volume SMB

1. Scegliere il pannello **Volumi** nel pannello Pool di capacità. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Fare clic su **+ Aggiungi volume** per creare un volume.  
    Viene visualizzata la finestra Crea un volume.

3. Nella finestra Crea un volume fare clic **su Crea** e specificare le informazioni per i campi seguenti nella scheda Informazioni di base:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile usare qualsiasi carattere alfanumerico.   

        Non è possibile usare `default` o come nome del `bin` volume.

    * **Pool di capacità**  
        Specificare il pool di capacità in cui si vuole creare il volume.

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.  

    * **Velocità effettiva (MiB/S)**   
        Se il volume viene creato in un pool di capacità QoS manuale, specificare la velocità effettiva desiderata per il volume.   

        Se il volume viene creato in un pool di capacità QoS automatico, il valore visualizzato in questo campo è (quota x velocità effettiva del livello di servizio).   

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  

        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Il servizio Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È anche possibile accedere al volume dalla rete locale tramite ExpressRoute.   

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata ad Azure NetApp Files. 
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina Crea un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale è possibile delegare una sola subnet ad Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se si desidera applicare un criterio di  snapshot esistente al volume, fare clic su Mostra sezione avanzata per espanderlo, specificare se si vuole nascondere il percorso dello snapshot e selezionare un criterio di snapshot nel menu a discesa. 

        Per informazioni sulla creazione di criteri snapshot, vedere [Gestire i criteri snapshot.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Mostra selezione avanzata](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Fare clic su **Protocollo** e completare le informazioni seguenti:  
    * Selezionare **SMB** come tipo di protocollo per il volume. 
    * Selezionare la connessione **Active Directory** nell'elenco a discesa.
    * Specificare il nome del volume condiviso in **Nome condivisione**.
    * Se si vuole abilitare la crittografia per SMB3, selezionare **Abilita crittografia del protocollo SMB3**.   
        Questa funzionalità abilita la crittografia per i dati SMB3 in-flight. I client SMB che non usano la crittografia SMB3 non saranno in grado di accedere a questo volume.  I dati in stato di inquieto vengono crittografati indipendentemente da questa impostazione.  
        Per altre informazioni, vedere Domande frequenti sulla crittografia [SMB.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

        La **funzionalità di crittografia del protocollo SMB3** è attualmente in anteprima. Se è la prima volta che si usa questa funzionalità, registrarla prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Controllare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > RegistrationState **può** essere nello `Registering` stato per un massimo di 60 minuti prima di cambiare in `Registered` . Attendere che lo stato sia `Registered` prima di continuare.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        È anche possibile usare i comandi [dell'interfaccia della riga](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) di comando di Azure `az feature register` e per `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione.  
    * Se si vuole abilitare la disponibilità continua per il volume SMB, selezionare **Abilita disponibilità continua.**    

        > [!IMPORTANT]   
        > La funzionalità disponibilità continua SMB è attualmente disponibile in anteprima pubblica. È necessario inviare una richiesta di elenco di attesa per accedere alla funzionalità tramite la pagina di invio della lista di attesa Azure NetApp Files di disponibilità continua **[SMB](https://aka.ms/anfsmbcasharespreviewsignup)** in anteprima pubblica . Attendere un messaggio di posta elettronica di conferma ufficiale dal team Azure NetApp Files prima di usare la funzionalità disponibilità continua.   
        > 
        > È consigliabile abilitare la disponibilità continua solo per SQL Server e i contenitori dei profili utente [FSLogix](../virtual-desktop/create-fslogix-profile-container.md). L'uso di condivisioni di disponibilità continua SMB per carichi di lavoro diversi SQL Server contenitori del profilo utente FSLogix *non è* supportato. Questa funzionalità è attualmente supportata in Windows SQL Server. La SQL Server Linux non è attualmente supportata. Se si usa un account non amministratore (dominio) per installare SQL Server, assicurarsi che all'account siano assegnati i privilegi di sicurezza necessari. Se l'account di dominio non dispone del privilegio di sicurezza necessario ( ) e il privilegio non può essere impostato a livello di dominio, è possibile concedere il privilegio all'account usando il campo Utenti con privilegi di sicurezza delle connessioni `SeSecurityPrivilege` Active Directory.  Vedere [Creare una connessione Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).

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

    ![Screenshot che descrive la scheda Protocollo della creazione di un volume SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume.  Quindi fare clic su **Crea** per creare il volume SMB.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="control-access-to-an-smb-volume"></a>Controllare l'accesso a un volume SMB  

L'accesso a un volume SMB viene gestito tramite le autorizzazioni.  

### <a name="share-permissions"></a>Autorizzazioni di condivisione  

Per impostazione predefinita, un nuovo volume dispone delle autorizzazioni di condivisione **Tutti/Controllo completo**. I membri del gruppo Domain Admins possono modificare le autorizzazioni di condivisione come indicato di seguito:  

1. Eseguire il mapping della condivisione a un'unità.  
2. Fare clic con il pulsante destro del mouse **sull'unità, scegliere Proprietà** e quindi passare alla **scheda** Sicurezza.

[![Impostare le autorizzazioni di condivisione](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>Autorizzazioni per file e cartelle NTFS  

È possibile impostare le autorizzazioni per un file o una cartella usando la scheda **Sicurezza** delle proprietà dell'oggetto nel client SMB di Windows.
 
![Impostare le autorizzazioni per file e cartelle](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Passaggi successivi  

* [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Domande frequenti su SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Risolvere i problemi relativi a volumi SMB o a doppio protocollo](troubleshoot-dual-protocol-volumes.md)
* [Informazioni sull'integrazione delle reti virtuali per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
