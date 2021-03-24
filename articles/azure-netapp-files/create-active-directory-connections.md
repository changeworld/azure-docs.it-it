---
title: Creare e gestire connessioni di Active Directory per Azure NetApp Files | Microsoft Docs
description: Questo articolo illustra come creare e gestire connessioni di Active Directory per Azure NetApp Files.
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
ms.openlocfilehash: add907923cc2284939acd972237fd4ec74ee2d12
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864007"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Creare e gestire connessioni di Active Directory per Azure NetApp Files

Per diverse funzionalità di Azure NetApp Files è necessario disporre di una connessione Active Directory.  Ad esempio, è necessario disporre di una connessione Active Directory prima di poter creare un volume [SMB](azure-netapp-files-create-volumes-smb.md) o un [volume a doppio protocollo](create-volumes-dual-protocol.md).  Questo articolo illustra come creare e gestire connessioni di Active Directory per Azure NetApp Files.

## <a name="before-you-begin"></a>Prima di iniziare  

È necessario avere già configurato un pool di capacità.   
[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)   
È necessario delegare una subnet ad Azure NetApp Files.  
[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Requisiti per le connessioni Active Directory

 I requisiti per le connessioni Active Directory sono i seguenti: 

* L'account amministratore usato deve avere la possibilità di creare account computer nel percorso dell'unità organizzativa (OU) che verrà specificato.  

* Le porte appropriate devono essere aperte nel server Windows Active Directory (AD) applicabile.  
    Le porte richieste sono le seguenti: 

    |     Service           |     Porta     |     Protocollo     |
    |-----------------------|--------------|------------------|
    |    Servizi Web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/D       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nome NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* La topologia del sito per Active Directory Domain Services di destinazione deve rispettare le linee guida, in particolare la rete virtuale di Azure in cui viene distribuito Azure NetApp Files.  

    Lo spazio indirizzi per la rete virtuale in cui viene distribuito Azure NetApp Files deve essere aggiunto a un sito Active Directory nuovo o esistente (in cui risiede un controller di dominio raggiungibile da Azure NetApp Files). 

* I server DNS specificati devono essere raggiungibili dalla [subnet delegata](./azure-netapp-files-delegate-subnet.md) di Azure NetApp Files.  

    Per le topologie di rete supportate, vedere [Linee guida per la pianificazione della rete per Azure NetApp Files](./azure-netapp-files-network-topologies.md).

    I gruppi di sicurezza di rete (NSG) e i firewall devono disporre di regole configurate in modo appropriato per consentire le richieste di traffico Active Directory e DNS. 

* La subnet delegata di Azure NetApp Files deve essere in grado di raggiungere tutti i controller di dominio di Active Directory Domain Services (ADDS) nel dominio, inclusi tutti i controller di dominio locali e remoti. In caso contrario, può verificarsi un'interruzione del servizio.  

    Se si hanno controller di dominio non raggiungibili dalla subnet delegata di Azure NetApp Files, è possibile specificare un sito Active Directory durante la creazione della connessione Active Directory.  Azure NetApp Files deve comunicare solo con i controller di dominio nel sito in cui si trova lo spazio indirizzi della subnet delegata di Azure NetApp Files.

    Vedere [Progettazione della topologia del sito](/windows-server/identity/ad-ds/plan/designing-the-site-topology) per siti e servizi di Active Directory. 
    
* È possibile abilitare la crittografia AES per l'autenticazione di Active Directory selezionando la casella **crittografia AES** nella finestra [join Active Directory](#create-an-active-directory-connection) . Azure NetApp Files supporta i tipi di crittografia DES, Kerberos AES 128 e Kerberos AES 256 (dal meno sicuro al più sicuro). Se si Abilita la crittografia AES, le credenziali utente usate per unire Active Directory devono avere l'opzione account corrispondente più alta abilitata che corrisponde alle funzionalità abilitate per il Active Directory.    

    Se, ad esempio, il Active Directory dispone solo della funzionalità AES-128, è necessario abilitare l'opzione relativa all'account AES-128 per le credenziali utente. Se il Active Directory ha la funzionalità AES-256, è necessario abilitare l'opzione relativa all'account AES-256, che supporta anche AES-128. Se il Active Directory non dispone di alcuna funzionalità di crittografia Kerberos, Azure NetApp Files USA DES per impostazione predefinita.  

    È possibile abilitare le opzioni dell'account nelle proprietà del Active Directory Microsoft Management Console (MMC) utenti e computer:   

    ![MMC utenti e computer Active Directory](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files supporta la [firma LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), che consente la trasmissione sicura del traffico LDAP tra il servizio Azure NetApp files e i [controller di dominio Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di destinazione. Se si seguono le linee guida di Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) per la firma LDAP, è necessario abilitare la funzionalità di firma ldap in Azure NetApp files selezionando la casella **firma LDAP** nella finestra [join Active Directory](#create-an-active-directory-connection) . 

    La configurazione dell' [associazione di canale LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) da sola non ha alcun effetto sul servizio Azure NetApp files. Tuttavia, se si usano sia il binding di canale LDAP che l'LDAP sicuro (ad esempio, LDAPs o `start_tls` ), la creazione del volume SMB avrà esito negativo.

## <a name="decide-which-domain-services-to-use"></a>Decidere quali servizi di dominio usare 

Azure NetApp Files supporta sia [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) che Azure Active Directory Domain Services (AADDS) per le connessioni AD.  Prima di creare una connessione AD, è necessario decidere se usare ADDS o AADDS.  

Per altre informazioni, vedere [Confrontare soluzioni Active Directory Domain Services autogestite, Azure Active Directory e Azure Active Directory Domain Services gestite](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

È possibile usare l'ambito preferito di [Siti e servizi di Active Directory](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) per Azure NetApp Files. Questa opzione consente letture e scritture nei controller di dominio di Active Directory Domain Services (ADDS) [accessibili da Azure NetApp Files](azure-netapp-files-network-topologies.md). Impedisce inoltre al servizio di comunicare con i controller di dominio che non si trovano nel sito specificato in Siti e servizi di Active Directory. 

Per trovare il nome del sito quando si usa ADDS, è possibile contattare il gruppo amministrativo dell'organizzazione responsabile di Active Directory Domain Services. Nell'esempio seguente viene illustrato il plug-in Siti e servizi di Active Directory in cui viene visualizzato il nome del sito: 

![Siti e servizi di Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Quando si configura una connessione AD per Azure NetApp Files, è possibile specificare il nome del sito nell'ambito del campo **Nome sito di AD**.

### <a name="azure-active-directory-domain-services"></a>Servizi di dominio Azure Active Directory 

Per la configurazione di Azure Active Directory Domain Services (AADDS) e altre informazioni, vedere [Documentazione di Azure AD Domain Services](../active-directory-domain-services/index.yml).

Per Azure NetApp Files sono valide altre considerazioni relative a AADDS: 

* Assicurarsi che la rete virtuale o la subnet in cui viene distribuito AADDS si trovi nella stessa area di Azure della distribuzione di Azure NetApp Files.
* Se si usa un'altra rete virtuale nell'area in cui viene distribuito Azure NetApp Files, è necessario creare un peering tra le due reti virtuali.
* Azure NetApp Files supporta i tipi `user` e `resource forest`.
* Per tipo di sincronizzazione è possibile selezionare `All` o `Scoped`.   
    Se si seleziona `Scoped`, assicurarsi che sia selezionato il gruppo di Azure AD corretto per l'accesso alle condivisioni SMB.  Se non si è sicuri, è possibile usare il tipo di sincronizzazione `All`.
* È necessario usare lo SKU Enterprise o Premium. Lo SKU Standard non è supportato.

Quando si crea una connessione Active Directory, tenere presenti le specifiche seguenti per AADDS:

* È possibile trovare informazioni per **DNS primario**, **DNS secondario** e **Nome di dominio DNS di Active Directory** nel menu AADDS.  
Per i server DNS, verranno usati due indirizzi IP per la configurazione della connessione Active Directory. 
* Il **percorso unità organizzativa** è `OU=AADDC Computers`.  
Questa impostazione viene configurata in **Connessioni Active Directory** in **Account NetApp**:

  ![Percorso unità organizzativa](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Le credenziali del **nome utente** possono essere qualsiasi utente membro del gruppo di **amministratori dei controller di dominio di Azure AD**.


## <a name="create-an-active-directory-connection"></a>Creare una connessione Active Directory

1. Dall'account NetApp fare clic su **Connessioni Active Directory**, quindi fare clic su **Aggiungi**.  

    ![Connessioni Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Nella finestra Aggiungi Active Directory specificare le informazioni seguenti in base ai servizi di dominio che si vuole usare:  

    Per informazioni specifiche dei servizi di dominio usati, vedere [Decidere quali servizi di dominio usare](#decide-which-domain-services-to-use). 

    * **DNS primario**  
        Si tratta del DNS necessario per le operazioni di aggiunta al dominio Active Directory e di autenticazione SMB. 
    * **DNS secondario**   
        Si tratta del server DNS secondario per garantir la ridondanza dei servizi dei nomi. 
    * **Nome di dominio DNS di Active Directory**  
        Si tratta del nome di dominio di Active Directory Domain Services che si vuole aggiungere.
    * **Nome sito di AD**  
        Questo è il nome del sito a cui sarà limitata l'individuazione del controller di dominio. Deve corrispondere al nome del sito in Active Directory siti e servizi.
    * **Prefisso del server SMB (account computer)**  
        Questo è il prefisso di denominazione per l'account computer in Active Directory che verrà usato da Azure NetApp Files per la creazione di nuovi account.

        Se, ad esempio, lo standard di denominazione usato dall'organizzazione per i file server è NAS-01, NAS-02..., NAS-045, immettere "NAS" per il prefisso. 

        Il servizio creerà account computer aggiuntivi in Active Directory in base alle esigenze.

        > [!IMPORTANT] 
        > La ridenominazione del prefisso del server SMB dopo la creazione della connessione Active Directory è un'operazione che comporta problemi. Sarà necessario rimontare le condivisioni SMB esistenti dopo aver rinominato il prefisso del server SMB.

    * **Percorso unità organizzativa**  
        Si tratta del percorso LDAP per l'unità organizzativa (OU) in cui verranno creati gli account computer del server SMB, ad esempio, OU=second level, OU=first level. 

        Se si usa Azure NetApp Files con Azure Active Directory Domain Services, il percorso dell'unità organizzativa sarà `OU=AADDC Computers` quando si configura Active Directory per l'account NetApp.

        ![Aggiungi Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Crittografia AES**   
        Selezionare questa casella di controllo se si vuole abilitare la crittografia AES per un volume SMB. Vedere [requisiti per le connessioni Active Directory](#requirements-for-active-directory-connections) per i requisiti. 

        ![Crittografia AES Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        La funzionalità di **crittografia AES** è attualmente disponibile in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Attendere che lo stato sia `Registered` prima di continuare.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

    * **Firma LDAP**   
        Selezionare questa casella di controllo per abilitare la firma LDAP. Questa funzionalità consente ricerche LDAP sicure tra il servizio Azure NetApp Files e i [controller di dominio Active Directory Domain Services](/windows/win32/ad/active-directory-domain-services)specificati dall'utente. Per ulteriori informazioni, vedere [ADV190023 | Indicazioni Microsoft per l'abilitazione dell'associazione di canale LDAP e della firma LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory firma LDAP](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        La funzionalità di **firma LDAP** è attualmente in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Attendere che lo stato sia `Registered` prima di continuare.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

     * **Utenti con privilegi di sicurezza**   <!-- SMB CA share feature -->   
        È possibile concedere privilegi di sicurezza ( `SeSecurityPrivilege` ) agli utenti che richiedono privilegi elevati per accedere ai volumi Azure NetApp files. Agli account utente specificati sarà consentito eseguire determinate azioni su Azure NetApp Files condivisioni SMB che richiedono privilegi di sicurezza non assegnati per impostazione predefinita agli utenti di dominio.   

        Per gli account utente utilizzati per l'installazione di SQL Server in determinati scenari, ad esempio, è necessario concedere privilegi di sicurezza elevati. Se si usa un account non amministratore (dominio) per installare SQL Server e all'account non è assegnato il privilegio di sicurezza, è necessario aggiungere privilegi di sicurezza all'account.  

        > [!IMPORTANT]
        > L'account di dominio utilizzato per l'installazione di SQL Server deve esistere già prima di aggiungerlo al campo **privilegi di sicurezza utenti** . Quando si aggiunge l'account del programma di installazione di SQL Server agli **utenti con privilegi di sicurezza**, il servizio Azure NetApp files può convalidare l'account contattando il controller di dominio. Il comando potrebbe non riuscire se non è in grado di contattare il controller di dominio.  

        Per ulteriori informazioni su `SeSecurityPrivilege` e SQL Server, vedere [errore di installazione SQL Server se l'account di installazione non dispone di determinati diritti utente](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Screenshot che mostra la casella utenti con privilegi di sicurezza della finestra connessioni Active Directory.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Utenti dei criteri di backup**  
        È possibile includere account aggiuntivi che richiedono privilegi elevati per l'account computer creato per l'uso con Azure NetApp Files. Agli account specificati sarà consentito modificare le autorizzazioni NTFS a livello di file o di cartella. È ad esempio possibile specificare un account del servizio senza privilegi usato per la migrazione dei dati a una condivisione file SMB in Azure NetApp Files.  

        ![Utenti del criterio di backup Active Directory](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        La funzionalità **utenti criteri di backup** è attualmente in anteprima. Se è la prima volta che si usa questa funzionalità, registrare la funzionalità prima di usarla: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Attendere che lo stato sia `Registered` prima di continuare.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

    * Credenziali, inclusi **nome utente** e **password**

        ![Credenziali Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Fare clic su **Accedi**.  

    Viene visualizzata la connessione Active Directory creata.

    ![Connessioni di Active Directory create](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)
* [Configurare la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md)
* [Installare una nuova foresta Active Directory usando l'interfaccia della riga di comando di Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
