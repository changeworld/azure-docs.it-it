---
title: Configure aggiunge LDAP con gruppi estesi per l'accesso Azure NetApp Files volume NFS | Microsoft Docs
description: Vengono descritte le considerazioni e i passaggi per abilitare LDAP con i gruppi estesi quando si crea un volume NFS utilizzando Azure NetApp Files.
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
ms.date: 04/08/2021
ms.author: b-juche
ms.openlocfilehash: 9edf8c6eca223ece8728f9868ee9fe310c517ca9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259711"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Configure aggiunge LDAP con gruppi estesi per l'accesso al volume NFS

Quando si [Crea un volume NFS](azure-netapp-files-create-volumes.md), è possibile abilitare la funzionalità LDAP con gruppi estesi (opzione **LDAP** ) per il volume. Questa funzionalità consente di Active Directory utenti LDAP e gruppi estesi (fino a 1024 gruppi) per accedere al volume.  

Questo articolo illustra le considerazioni e i passaggi per abilitare LDAP con i gruppi estesi quando si crea un volume NFS.  

## <a name="considerations"></a>Considerazioni

* LDAP su TLS *non* deve essere abilitato se si usa Azure Active Directory Domain Services (AADDS).  

* Se si Abilita la funzionalità LDAP con gruppi estesi, i [volumi Kerberos](configure-kerberos-encryption.md) abilitati per LDAP non visualizzeranno correttamente il proprietario del file per gli utenti LDAP. Per impostazione predefinita, un file o una directory creata da un utente LDAP sarà `root` il proprietario anziché l'utente LDAP effettivo. L' `root` account può tuttavia modificare manualmente la proprietà del file utilizzando il comando `chown <username> <filename>` . 

* Dopo aver creato il volume, non è possibile modificare l'impostazione dell'opzione LDAP (abilitata o disabilitata).  

* Nella tabella seguente vengono descritte le impostazioni TTL (time to Live) per la cache LDAP. È necessario attendere che la cache venga aggiornata prima di tentare di accedere a un file o a una directory tramite un client. In caso contrario, nel client verrà visualizzato un messaggio di accesso negato. 

    |     Condizione di errore    |     Soluzione    |
    |-|-|
    | Cache |  Timeout predefinito |
    | Elenco appartenenza a gruppi  | TTL a 24 ore  |
    | Gruppi UNIX  | TTL a 24 ore, TTL negativo 1 minuto  |
    | Utenti UNIX  | TTL a 24 ore, TTL negativo 1 minuto  |

    Le cache hanno un periodo di timeout specifico denominato *durata (TTL*). Dopo il periodo di timeout, le voci si sono rivelate in modo che le voci non aggiornate non indugino. Il valore *TTL negativo* è il punto in cui si verifica un errore di ricerca che consente di evitare problemi di prestazioni causati da query LDAP per oggetti che potrebbero non esistere ".        

## <a name="steps"></a>Passaggi

1. La funzionalità LDAP con gruppi estesi è attualmente disponibile in anteprima. Prima di usare questa funzionalità per la prima volta, è necessario registrare la funzionalità:  

    1. Registrare la funzionalità:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Verificare lo stato della registrazione della funzionalità: 

        > [!NOTE]
        > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Attendere che lo stato sia `Registered` prima di continuare.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    È anche possibile usare i [comandi dell'interfaccia](/cli/azure/feature) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

2. I volumi LDAP richiedono una configurazione Active Directory per le impostazioni del server LDAP. Seguire le istruzioni riportate in [requisiti per le connessioni Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections) e [creare una connessione Active Directory](create-active-directory-connections.md#create-an-active-directory-connection) per configurare Active Directory connessioni sul portale di Azure.  

3. Verificare che il Active Directory server LDAP sia attivo e in esecuzione nel Active Directory. 

4. Per gli utenti LDAP NFS è necessario che nel server LDAP siano presenti determinati attributi POSIX. Impostare gli attributi per gli utenti LDAP e i gruppi LDAP come indicato di seguito: 

    * Attributi obbligatori per gli utenti LDAP:   
        `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: user`
    * Attributi obbligatori per i gruppi LDAP:   
        `objectClass: group`, `gidNumber: 555`

    È possibile gestire gli attributi POSIX usando lo snap-in MMC utenti e computer Active Directory. Nell'esempio seguente viene illustrato l'editor di attributi Active Directory:  

    ![Editor attributi Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. Se si vuole configurare un client Linux integrato LDAP, vedere [configurare un client NFS per Azure NetApp files](configure-nfs-clients.md).

6.  Seguire i passaggi in [creare un volume NFS per Azure NetApp files](azure-netapp-files-create-volumes.md) per creare un volume NFS. Durante il processo di creazione del volume, nella scheda **protocollo** abilitare l'opzione **LDAP** .   

    ![Screenshot che mostra la pagina creare un volume con l'opzione LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Facoltativo: è possibile abilitare gli utenti client NFS locali non presenti nel server LDAP Windows per accedere a un volume NFS con LDAP con gruppi estesi abilitati. A tale scopo, abilitare l'opzione **Consenti agli utenti NFS locali con LDAP** come indicato di seguito:
    1. Fare clic su **Active Directory connessioni**.  In una connessione Active Directory esistente, fare clic sul menu di scelta rapida (i tre punti `…` ) e selezionare **modifica**.  
    2. Nella finestra **Modifica impostazioni Active Directory** visualizzata selezionare l'opzione **Consenti utenti NFS locali con LDAP** .  

    ![Screenshot che mostra l'opzione Consenti agli utenti NFS locali con LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Passaggi successivi  

* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare e gestire connessioni di Active Directory](create-active-directory-connections.md)
* [Risolvere i problemi del volume LDAP](troubleshoot-ldap-volumes.md)
