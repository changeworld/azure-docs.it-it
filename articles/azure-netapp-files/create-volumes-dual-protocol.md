---
title: Creare un volume dual-protocol (NFSv3 e SMB) per Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare un volume che usa il protocollo duale di NFSv3 e SMB con supporto per il mapping utenti LDAP.
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
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725684"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Creare un volume dual-protocol (NFSv3 e SMB) per Azure NetApp Files

Azure NetApp Files supporta la creazione di volumi usando NFS (NFSv3 e NFSv4.1), SMB3 o doppio protocollo. Questo articolo illustra come creare un volume che usa il protocollo duale di NFSv3 e SMB con supporto per il mapping utenti LDAP. 

Per creare volumi NFS, vedere [Creare un volume NFS.](azure-netapp-files-create-volumes.md) Per creare volumi SMB, vedere [Creare un volume SMB.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Prima di iniziare 

* È necessario avere già creato un pool di capacità.  
    Vedere [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).   
* È necessario delegare una subnet ad Azure NetApp Files.  
    Vedere [Delegare una subnet Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerazioni

* Assicurarsi di soddisfare i requisiti [per le connessioni di Active Directory.](create-active-directory-connections.md#requirements-for-active-directory-connections) 
* Creare una zona di ricerca inversa nel server DNS e quindi aggiungere un record puntatore (PTR) del computer host di Active Directory in tale zona di ricerca inversa. In caso contrario, la creazione del volume dual-protocol avrà esito negativo.
* Verificare che il client NFS sia aggiornato ed esegua gli aggiornamenti più recenti per il sistema operativo.
* Assicurarsi che il server LDAP di Active Directory (AD) sia attivo e in esecuzione in Active Directory. A tale scopo, installare e configurare il ruolo Active Directory Lightweight Directory Services [(AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) nel computer ad Active Directory.
* I volumi a doppio protocollo non supportano attualmente Azure Active Directory Domain Services (AADDS). LDAP su TLS non deve essere abilitato se si usa AADDS.
* La versione NFS usata da un volume a doppio protocollo è NFSv3. Di conseguenza, si applicano le considerazioni seguenti:
    * Il doppio protocollo non supporta gli attributi estesi ACLS di Windows `set/get` dai client NFS.
    * I client NFS non possono modificare le autorizzazioni per lo stile di sicurezza NTFS e i client Windows non possono modificare le autorizzazioni per i volumi dual protocol in stile UNIX.   

    La tabella seguente descrive gli stili di sicurezza e i relativi effetti:  
    
    | Stile di sicurezza    | Client che possono modificare le autorizzazioni   | Autorizzazioni che i client possono usare  | Stile di sicurezza efficace risultante    | Client che possono accedere ai file     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | Bit della modalità NFSv3   | UNIX  | NFS e Windows   |
    | `Ntfs`    | Windows   | ACL NTFS     | NTFS  |NFS e Windows|
* Gli utenti UNIX che montano il volume dello stile di sicurezza NTFS tramite NFS verranno autenticati come utente di Windows per `root` UNIX `root` e per tutti gli `pcuser` altri utenti. Assicurarsi che questi account utente esistano in Active Directory prima di montare il volume quando si usa NFS. 
* Se si dispone di topologie di grandi dimensioni e si usa lo stile di sicurezza con un volume a doppio protocollo o LDAP con gruppi estesi, Azure NetApp Files potrebbe non essere in grado di accedere a tutti i server nelle `Unix` topologie.  Se si verifica questa situazione, contattare il team dell'account per assistenza.  <!-- NFSAAS-15123 --> 
* Non è necessario un certificato CA radice del server per la creazione di un volume con doppio protocollo. È necessario solo se LDAP su TLS è abilitato.


## <a name="create-a-dual-protocol-volume"></a>Creare un volume con doppio protocollo

1.  Scegliere il pannello **Volumi** nel pannello Pool di capacità. Fare clic su **+ Aggiungi volume** per creare un volume. 

    ![Passare a Volumi](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Nella finestra Crea un volume fare clic **su Crea** e specificare le informazioni per i campi seguenti nella scheda Informazioni di base:   
    * **Nome del volume**      
        Specificare il nome per il volume che si sta creando.   

        Un nome di volume deve essere univoco all'interno di ogni pool di capacità. Deve essere composto da almeno tre caratteri. È possibile usare qualsiasi carattere alfanumerico.   

        Non è possibile `default` usare o come nome del `bin` volume.

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
        
        Se non è stata delegata una subnet, fare clic su **Crea nuovo** nella pagina di creazione di un volume. Nella pagina di creazione della subnet, specificare le informazioni relative alla stessa e selezionare **Microsoft.NetApp/volumi** per delegarla ad Azure NetApp Files. In ogni rete virtuale è possibile delegare solo una subnet Azure NetApp Files.   
 
        ![Crea un volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Creare una subnet](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se si desidera applicare un criterio di  snapshot esistente al volume, fare clic su Mostra sezione avanzata per espanderlo, specificare se si vuole nascondere il percorso dello snapshot e selezionare un criterio di snapshot nel menu a discesa. 

        Per informazioni sulla creazione di criteri snapshot, vedere [Gestire i criteri snapshot.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Mostra selezione avanzata](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Fare clic su **Protocollo** e quindi completare le azioni seguenti:  
    * Selezionare **dual-protocol (NFSv3 e SMB)** come tipo di protocollo per il volume.   

    * Specificare il **percorso del volume.**   
    Questo percorso del volume è il nome del volume condiviso. Il nome deve iniziare con un carattere alfabetico e deve essere univoco all'interno di ogni sottoscrizione e di ogni area.  

    * Specificare lo **stile di sicurezza** da usare: NTFS (impostazione predefinita) o UNIX.

    * Se si vuole abilitare la crittografia del protocollo SMB3 per il volume dual-protocol, selezionare Abilita crittografia del **protocollo SMB3**.   

        Questa funzionalità abilita la crittografia solo per i dati SMB3 in-flight. Non crittografa i dati in-flight di NFSv3. I client SMB che non usano la crittografia SMB3 non saranno in grado di accedere a questo volume. I dati in stato di inquieto vengono crittografati indipendentemente da questa impostazione. Per altre informazioni, vedere Domande frequenti sulla crittografia [SMB.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

        La funzionalità di crittografia del protocollo **SMB3** è attualmente in anteprima. Se questa è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

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

    * Facoltativamente, configurare [i criteri di esportazione per il volume](azure-netapp-files-configure-export-policy.md).

    ![Specificare il protocollo doppio](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Fare clic su **Rivedi e crea** per esaminare i dettagli del volume. Fare quindi **clic su** Crea per creare il volume.

    Il volume creato viene visualizzato nella pagina Volumi. 
 
    Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Consentire agli utenti NFS locali con LDAP di accedere a un volume con doppio protocollo 

È possibile abilitare gli utenti client NFS locali non presenti nel server LDAP di Windows per accedere a un volume a doppio protocollo con LDAP con gruppi estesi abilitati. A tale scopo, abilitare **l'opzione Consenti utenti NFS locali** con LDAP come indicato di seguito:

1. Fare clic **su Connessioni di Active Directory**.  In una connessione Active Directory esistente fare clic sul menu di scelta rapida (i tre `…` puntini ) e selezionare **Modifica.**  

2. Nella finestra **Edit Active Directory settings (Modifica** impostazioni Active Directory) visualizzata selezionare l'opzione Allow local **NFS users with LDAP (Consenti utenti NFS locali con LDAP).**  

    ![Screenshot che mostra l'opzione Consenti utenti NFS locali con LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Gestire gli attributi POSIX LDAP

È possibile gestire gli attributi POSIX, ad esempio UID, Home Directory e altri valori, usando Utenti e computer di Active Directory snap-in MMC.  L'esempio seguente illustra l'Editor attributi di Active Directory:  

![Editor attributi di Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

È necessario impostare gli attributi seguenti per gli utenti LDAP e i gruppi LDAP: 
* Attributi obbligatori per gli utenti LDAP:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Attributi obbligatori per i gruppi LDAP:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Configurare il client NFS 

Seguire le istruzioni in [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md) configurare il client NFS.  

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md)
* [Risolvere i problemi relativi ai volumi SMB o a doppio protocollo](troubleshoot-dual-protocol-volumes.md)
* [Risolvere i problemi relativi ai volumi LDAP](troubleshoot-ldap-volumes.md)
