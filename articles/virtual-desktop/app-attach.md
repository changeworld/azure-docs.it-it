---
title: Configurare gli script di PowerShell per la connessione di desktop virtuale Windows MSIX-Azure
description: Come creare script di PowerShell per la connessione all'app MSIX per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 224f2e773ecd42dcbdd356531d9ce94636de002f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448271"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Creare script di PowerShell per la connessione all'app MSIX (anteprima)

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo argomento descrive come configurare gli script di PowerShell per la connessione all'app MSIX.

>[!IMPORTANT]
>Prima di iniziare, assicurarsi di compilare e inviare [il modulo](https://aka.ms/enablemsixappattach) per abilitare la connessione dell'app MSIX nella sottoscrizione. Se non si ha una richiesta approvata, la connessione all'app MSIX non funzionerà. L'approvazione delle richieste può richiedere fino a 24 ore durante i giorni lavorativi. Quando la richiesta è stata accettata e completata, si riceverà un messaggio di posta elettronica.

## <a name="install-certificates"></a>Installare i certificati

È necessario installare i certificati in tutti gli host di sessione nel pool host che ospiteranno le app dai pacchetti di associazione dell'app MSIX.

Se l'app usa un certificato che non è pubblico o è autofirmato, di seguito viene illustrato come installarlo:

1. Fare clic con il pulsante destro del mouse sul pacchetto e scegliere **Proprietà**.
2. Nella finestra visualizzata, selezionare la scheda **Firme digitali**. Nella scheda deve essere presente un solo elemento nell'elenco, come illustrato nella figura seguente. Selezionare l'elemento per evidenziarlo, quindi selezionare **Dettagli**.
3. Quando viene visualizzata la finestra Dettagli firma digitale, selezionare la scheda **generale** , selezionare **Visualizza certificato**, quindi selezionare **Installa certificato**.
4. Quando si apre il programma di installazione, selezionare **Computer locale** come percorso di archiviazione, quindi selezionare **Avanti**.
5. Se il programma di installazione chiede se consentire all'app di apportare modifiche al dispositivo, selezionare **Sì**.
6. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi selezionare **Sfoglia**.
7. Quando viene visualizzata la finestra Seleziona archivio certificati, selezionare **Persone attendibili**, quindi selezionare **OK**.
8. Selezionare **Avanti** e **fine**.

## <a name="enable-microsoft-hyper-v"></a>Abilita Microsoft Hyper-V

È necessario abilitare Microsoft Hyper-V perché il `Mount-VHD` comando è necessario per eseguire il staging ed `Dismount-VHD` è necessario per eseguire la fase di destaging.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Questa modifica richiede il riavvio della macchina virtuale.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparare uno script di PowerShell per la connessione all'app MSIX

La connessione all'app MSIX prevede quattro fasi distinte che devono essere eseguite nell'ordine seguente:

1. Fase
2. Register
3. Annullamento registrazione
4. Rimozione gestione temporanea

Ogni fase crea uno script di PowerShell. Gli script di esempio per ogni fase sono disponibili [qui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Script di PowerShell per stage

Prima di aggiornare gli script di PowerShell, assicurarsi di disporre del GUID del volume nel disco rigido virtuale. Per ottenere il GUID del volume:

1.  Aprire la condivisione di rete in cui si trova il disco rigido virtuale all'interno della macchina virtuale in cui verrà eseguito lo script.

2.  Fare clic con il pulsante destro del mouse sul disco rigido virtuale e scegliere **Monta**. Il disco rigido virtuale verrà montato in una lettera di unità.

3.  Dopo aver montato il disco rigido virtuale, verrà visualizzata la finestra di **Esplora file**. Acquisire la cartella padre e aggiornare la variabile **$parentFolder**

    >[!NOTE]
    >Se non viene visualizzata alcuna cartella padre, significa che MSIX non è stato espanso correttamente. Ripetere la sezione precedente e riprovare.

4.  Aprire la cartella padre. Se espansa correttamente, verrà visualizzata una cartella con lo stesso nome del pacchetto. Aggiornare la variabile **$packageName** in modo che corrisponda al nome di questa cartella.

    Ad esempio: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Aprire un prompt dei comandi e immettere **mountvol**. Questo comando visualizzerà un elenco di volumi e i relativi GUID. Copiare il GUID del volume in cui la lettera di unità corrisponde all'unità in cui è stato montato il disco rigido virtuale nel passaggio 2.

    Ad esempio, in questo output di esempio per il comando mountvol, se il disco rigido virtuale è stato montato sull'unità C, sarà necessario copiare il precedente valore `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aggiornare la variabile **$volumeGuid** con il GUID del volume appena copiato.

7. Aprire un prompt di PowerShell come amministratore e aggiornare lo script di PowerShell seguente con le variabili applicabili all'ambiente.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrare lo script di PowerShell

Per eseguire lo script di registrazione, eseguire i cmdlet di PowerShell riportati di seguito, dopo avere sostituito i valori segnaposto con valori validi per l'ambiente in uso.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Annullare la registrazione di uno script di PowerShell

Per questo script, sostituire il segnaposto per **$packageName** con il nome del pacchetto che si sta testando.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Rimuovere la gestione temporanea di uno script di PowerShell

Per questo script, sostituire il segnaposto per **$packageName** con il nome del pacchetto che si sta testando. In una distribuzione di produzione è preferibile eseguire questa operazione alla chiusura.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurare script di simulazione per l'agente di connessione all'app MSIX

Dopo aver creato gli script, gli utenti possono eseguirli manualmente o configurarli per l'esecuzione automatica come script di avvio, di accesso, di disconnessione e di arresto. Per altre informazioni su questi tipi di script, vedere [Uso degli script di avvio, arresto, accesso e disconnessione in Criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Ognuno di questi script automatici esegue una fase degli script di associazione dell'app:

- Lo script di avvio esegue lo script di preparazione per il commit.
- Lo script di accesso esegue lo script di registrazione.
- Lo script di disconnessione esegue lo script di annullamento della registrazione.
- Lo script di arresto esegue lo script di rimozione della gestione temporanea.

## <a name="use-packages-offline"></a>Usare pacchetti offline

Se si usano pacchetti di [Microsoft Store for Business](https://businessstore.microsoft.com/) o [Microsoft Store for Education](https://educationstore.microsoft.com/) all'interno della rete o in dispositivi non connessi a Internet, sarà necessario ottenere le licenze del pacchetto da Microsoft Store e installarle sul dispositivo per eseguire correttamente l'app. Se il dispositivo è online ed è in grado di connettersi a Microsoft Store for Business, le licenze necessarie saranno scaricate automaticamente, mentre se si è offline sarà necessario configurarle manualmente.

Per installare i file di licenza, sarà necessario usare uno script di PowerShell che chiami la classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 nel provider del bridge WMI.

Di seguito viene illustrato come configurare le licenze per l'uso offline:

1. Scaricare il pacchetto dell'app, le licenze e i framework necessari da Microsoft Store for Business. Sono necessari sia il file di licenza codificato che quello non codificato. Le istruzioni dettagliate per il download sono disponibili [qui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aggiornare le variabili seguenti nello script per il passaggio 3:
      1. `$contentID` è il valore ContentID del file di licenza non codificato (.xml). Il file di licenza può essere aperto in un editor di testo di propria scelta.
      2. `$licenseBlob` è la stringa intera per il BLOB di licenza nel file di licenza codificato (.bin). Il file di licenza codificato può essere aperto in un editor di testo di propria scelta.
3. Eseguire lo script seguente da un prompt di PowerShell come amministratore. Un percorso ideale per eseguire l'installazione delle licenze è alla fine dello [script di staging](#stage-powershell-script) che deve essere eseguito anche da un prompt di amministratore.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Passaggi successivi

Questa funzionalità non è attualmente supportata, ma è possibile porre domande alla community sulla [TechCommunity di Desktop virtuale Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
