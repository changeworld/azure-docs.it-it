---
title: Distribuire Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni su come distribuire Sincronizzazione file di Azure, dall'inizio alla fine, usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 989bcbb7e509b9b7692f067af2989fcad94b6ad1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796883"
---
# <a name="deploy-azure-file-sync"></a>Distribuire Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

È consigliabile leggere [Pianificazione per la distribuzione di File di Azure](../files/storage-files-planning.md) e [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md) prima di completare i passaggi descritti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Una condivisione file di Azure nella stessa area che si vuole distribuire Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](file-sync-planning.md#azure-file-sync-region-availability) per Sincronizzazione file di Azure.
    - [Creare una condivisione file](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
1. Almeno un'istanza supportata del cluster Windows Server o Windows Server da sincronizzare con Sincronizzazione file di Azure. Per altre informazioni sulle versioni supportate di Windows Server e sulle risorse di sistema consigliate, vedere Considerazioni su [Windows file server .](file-sync-planning.md#windows-file-server-considerations)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Una condivisione file di Azure nella stessa area che si vuole distribuire Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](file-sync-planning.md#azure-file-sync-region-availability) per Sincronizzazione file di Azure.
    - [Creare una condivisione file](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
1. Almeno un'istanza supportata del cluster Windows Server o Windows Server da sincronizzare con Sincronizzazione file di Azure. Per altre informazioni sulle versioni supportate di Windows Server e sulle risorse di sistema consigliate, vedere Considerazioni su [Windows file server .](file-sync-planning.md#windows-file-server-considerations)

1. Il modulo Az PowerShell può essere usato con PowerShell 5.1 o PowerShell 6+. È possibile usare il modulo Az PowerShell per Sincronizzazione file di Azure in qualsiasi sistema supportato, inclusi i sistemi non Windows, tuttavia il cmdlet di registrazione del server deve sempre essere eseguito nell'istanza di Windows Server che si sta registrando (questa operazione può essere eseguita direttamente o tramite la comunicazione remota di PowerShell). In Windows Server 2012 R2 è possibile verificare che sia in esecuzione almeno PowerShell 5.1. \* esaminando il valore della proprietà **PSVersion** **dell'oggetto $PSVersionTable:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se il valore **psVersion** è minore di 5.1. , come nel caso della maggior parte delle installazioni aggiornate di Windows Server 2012 R2, è possibile eseguire facilmente l'aggiornamento scaricando e installando \* Windows Management Framework [(WMF) 5.1.](https://www.microsoft.com/download/details.aspx?id=54616) Il pacchetto appropriato per il download e l'installazione per Windows Server 2012 R2 è **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu.** 

    PowerShell 6+ può essere usato con qualsiasi sistema supportato e può essere scaricato tramite la pagina [GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se si prevede di usare l'interfaccia utente di Registrazione server, anziché eseguire la registrazione direttamente da PowerShell, è necessario usare PowerShell 5.1.

1. Se si è scelto di usare PowerShell 5.1, assicurarsi che sia installato almeno .NET 4.7.2. Altre informazioni sulle [.NET Framework e sulle dipendenze](/dotnet/framework/migration-guide/versions-and-dependencies) nel sistema.

    > [!Important]  
    > Se si installa .NET 4.7.2+ in Windows Server Core, è necessario eseguire l'installazione con i flag e . In caso contrario, `quiet` `norestart` l'installazione avrà esito negativo. Ad esempio, se si installa .NET 4.8, il comando sarà simile al seguente:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Il modulo Az PowerShell, che può essere installato seguendo le istruzioni riportate qui: [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Il modulo Az.StorageSync viene ora installato automaticamente quando si installa il modulo Az PowerShell.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Una condivisione file di Azure nella stessa area che si vuole distribuire Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](file-sync-planning.md#azure-file-sync-region-availability) per Sincronizzazione file di Azure.
    - [Creare una condivisione file](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
1. Almeno un'istanza supportata di Windows Server o del cluster Windows Server da sincronizzare con Sincronizzazione file di Azure. Per altre informazioni sulle versioni supportate di Windows Server e sulle risorse di sistema consigliate, vedere Considerazioni su [Windows file server.](file-sync-planning.md#windows-file-server-considerations)

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

   Se si preferisce, è anche possibile usare Azure Cloud Shell per completare la procedura descritta in questa esercitazione.  Azure Cloud Shell è un ambiente di shell interattivo utilizzabile tramite il browser.  Per avviare Cloud Shell, usare uno di questi metodi:

   - Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. **Prova si** aprirà Azure Cloud Shell, ma non copia automaticamente il codice in Cloud Shell.

   - Aprire Cloud Shell passando a [https://shell.azure.com](https://shell.azure.com)

   - Selezionare il pulsante **Cloud Shell** sulla barra dei menu nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com)

1. Accedere.

   Accedere usando il comando [az login](/cli/azure/reference-index#az_login) se si usa un'installazione locale dell'interfaccia della riga di comando.

   ```azurecli
   az login
   ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

1. Installare [l'estensione az filesync dell'interfaccia](/cli/azure/ext/storagesync/storagesync) della riga di comando di Azure.

   ```azurecli
   az extension add --name storagesync
   ```

   Dopo aver installato il riferimento **all'estensione storagesync,** verrà visualizzato l'avviso seguente.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparare Windows Server per l'uso con Sincronizzazione file di Azure
Per ogni server da usare con Sincronizzazione file di Azure, incluso ogni nodo server in un cluster di failover, disabilitare **Sicurezza avanzata di Internet Explorer**. Questa operazione è necessaria solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.

# <a name="portal"></a>[Portale](#tab/azure-portal)
> [!Note]  
> È possibile ignorare questo passaggio se si distribuisce Sincronizzazione file di Azure in Windows Server Core.

1. Aprire Server Manager.
2. Fare clic su **Server locale**:  
    !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. Ne riquadro secondario **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  
    ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. Nella finestra **Internet Explorer di dialogo Configurazione sicurezza avanzata** selezionare **Disattivato** per **Amministratori** e **utenti**:  
    ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per disabilitare Sicurezza avanzata di Internet Explorer, eseguire quanto segue da una sessione di PowerShell con privilegi elevati:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire le istruzioni per il portale di Azure o PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Distribuire il servizio di sincronizzazione archiviazione 
La distribuzione di Sincronizzazione file di Azure inizia con l'inserimento di una risorsa **servizio di sincronizzazione archiviazione** in un gruppo di risorse della sottoscrizione selezionata. È consigliabile effettuare il provisioning di alcune di queste risorse in base alle esigenze. Verrà creata una relazione di trust tra i server e la risorsa e un server può essere registrato solo in un servizio di sincronizzazione archiviazione. Di conseguenza, è consigliabile distribuire tutti i servizi di sincronizzazione archiviazione necessari per separare i gruppi di server. Tenere presente che non è possibile sincronizzare tra di loro i server di servizi di sincronizzazione archiviazione diversi.

> [!Note]
> Il servizio di sincronizzazione archiviazione eredita le autorizzazioni di accesso dalla sottoscrizione e dal gruppo di risorse in cui è stato distribuito. È consigliabile controllare attentamente chi dispone di accesso. Le entità con accesso in scrittura possono avviare la sincronizzazione di nuovi set di file dai server registrati in questo servizio di sincronizzazione archiviazione, causando il flusso dei dati in un archivio di Azure a loro accessibile.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per distribuire un servizio di sincronizzazione archiviazione, passare al [portale di Azure](https://portal.azure.com/), fare clic su *Crea* una risorsa e quindi cercare Sincronizzazione file di Azure. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure** e quindi selezionare **Crea** per aprire la **scheda Distribuisci sincronizzazione** archiviazione.

Nel pannello che viene visualizzato immettere le informazioni seguenti:

- **Nome**: nome univoco (per area) per il servizio di sincronizzazione archiviazione.
- **Sottoscrizione**: la sottoscrizione in cui creare il servizio di sincronizzazione archiviazione. A seconda della strategia di configurazione dell'organizzazione, è possibile accedere a una o più sottoscrizioni. Una sottoscrizione di Azure è il contenitore di base per la fatturazione di ogni servizio cloud, ad esempio File di Azure.
- **Gruppo di risorse:** un gruppo di risorse è un gruppo logico di risorse di Azure, ad esempio un account di archiviazione o un servizio di sincronizzazione archiviazione. È possibile creare un nuovo gruppo di risorse o usare un gruppo di risorse esistente per Sincronizzazione file di Azure. È consigliabile usare i gruppi di risorse come contenitori per isolare le risorse in modo logico per l'organizzazione, ad esempio il raggruppamento delle risorse umane o delle risorse per un progetto specifico.
- **Località:** l'area in cui si vuole distribuire Sincronizzazione file di Azure. In questo elenco sono disponibili solo le aree supportate.

Al termine, selezionare **Crea** per distribuire il servizio di sincronizzazione di archiviazione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Sostituire `<Az_Region>` , e con i propri `<RG_Name>` `<my_storage_sync_service>` valori, quindi usare i comandi seguenti per creare e distribuire un servizio di sincronizzazione archiviazione:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire le istruzioni per l'portale di Azure o PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
È possibile scaricare l'agente dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Al termine del download, fare doppio clic sul pacchetto MSI per avviare l'installazione dell'agente Sincronizzazione file di Azure.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster. Ogni nodo del cluster deve essere registrato per funzionare con Sincronizzazione file di Azure.

È consigliabile eseguire queste operazioni:
- Lasciare il percorso di installazione predefinito (c:\Programmi\Microsoft Files\Azure\StorageSyncAgent), per semplificare la risoluzione dei problemi e la manutenzione del server.
- Abilitare Microsoft Update per mantenere sempre aggiornato Sincronizzazione file di Azure. Tutti gli aggiornamenti per l'agente Sincronizzazione file di Azure, inclusi gli aggiornamenti delle funzionalità e gli hotfix, vengono eseguiti tramite Microsoft Update. È consigliabile installare l'aggiornamento più recente per Sincronizzazione file di Azure. Per altre informazioni, vedere criteri [Sincronizzazione file di Azure di aggiornamento.](file-sync-planning.md#azure-file-sync-agent-update-policy)

Al termine dell'installazione dell'agente Sincronizzazione file di Azure, viene avviata automaticamente l'interfaccia utente di Registrazione server. È necessario disporre di un servizio di sincronizzazione archiviazione prima di eseguire la registrazione. Per creare un servizio di sincronizzazione archiviazione, vedere la sezione successiva.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Eseguire il codice di PowerShell seguente per scaricare la versione appropriata dell'agente di Sincronizzazione file di Azure per il sistema operativo in uso e installarla nel sistema.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster. Ogni nodo del cluster deve registrato per l'utilizzo con Sincronizzazione file di Azure.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire le istruzioni per l'portale di Azure o PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrare Windows Server con il servizio di sincronizzazione archiviazione
La registrazione di Windows Server con un servizio di sincronizzazione archiviazione consente di stabilire una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. Un server può essere registrato solo in un servizio di sincronizzazione archiviazione e può eseguire la sincronizzazione con altri server e condivisioni file di Azure associati allo stesso servizio di sincronizzazione archiviazione.

> [!Note]
> La registrazione server usa le credenziali di Azure per creare una relazione di trust tra il servizio di sincronizzazione archiviazione e Windows Server. Tuttavia, il server crea e usa successivamente la propria identità, che rimane valida fino a quando il server risulta registrato e il token di firma di accesso condiviso (SAS di archiviazione) corrente è valido. Non è possibile rilasciare un nuovo token di firma di accesso condiviso per il server dopo l'annullamento della registrazione del server, quindi il server non può più accedere alle condivisioni file di Azure con il conseguente arresto di ogni attività di sincronizzazione.

L'amministratore che registra il server deve essere membro dei ruoli di gestione **Proprietario** o **Collaboratore** per il servizio di sincronizzazione archiviazione specificato. Questa impostazione può essere configurata in Controllo di accesso **(IAM)** nel portale di Azure per il servizio di sincronizzazione archiviazione.

È anche possibile distinguere gli amministratori in grado di registrare i server da quelli autorizzati a configurare la sincronizzazione anche in un servizio di sincronizzazione archiviazione. A questo punto è necessario creare un ruolo personalizzato in cui elencare gli amministratori autorizzati solo a registrare i server e assegnare al ruolo personalizzato le autorizzazioni seguenti:

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[Portale](#tab/azure-portal)
L'interfaccia utente di Registrazione server viene visualizzata automaticamente al termine dell'installazione dell'agente Sincronizzazione file di Azure. In caso contrario, è possibile aprirla manualmente dal percorso: c:\Programmi\Microsoft Files\Azure\StorageSyncAgent\ServerRegistration.exe. Dopo avere aperto l'interfaccia utente di Registrazione server fare clic su **Accesso** per iniziare.

Eseguito l'accesso, viene richiesto di specificare le informazioni seguenti:

![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Sottoscrizione di Azure**: la sottoscrizione che contiene il servizio di sincronizzazione archiviazione (vedere [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service)). 
- **Gruppo di risorse**: il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.
- **Servizio di sincronizzazione archiviazione**: il nome del servizio di sincronizzazione archiviazione con cui si effettua la registrazione.

Dopo avere selezionato le informazioni appropriate, fare clic su **Registra** per completare la registrazione del server. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire le istruzioni per il portale di Azure o PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Creare un gruppo di sincronizzazione e un endpoint cloud
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere un endpoint cloud, che rappresenta una condivisione file di Azure, e uno o più endpoint server. Un endpoint server rappresenta un percorso in un server registrato. Un server può avere endpoint server in più gruppi di sincronizzazione. È possibile creare tutti i gruppi di sincronizzazione necessari per descrivere in modo appropriato la topologia di sincronizzazione desiderata.

Un endpoint cloud è un puntatore a una condivisione file di Azure. Tutti gli endpoint server vengono sincronizzati con un endpoint cloud, che diventa quindi l'hub. L'account di archiviazione per la condivisione file di Azure deve trovarsi nella stessa area del servizio di sincronizzazione archiviazione. Viene sincronizzata l'intera condivisione file di Azure con un'unica eccezione: viene effettuato il provisioning di una cartella speciale, paragonabile alla cartella di informazioni sul volume di sistema nascosta, in un volume NTFS. Tale directory è denominata ".SystemShareInformation". Contiene metadati di sincronizzazione importanti che non vengono sincronizzati negli altri endpoint. Non usarla né eliminarla.

> [!Important]  
> È possibile apportare modifiche a qualsiasi endpoint cloud o endpoint server nel gruppo di sincronizzazione e fare in modo che i file vengano sincronizzati con gli altri endpoint del gruppo di sincronizzazione. Se si apporta direttamente una modifica all'endpoint cloud (condivisione file di Azure), le modifiche apportate devono essere prima di tutto individuate da un processo di rilevamento delle modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Per altre informazioni, vedere [Domande frequenti su File di Azure](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection).

L'amministratore che crea l'endpoint cloud deve essere un membro del ruolo di gestione **Proprietario** per l'account di archiviazione che contiene la condivisione file di Azure a cui punta l'endpoint cloud. Questa impostazione può essere configurata in **Controllo di accesso (IAM)** nel portale di Azure per l'account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un gruppo di sincronizzazione, [nel portale di Azure](https://portal.azure.com/)passare al servizio di sincronizzazione archiviazione e quindi selezionare + Gruppo **di sincronizzazione:**

![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

Nel riquadro che viene visualizzato immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

- **Sync group name**(Nome gruppo di sincronizzazione): nome del gruppo di sincronizzazione da creare. Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente.
- **Sottoscrizione**: la sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione in [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service).
- **Account di archiviazione**: se si seleziona **Selezionare l'account di archiviazione**, viene visualizzato un altro riquadro in cui è possibile selezionare l'account di archiviazione che contiene la condivisione file di Azure con cui si vuole eseguire la sincronizzazione.
- **Condivisione file di Azure**: il nome della condivisione file di Azure con cui si vuole eseguire la sincronizzazione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare il gruppo di sincronizzazione, eseguire il comando di PowerShell seguente. Ricordarsi di sostituire `<my-sync-group>` con il nome desiderato per il gruppo di sincronizzazione.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Dopo aver creato correttamente il gruppo di sincronizzazione, è possibile creare l'endpoint cloud. Assicurarsi di sostituire `<my-storage-account>` e `<my-file-share>` con i valori previsti.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il [comando az storagesync sync-group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) per creare un nuovo gruppo di sincronizzazione.  Per impostare come predefinito un gruppo di risorse per tutti i comandi dell'interfaccia della riga di [comando, usare az configure](/cli/azure/reference-index#az_configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Usare il [comando az storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) per creare un nuovo endpoint cloud.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Creare un endpoint server
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server. Un endpoint server deve essere un percorso in un server registrato (diverso da una condivisione montata) e per usare la suddivisione in livelli cloud il percorso deve trovarsi in un volume non di sistema. L'archiviazione NAS (Network Attached Storage) non è supportata.

> [!NOTE]
> La modifica del percorso o della lettera di unità dopo aver stabilito un endpoint server in un volume non è supportata. Assicurarsi di usare un percorso finale nel server registrato.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per aggiungere un endpoint server, passare al gruppo di sincronizzazione appena creato e quindi selezionare **Aggiungi endpoint server.**

![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

Nel riquadro **Aggiungi endpoint server** immettere le informazioni seguenti per creare un endpoint server:

- **Server registrato:** nome del server o del cluster in cui si vuole creare l'endpoint server.
- **Percorso:** percorso di Windows Server da sincronizzare come parte del gruppo di sincronizzazione.
- **Suddivisione in livelli cloud**: l'opzione che abilita o disabilita la suddivisione in livelli cloud, che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente.
- **Spazio disponibile nel volume:** quantità di spazio libero da riservare nel volume in cui si trova l'endpoint server. Se ad esempio lo spazio disponibile nel volume è impostato su 50% per un volume con un singolo endpoint server, circa la metà dei dati viene archiviata a livelli in File di Azure. A prescindere dall'abilitazione o meno della suddivisione in livelli nel cloud, per la condivisione file di Azure è sempre disponibile una copia completa dei dati nel gruppo di sincronizzazione.
- **Modalità di download iniziale:** si tratta di una selezione facoltativa, a partire dalla versione 11 dell'agente, che può essere utile quando sono presenti file nella condivisione file di Azure, ma non nel server. Tale situazione può esistere, ad esempio, se si crea un endpoint server per aggiungere un altro server della succursale a un gruppo di sincronizzazione o quando si ripristina un server in errore. Se la livellistica cloud è abilitata, per impostazione predefinita viene richiamato solo lo spazio dei nomi, nessun contenuto di file inizialmente. Ciò è utile se si ritiene che le richieste di accesso degli utenti debbano decidere quale contenuto di file viene richiamato al server. Se la distribuzione a livelli nel cloud è disabilitata, l'impostazione predefinita è che lo spazio dei nomi scarica prima e quindi i file verranno richiamati in base al timestamp dell'ultima modifica fino a quando non viene raggiunta la capacità locale. È tuttavia possibile modificare la modalità di download iniziale solo per lo spazio dei nomi. Una terza modalità può essere usata solo se la distribuzione a livelli cloud è disabilitata per questo endpoint server. Questa modalità evita prima di tutto di richiamare lo spazio dei nomi. I file verranno visualizzati nel server locale solo se hanno avuto la possibilità di scaricarli completamente. Questa modalità è utile se, ad esempio, un'applicazione richiede la presentazione di file completi e non può tollerare i file a livelli nello spazio dei nomi.

Per aggiungere l'endpoint server, selezionare **Crea**. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Eseguire i comandi di PowerShell seguenti per creare l'endpoint server e assicurarsi di sostituire e con i valori desiderati e controllare l'impostazione facoltativa per i criteri `<your-server-endpoint-path>` `<your-volume-free-space>` di download iniziale facoltativi.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il [comando az storagesync sync-group server-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) per creare un nuovo endpoint server.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Configurare le impostazioni del firewall e della rete virtuale

### <a name="portal"></a>Portale
Se si desidera configurare la sincronizzazione file di Azure per l'utilizzo con le impostazioni del firewall e della rete virtuale, eseguire le operazioni seguenti:

1. Dal portale di Azure passare all'account di archiviazione da proteggere.
1. Selezionare **Rete** nel menu a sinistra.
1. In **Reti selezionate** in Consenti accesso **da**.
1. Assicurarsi che l'indirizzo IP o la rete virtuale dei server sia elencato nella **sezione Intervallo di** indirizzi.
1. Assicurarsi che **l'opzione Consenti servizi Microsoft'accesso a questo account di archiviazione** sia selezionata.
1. Per salvare le impostazioni, fare clic su **Save** (Salva).

    ![Configurazione delle impostazioni del firewall e della rete virtuale per l'utilizzo con Sincronizzazione file di Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding con Sincronizzazione file di Azure
I passaggi consigliati per eseguire l'onboarding Sincronizzazione file di Azure per la prima volta senza tempi di inattività mantenendo la fedeltà completa dei file e l'elenco di controllo di accesso (ACL) sono i seguenti:
 
1. Distribuire un servizio di sincronizzazione archiviazione.
1. Creare un gruppo di sincronizzazione.
1. Installare l'agente di Sincronizzazione file di Azure nel server con il set di dati completo.
1. Registrare il server e creare un endpoint del server nella condivisione. 
1. Consentire alla sincronizzazione di eseguire il caricamento completo in condivisione file di Azure (endpoint cloud).  
1. Al termine del caricamento iniziale, installare l'agente di Sincronizzazione file di Azure in ognuno dei server rimanenti.
1. Creare nuove condivisioni file in ognuno dei server rimanenti.
1. Creare endpoint server nelle nuove condivisioni file con i criteri di suddivisione in livelli nel cloud, se lo si desidera. Per questo passaggio sono necessarie altre risorse di archiviazione disponibili per l'installazione iniziale.
1. Consentire Sincronizzazione file di Azure'agente di eseguire un ripristino rapido dello spazio dei nomi completo senza il trasferimento effettivo dei dati. Dopo la sincronizzazione completa dello spazio dei nomi, il motore di sincronizzazione riempirà lo spazio su disco locale in base ai criteri suddivisione in livelli nel cloud per l'endpoint server. 
1. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze. 
1. Reindirizzare gli utenti e le applicazioni a questa nuova condivisione.
1. Facoltativamente, è possibile eliminare le condivisioni duplicate nei server.
 
Se non si dispone di risorse di archiviazione extra per l'onboarding iniziale e si desidera collegarsi alle condivisioni esistenti, è possibile effettuare il pre-seeding dei dati nelle condivisioni file di Azure. Questo approccio è consigliato solo se è possibile accettare il tempo di inattività e garantire che non avvenga alcuna modifica nelle condivisioni dei server durante il processo di onboarding iniziale. 
 
1. Assicurarsi che i dati in uno dei server non possano cambiare durante il processo di onboarding.
1. Eseguire il preseeded delle condivisioni file di Azure con i dati del server usando qualsiasi strumento di trasferimento dati tramite SMB. Robocopy, ad esempio. È anche possibile usare AzCopy su REST. Assicurarsi di usare AzCopy con le opzioni appropriate per mantenere i timestamp e gli attributi degli ACL.
1. Creare una topologia di Sincronizzazione file di Azure con gli endpoint del server desiderati che puntano alle condivisioni esistenti.
1. Consentire alla sincronizzazione di completare il processo di riconciliazione in tutti gli endpoint. 
1. Una volta completata la riconciliazione, è possibile aprire le condivisioni per le modifiche.
 
Attualmente, l'approccio di pre-seeding presenta alcune limitazioni: 
- Le modifiche ai dati nel server prima che la topologia di sincronizzazione sia completamente operativa possono causare conflitti negli endpoint server.  
- Dopo aver creato l'endpoint cloud, Sincronizzazione file di Azure esegue un processo per rilevare i file nel cloud prima di avviare la sincronizzazione iniziale. Il tempo impiegato per completare questo processo varia a seconda dei diversi fattori, ad esempio la velocità di rete, la larghezza di banda disponibile e il numero di file e cartelle. Per la stima approssimativa nella versione di anteprima, il processo di rilevamento viene eseguito a una velocità di circa 10 file/sec. Di conseguenza, anche se il pre-seeding viene eseguito velocemente, il tempo complessivo per ottenere un sistema completamente operativo può essere notevolmente più lungo quando viene effettuato il pre-seeding dei dati nel cloud.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Ripristino self-service tramite versioni precedenti e VSS (Servizio Copia Shadow del volume)

> [!IMPORTANT]
> Le informazioni seguenti possono essere usate solo con la versione 9 (o successiva) dell'agente di sincronizzazione archiviazione. Le versioni precedenti alla 9 non avranno i cmdlet StorageSyncSelfService.

Versioni precedenti è una funzionalità di Windows che consente di usare snapshot VSS lato server di un volume per presentare versioni ripristinabili di un file a un client SMB.
Ciò consente uno scenario avanzato, comunemente noto come ripristino self-service, direttamente per information worker anziché dipendere dal ripristino da un amministratore IT.

Gli snapshot VSS e le versioni precedenti funzionano indipendentemente Sincronizzazione file di Azure. Tuttavia, il cloud a livelli deve essere impostato su una modalità compatibile. Molti Sincronizzazione file di Azure endpoint server possono esistere nello stesso volume. È necessario effettuare la chiamata di PowerShell seguente per ogni volume con anche un endpoint server in cui si prevede o si usa la funzionalità di cloud a livelli.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Gli snapshot VSS vengono evasi da un intero volume. Per impostazione predefinita, per un determinato volume possono esistere fino a 64 snapshot, con spazio sufficiente per archiviare gli snapshot. VSS gestisce automaticamente questa operazione. La pianificazione snapshot predefinita accetta due snapshot al giorno, dal lunedì al venerdì. Tale pianificazione è configurabile tramite un'attività pianificata di Windows. Il cmdlet di PowerShell precedente esegue due operazioni:
1. Configura il cloud di Sincronizzazione file di Azure a livelli nel volume specificato in modo che sia compatibile con le versioni precedenti e garantisce che un file possa essere ripristinato da una versione precedente, anche se è stato a livelli nel cloud nel server. 
1. Abilita la pianificazione vss predefinita. È quindi possibile decidere di modificarlo in un secondo momento. 

> [!Note]  
> Esistono due aspetti importanti da notare:
>- Se si usa il parametro -Force e VSS è attualmente abilitato, sovrascriverà la pianificazione corrente dello snapshot VSS e la sostituirà con la pianificazione predefinita. Assicurarsi di salvare la configurazione personalizzata prima di eseguire il cmdlet.
> - Se si usa questo cmdlet in un nodo del cluster, è necessario eseguirlo anche in tutti gli altri nodi del cluster. 

Per verificare se la compatibilità del ripristino self-service è abilitata, è possibile eseguire il cmdlet seguente.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Verranno elencati tutti i volumi nel server, nonché il numero di giorni compatibili con la distribuzione a livelli del cloud per ognuno. Questo numero viene calcolato automaticamente in base al numero massimo di snapshot possibili per ogni volume e alla pianificazione predefinita degli snapshot. Pertanto, per impostazione predefinita, tutte le versioni precedenti presentate a un information worker possono essere usate per il ripristino. Lo stesso vale se si modifica la pianificazione predefinita per creare più snapshot.
Tuttavia, se si modifica la pianificazione in modo da ottenere uno snapshot disponibile nel volume precedente al valore di giorni compatibili, gli utenti non potranno usare questo snapshot precedente (versione precedente) da cui eseguire il ripristino.

> [!Note]
> L'abilitazione del ripristino self-service può influire sul consumo e sulla fattura dell'archiviazione di Azure. Questo impatto è limitato ai file attualmente a livelli nel server. L'abilitazione di questa funzionalità garantisce che nel cloud sia disponibile una versione del file a cui è possibile fare riferimento tramite una voce delle versioni precedenti (snapshot VSS).
>
> Se si disabilita la funzionalità, l'utilizzo di Archiviazione di Azure diminuisce lentamente fino al superamento della finestra dei giorni compatibili. Non è possibile velocizzare questa operazione. 

Il numero massimo predefinito di snapshot VSS per volume (64) e la pianificazione predefinita per accettarli, comportano un massimo di 45 giorni delle versioni precedenti da cui un Information Worker può eseguire il ripristino, a seconda del numero di snapshot VSS che è possibile archiviare nel volume.

Se il numero massimo di 64 snapshot VSS per volume non è l'impostazione corretta, è possibile modificare tale valore [tramite una chiave del Registro di sistema](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Per attivare il nuovo limite, è necessario eseguire nuovamente il cmdlet per abilitare la compatibilità delle versioni precedenti in ogni volume abilitato in precedenza, con il flag -Force per prendere in considerazione il nuovo numero massimo di snapshot VSS per volume. Il risultato sarà un nuovo numero calcolato di giorni compatibili. Si noti che questa modifica avrà effetto solo sui file appena a livelli e sovrascriverà tutte le personalizzazioni nella pianificazione del Servizio Copia Copia Backup di Microsoft.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Richiamare in modo proattivo i file nuovi e modificati da una condivisione file di Azure

Con la versione 11 dell'agente, una nuova modalità diventa disponibile in un endpoint server. Questa modalità consente alle aziende distribuite a livello globale di avere la cache del server in un'area remota precompilato anche prima che gli utenti locali accedono a qualsiasi file. Se abilitata in un endpoint server, questa modalità fa sì che il server richiami i file creati o modificati nella condivisione file di Azure.

### <a name="scenario"></a>Scenario

Una società distribuita a livello globale ha succursali negli Stati Uniti e in India. Nella mattina (ora degli Stati Uniti) gli information worker creano una nuova cartella e nuovi file per un progetto completamente nuovo e lavorano tutto il giorno su di esso. Sincronizzazione file di Azure la cartella e i file verranno sincronizzati con la condivisione file di Azure (endpoint cloud). Gli Information Worker in India continueranno a lavorare al progetto nel fuso orario. Quando arrivano la mattina, il server Sincronizzazione file di Azure locale abilitato in India deve avere questi nuovi file disponibili in locale, in modo che il team india possa lavorare in modo efficiente fuori da una cache locale. L'abilitazione di questa modalità impedisce che l'accesso iniziale ai file sia più lento a causa del richiamo su richiesta e consente al server di richiamare in modo proattivo i file non appena sono stati modificati o creati nella condivisione file di Azure.

> [!IMPORTANT]
> È importante tenere presente che il rilevamento delle modifiche nella condivisione file di Azure che si trova nel server può aumentare il traffico in uscita e la fattura da Azure. Se i file richiamati nel server non sono effettivamente necessari in locale, il richiamo non necessario al server può avere conseguenze negative. Usare questa modalità quando si sa che la pre-compilazione della cache in un server con modifiche recenti nel cloud avrà un effetto positivo sugli utenti o sulle applicazioni che usano i file in tale server.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Abilitare un endpoint server per richiamare in modo proattivo le modifiche in una condivisione file di Azure

# <a name="portal"></a>[Portale](#tab/proactive-portal)

1. Nel [portale di Azure](https://portal.azure.com/)passare al servizio di sincronizzazione archiviazione, selezionare il gruppo di sincronizzazione corretto e quindi identificare l'endpoint server per cui si vuole tenere traccia delle modifiche nella condivisione file di Azure (endpoint cloud).
1. Nella sezione cloud tiering trovare l'argomento "Download della condivisione file di Azure". Verrà visualizzata la modalità attualmente selezionata e sarà possibile modificarla per tenere traccia delle modifiche della condivisione file di Azure più da vicino e richiamarle in modo proattivo nel server.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Immagine che mostra il comportamento di download della condivisione file di Azure per un endpoint server attualmente attivo e un pulsante per aprire un menu che consente di modificarlo.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

È possibile modificare le proprietà dell'endpoint server in PowerShell tramite il cmdlet [Set-AzStorageSyncServerEndpoint.](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint)

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure
Per eseguire la migrazione di una distribuzione di DFS-R in Sincronizzazione file di Azure:

1. Creare un gruppo di sincronizzazione per rappresentare la topologia di DFS-R che si sta sostituendo.
1. Avviare il server contenente il set completo di dati in una topologia DFS-R di cui eseguire la migrazione. Installare Sincronizzazione file di Azure su tale server.
1. Registrare il server e creare un endpoint server per il primo server di cui eseguire la migrazione. Non abilitare la funzionalità Suddivisione in livelli cloud.
1. Consentire la sincronizzazione di tutti i dati in Condivisione file di Azure (endpoint cloud).
1. Installare e registrare l'agente Sincronizzazione file di Azure in ogni server DFS-R rimanente.
1. Disabilitare DFS-R. 
1. Creare un endpoint server in ogni server DFS-R. Non abilitare la funzionalità Suddivisione in livelli cloud.
1. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze.
1. Disattivare DFS-R.
1. A questo punto, è possibile abilitare la funzionalità Suddivisione in livelli cloud in qualsiasi endpoint server in base alle esigenze.

Per altre informazioni, vedere [Interoperabilità di Sincronizzazione file di Azure con il file system distribuito](file-sync-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere e rimuovere un endpoint server di Sincronizzazione file di Azure](file-sync-server-endpoint.md)
- [Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure](file-sync-server-registration.md)
- [Monitorare Sincronizzazione file di Azure](file-sync-monitoring.md)