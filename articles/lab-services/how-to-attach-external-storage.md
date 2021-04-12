---
title: Utilizzo dell'archiviazione di file esterni in Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab che usa l'archiviazione di file esterni in Lab Services.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111796"
---
# <a name="using-external-file-storage-in-lab-services"></a>Uso dell'archiviazione di file esterni in Lab Services

Questo articolo illustra alcune opzioni per l'archiviazione di file esterni quando si usa Azure Lab Services.  [File di Azure](https://azure.microsoft.com/services/storage/files/) offre condivisioni file completamente gestite nel cloud [accessibili tramite SMB 2,1 e SMB 3,0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Una condivisione di File di Azure può essere connessa pubblicamente o privatamente all'interno di una rete virtuale.  Inoltre, può essere configurato per usare le credenziali di Active Directory per la connessione alla condivisione file di Student.  L'uso di Azure NetApp Files con volumi NFS per computer Linux è un'altra opzione per l'archiviazione di file esterni con Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Scelta della soluzione da usare

Ogni soluzione presenta requisiti e capacità diversi.  Nella tabella seguente sono elencati i punti importanti da considerare per ogni soluzione.  

|     Soluzione    |    Importante da tenere presente    |
| -------------- | ------------------------ |
| [File di Azure condivisione con endpoint pubblico](#azure-files-share) | <ul><li>Tutti hanno accesso in lettura/scrittura.</li><li>Non è necessario alcun peering vnet.</li><li>Accessibile a tutte le macchine virtuali, non solo alle macchine virtuali del Lab.</li><li>Se si usa Linux, gli studenti avranno accesso alla chiave dell'account di archiviazione.</li></ul> |
| [File di Azure condivisione con endpoint privato](#azure-files-share) | <ul><li>Tutti hanno accesso in lettura/scrittura.</li><li>Il peering VNET è obbligatorio.</li><li>Accessibile solo alle macchine virtuali nella stessa rete (o rete con peering) come account di archiviazione.</li><li>Se si usa Linux, gli studenti avranno accesso alla chiave dell'account di archiviazione.</li></ul> |
| [File di Azure con autorizzazione basata sull'identità](#azure-files-with-identity-base-authorization) | <ul><li>È possibile impostare le autorizzazioni di accesso in lettura o in lettura/scrittura per la cartella o il file.</li><li>Il peering VNET è obbligatorio.</li><li>L'account di archiviazione deve essere connesso a Active Directory.</li><li>Le macchine virtuali del lab devono essere unite a un dominio.</li><li>Chiave dell'account di archiviazione non usata per gli studenti per la connessione alla condivisione file.</li></ul> |
| [File NetApp con volumi NFS](#netapp-files-with-nfs-volumes) | <ul><li>È possibile impostare l'accesso in lettura o in lettura/scrittura per i volumi.</li><li>Le autorizzazioni vengono impostate tramite l'indirizzo IP della macchina virtuale Student.</li><li>Il peering VNET è obbligatorio.</li><li>Potrebbe essere necessario eseguire la registrazione per usare il servizio file NetApp.</li><li>Solo Linux.</li></ul>

Il costo di utilizzo dell'archiviazione esterna non è incluso nel costo dell'utilizzo di Azure Lab Services.  Per ulteriori informazioni sui prezzi, vedere [file di Azure prezzi](https://azure.microsoft.com/pricing/details/storage/files/) e prezzi [Azure NetApp files](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Condivisione di File di Azure

È possibile accedere alle condivisioni file di Azure usando un endpoint pubblico o privato.  Le condivisioni file di Azure vengono montate usando la chiave dell'account di archiviazione come password.  Con questo approccio, tutti hanno accesso in lettura e scrittura alla condivisione file.

Se si usa un endpoint pubblico per la condivisione di File di Azure, è importante ricordare quanto segue:

- Non è necessario eseguire il peering della rete virtuale per l'account di archiviazione all'account Lab.  La condivisione file può essere creata in qualsiasi momento prima della pubblicazione della macchina virtuale del modello.
- È possibile accedere alla condivisione file da qualsiasi computer se un utente ha la chiave dell'account di archiviazione.  
- Gli studenti Linux possono visualizzare la chiave dell'account di archiviazione.  Le credenziali per il montaggio di una condivisione di File di Azure vengono archiviate in `{file-share-name}.cred` in VM Linux e leggibili da sudo.  Poiché per impostazione predefinita agli studenti viene concesso l'accesso sudo nelle macchine virtuali del servizio lab di Azure, è possibile leggere la chiave dell'account di archiviazione.  Se l'endpoint dell'account di archiviazione è pubblico, gli studenti possono accedere alla condivisione file all'esterno della macchina virtuale per studenti.  Provare a ruotare la chiave dell'account di archiviazione dopo che la classe è terminata e usando condivisioni file private.

Se si usa un endpoint privato per la condivisione di File di Azure, è importante ricordare quanto segue:

- L'accesso è limitato al traffico proveniente dalla rete privata e non è possibile accedervi tramite la rete Internet pubblica.  Solo le macchine virtuali nella rete virtuale privata, le VM in una rete con peering alla rete virtuale privata o i computer connessi a una VPN per la rete privata possono accedere alla condivisione file.  
- Gli studenti Linux possono visualizzare la chiave dell'account di archiviazione.  Le credenziali per il montaggio di una condivisione di File di Azure vengono archiviate in `{file-share-name}.cred` in VM Linux e leggibili da sudo.  Poiché per impostazione predefinita agli studenti viene concesso l'accesso sudo nelle macchine virtuali del servizio lab di Azure, è possibile leggere la chiave dell'account di archiviazione.  Provare a ruotare la chiave dell'account di archiviazione dopo che la classe è terminata.
- Questo approccio richiede il peering della rete virtuale di condivisione file con l'account Lab.  È necessario eseguire il peering della rete virtuale per l'account di archiviazione di Azure alla rete virtuale per l'account Lab **prima** della creazione del Lab.

> [!NOTE]
> Le condivisioni file più grandi di 5 TB sono disponibili solo per gli [account di archiviazione con ridondanza locale (con ridondanza locale)](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions).

Seguire questa procedura per creare una VM connessa a una condivisione file di Azure.

1. Creare un [account di archiviazione di Azure](/azure/storage/files/storage-how-to-create-file-share). Nella pagina "metodo di connettività" scegliere endpoint pubblico o privato.
2. Se si usa, creare un [endpoint privato](/azure/private-link/create-private-endpoint-storage-portal) per consentire l'accesso alle condivisioni file dalla rete virtuale.  Creare una [zona DNS privata](/azure/dns/private-dns-privatednszone) o utilizzarne una esistente. Le zone DNS private di Azure forniscono la risoluzione dei nomi all'interno di una rete virtuale.
3. Creare una [condivisione file di Azure](/azure/storage/files/storage-how-to-create-file-share). La condivisione file è raggiungibile dal nome host pubblico dell'account di archiviazione.
4. Montare la condivisione file di Azure nella macchina virtuale del modello:
    - [Windows](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Vedere [uso di file di Azure con Linux](#using-azure-files-with-linux) per evitare problemi di montaggio nelle macchine virtuali per studenti.
5. [Pubblicare](how-to-create-manage-template.md#publish-the-template-vm) la macchina virtuale del modello.

> [!IMPORTANT]
> Verificare che il firewall non blocchi la connessione SMB in uscita tramite la porta 445. Per impostazione predefinita, SMB è consentito per le macchine virtuali di Azure.

### <a name="using-azure-files-with-linux"></a>Uso di File di Azure con Linux

Se si usano le _istruzioni predefinite_ per montare una condivisione file di Azure, la condivisione file sembrerebbe scomparire nelle VM degli studenti una volta pubblicato il modello.  Di seguito è riportato uno script modificato che risolve questo problema.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Se la macchina virtuale modello è già pubblicata che monta la condivisione file di Azure nella `/mnt` Directory, lo studente può eseguire una delle operazioni seguenti.

- Spostare l'istruzione da montare nella `/mnt` parte superiore del `/etc/fstab` file.  
- Modificare le istruzioni per il montaggio `/mnt/{file-share-name}` in directory diverse `/prm-mnt/{file-share-name}` , ad esempio.

Gli studenti devono eseguire `mount -a` per rimontare le directory.

Per informazioni generali sull'uso delle condivisioni file con Linux, vedere [usare file di Azure con Linux](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>File di Azure con autorizzazione basata sull'identità

È possibile accedere alle condivisioni di File di Azure anche usando l'autenticazione di Active Directory se

1. La macchina virtuale Student è aggiunta a un dominio.
2. L'autenticazione di Active Directory è [abilitata nell'account di archiviazione di Azure](/azure/storage/files/storage-files-active-directory-overview) che ospita la condivisione file.  

L'unità di rete viene montata nella macchina virtuale usando l'identità dell'utente, non la chiave per l'account di archiviazione.  L'accesso all'account di archiviazione può usare endpoint pubblici o privati.

Verranno ora esaminati alcuni aspetti importanti di questo approccio.

- Le autorizzazioni possono essere impostate a livello di directory o di file.
- Le credenziali dell'utente corrente vengono usate per l'autenticazione nella condivisione file.

Se si usa un endpoint pubblico per la condivisione di File di Azure, è importante ricordare quanto segue:

- La rete virtuale per l'account di archiviazione non deve eseguire il peering con l'account Lab.  La condivisione file può essere creata in qualsiasi momento prima della pubblicazione della macchina virtuale del modello.

Se si usa un endpoint privato per la condivisione di File di Azure, è importante ricordare quanto segue:

- L'accesso è limitato al traffico proveniente dalla rete privata e non è possibile accedervi tramite la rete Internet pubblica.  Solo le macchine virtuali nella rete virtuale privata, le VM in una rete con peering alla rete virtuale privata o i computer connessi a una VPN per la rete privata possono accedere alla condivisione file.  
- Questo approccio richiede il peering della rete virtuale di condivisione file con l'account Lab.  È necessario eseguire il peering della rete virtuale per l'account di archiviazione di Azure alla rete virtuale per l'account Lab **prima** della creazione del Lab.

Attenersi alla procedura seguente per creare un'autenticazione AD abilitata File di Azure condivisione e dominio aggiungere le macchine virtuali del Lab.

1. Creare un [account di archiviazione di Azure](/azure/storage/files/storage-how-to-create-file-share).
2. Se si usa, creare un [endpoint privato](/azure/private-link/create-private-endpoint-storage-portal) per consentire l'accesso alle condivisioni file dalla rete virtuale.  Creare una [zona DNS privata](/azure/dns/private-dns-privatednszone) o utilizzarne una esistente. Le zone DNS private di Azure forniscono la risoluzione dei nomi all'interno di una rete virtuale.
3. Creare una [condivisione file di Azure](/azure/storage/files/storage-how-to-create-file-share).
4. Attenersi alla procedura per abilitare l'autorizzazione basata sull'identità.  Se si usa un annuncio locale sincronizzato con Azure AD, seguire la procedura per [l'autenticazione Active Directory Domain Services locale su SMB per le condivisioni file di Azure](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Se si usa solo Azure AD, seguire i passaggi per [abilitare l'autenticazione Azure Active Directory Domain Services in file di Azure](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Comunicare con il team che gestisce l'annuncio per verificare che tutti i prerequisiti elencati nelle istruzioni siano soddisfatti.
5. Assegnare i ruoli di autorizzazione condivisione SMB in Azure.  Per informazioni dettagliate sulle autorizzazioni concesse a ogni ruolo, vedere [autorizzazioni a livello di condivisione](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. È necessario assegnare il ruolo "collaboratore con privilegi elevati di condivisione SMB per i dati dei file di archiviazione" all'utente o al gruppo che imposterà le autorizzazioni per il contenuto della condivisione file.
    2. Il ruolo "collaboratore condivisione SMB di dati dei file di archiviazione" deve essere assegnato agli studenti che devono aggiungere o modificare file nella condivisione file.
    3. Il ruolo "lettore condivisione SMB dati file di archiviazione" deve essere assegnato agli studenti che dovranno solo leggere i file dalla condivisione file.
6. Configurare le autorizzazioni a livello di directory/file per la condivisione file.  Le autorizzazioni devono essere configurate da un computer aggiunto a un dominio con accesso di rete alla condivisione file.  **Per modificare le autorizzazioni a livello di directory/file, montare la condivisione file usando la chiave di archiviazione e non le credenziali di AAD.**  [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) I comandi di PowerShell o [icacls](/windows-server/administration/windows-commands/icacls) sono gli strumenti consigliati da usare quando si assegnano le autorizzazioni.
7. Eseguire il [peering della rete virtuale](how-to-connect-peer-virtual-network.md) per l'account di archiviazione all'account Lab.
8. [Creare il Lab della classe](how-to-manage-classroom-labs.md).
9. Salvare uno script nella macchina virtuale modello che gli studenti possono eseguire per connettersi all'unità di rete.  Per ottenere uno script di esempio:
    1. Aprire l'account di archiviazione nel portale di Azure.
    1. Selezionare **condivisioni file** in **servizio file** nel menu.
    1. Individuare la condivisione a cui si desidera connettersi, selezionare il pulsante con i puntini di sospensione all'estrema destra e scegliere **Connetti**.
    1. Il volo di **connessione** si aprirà con le istruzioni per Windows, Linux e MacOS.  Se si usa Windows, impostare il **metodo di autenticazione** su **Active Directory**.
    1. Copiare il codice nell'esempio e salvarlo nel computer modello in un `.ps1` file per Windows o `.sh` file per Linux.
10. Nel computer modello scaricare ed eseguire lo script per [aggiungere computer studente al dominio](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  Lo `Join-AzLabADTemplate` script [pubblicherà automaticamente la macchina virtuale del modello](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > Il computer modello non verrà aggiunto al dominio. Gli insegnanti devono assegnare a se stessi una macchina virtuale studente pubblicata per visualizzare i file nella condivisione.
11. Gli studenti che usano Windows possono connettersi alla condivisione File di Azure usando [Esplora file](/azure/storage/files/storage-how-to-use-files-windows) con le relative credenziali, una volta specificato il percorso della condivisione file.  In alternativa, gli studenti possono eseguire lo script creato in precedenza per connettersi all'unità di rete.  Per gli studenti che usano Linux, eseguire lo script creato in precedenza.

## <a name="netapp-files-with-nfs-volumes"></a>File NetApp con volumi NFS

[Azure NetApp files](https://azure.microsoft.com/services/netapp/) è un servizio di archiviazione file a consumo, a prestazioni elevate e di livello aziendale.

- I criteri di accesso possono essere impostati in base ai singoli volumi.
- I criteri di autorizzazione sono basati su IP per ogni volume.
- Se gli studenti necessitano di un proprio volume a cui gli altri studenti non hanno accesso, i criteri di autorizzazione devono essere assegnati dopo la pubblicazione del Lab.
- Nel contesto di Azure Lab Services, sono supportati solo i computer Linux.
- La rete virtuale per il pool di capacità di Azure NetApp Files deve essere sottoposti a peering nella rete virtuale per l'account Lab **prima** della creazione del Lab.

Attenersi alla procedura seguente per usare una condivisione di Azure NetApp Files in Azure Lab Services.

1. Eseguire l'onboarding in [Azure NetApp files](https://aka.ms/azurenetappfiles), se necessario.
2. Per creare un pool di capacità dei file NetApp e i volumi NFS, vedere [configurare Azure NetApp files e volume NFS](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Per informazioni sui livelli di servizio, vedere [livelli di servizio per Azure NetApp files](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. Eseguire il [peering della rete virtuale](how-to-connect-peer-virtual-network.md) per il pool di capacità dei file NetApp nell'account Lab.
4. [Creare il Lab della classe](how-to-manage-classroom-labs.md).
5. Nella macchina virtuale modello installare i componenti necessari per usare le condivisioni file NFS.
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Nella macchina virtuale modello salvare lo script seguente come `mount_fileshare.sh` per [montare la condivisione file NetApp](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Assegnare la `capacity_pool_ipaddress` variabile all'indirizzo IP di destinazione di montaggio per il pool di capacità.  Ottenere le istruzioni di montaggio per il volume per trovare il valore appropriato.  Lo script prevede il percorso e il nome del volume dei file NetApp.  Non dimenticare di eseguire `chmod u+x mount_fileshare.sh` per assicurarsi che lo script possa essere eseguito dagli utenti.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Se tutti gli studenti condividono l'accesso allo stesso volume di file NetApp, lo `mount_fileshare.sh` script può essere eseguito nel computer modello prima della pubblicazione.  Se gli studenti ottengono il proprio volume, salvare lo script da eseguire in un secondo momento dallo studente.
8. [Pubblicare](how-to-create-manage-template.md#publish-the-template-vm) la macchina virtuale del modello.
9. [Configurare i criteri](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) per la condivisione file.  I criteri di esportazione possono consentire a una singola macchina virtuale o a più macchine virtuali di accedere a un volume.  È possibile concedere l'accesso in sola lettura o in lettura/scrittura.
10. Gli studenti devono avviare la macchina virtuale ed eseguire lo script per montare la condivisione file.  Dovranno eseguire lo script una sola volta.  Il comando sarà simile a `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)