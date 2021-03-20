---
title: Windows Virtual Desktop MSIX app di connessione anteprima PowerShell-Azure
description: Come configurare la connessione all'app MSIX per desktop virtuale Windows con PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1196982fedc7321805e36cceed27c90e43a6e705
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99558338"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>Configurare la connessione all'app MSIX (anteprima) con PowerShell

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Oltre alla portale di Azure, è anche possibile configurare manualmente l'associazione dell'app MSIX (anteprima) con PowerShell. Questo articolo illustra come usare PowerShell per configurare la connessione all'app MSIX.

## <a name="requirements"></a>Requisiti

>[!IMPORTANT]
>Prima di iniziare, assicurarsi di compilare e inviare [il modulo](https://aka.ms/enablemsixappattach) per abilitare la connessione dell'app MSIX nella sottoscrizione. Se non si ha una richiesta approvata, la connessione all'app MSIX non funzionerà. L'approvazione delle richieste può richiedere fino a 24 ore durante i giorni lavorativi. Quando la richiesta è stata accettata e completata, si riceverà un messaggio di posta elettronica.

Ecco cosa occorre per configurare la connessione all'app MSIX:

- Una distribuzione di Desktop virtuale Windows funzionante. Per informazioni su come distribuire desktop virtuale di Windows (versione classica), vedere [creare un tenant in un desktop virtuale di Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Per informazioni su come distribuire desktop virtuale di Windows con l'integrazione di Azure Resource Manager, vedere [creare un pool host con l'portale di Azure](./create-host-pools-azure-marketplace.md).
- Un pool host di desktop virtuali Windows con almeno un host sessione attiva.
- Questo pool host deve trovarsi nell'ambiente di convalida.
- Un gruppo di app Remote Desktop.
- Strumento per la creazione di pacchetti MSIX.
- Un'applicazione in pacchetto MSIX è stata espansa in un'immagine MSIX che viene caricata in una condivisione file.
- Una condivisione file nella distribuzione di desktop virtuale di Windows in cui verrà archiviato il pacchetto MSIX.
- La condivisione file in cui è stata caricata l'immagine di MSIX deve essere accessibile anche a tutte le macchine virtuali (VM) nel pool host. Per accedere all'immagine, gli utenti dovranno disporre di autorizzazioni di sola lettura.
- Scaricare e installare PowerShell core.
- Scaricare il modulo di anteprima pubblica Azure PowerShell ed espanderlo in una cartella locale.
- Installare il modulo Azure eseguendo il cmdlet seguente:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Accedere ad Azure e importare il modulo

Quando tutti i requisiti sono pronti, aprire PowerShell core in un prompt dei comandi con privilegi elevati ed eseguire questo cmdlet:

```powershell
Connect-AzAccount
```

Al termine dell'esecuzione, autenticare l'account usando le proprie credenziali. In questo caso, è possibile che venga richiesto un URL o un token del dispositivo.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importare il modulo AZ. WindowsVirtualDesktop

Per seguire le istruzioni riportate in questo articolo, è necessario il modulo AZ. DesktopVirtualization.

>[!NOTE]
>Per l'anteprima pubblica, si fornirà il modulo come file ZIP distinti che è necessario importare manualmente.

Prima di iniziare, è possibile eseguire il cmdlet seguente per verificare se il modulo AZ. DesktopVirtualization è già installato nella sessione o nella macchina virtuale:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Se si desidera disinstallare una copia esistente del modulo e ricominciare, eseguire il cmdlet seguente:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Se il modulo è bloccato nella macchina virtuale, eseguire questo cmdlet per sbloccarlo:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Con questo tipo di pulizia, è il momento di importare il modulo.

1. Eseguire il cmdlet seguente, quindi premere il tasto **R** quando viene richiesto di accettare l'esecuzione del codice personalizzato.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Dopo aver eseguito il cmdlet Import, verificare se sono presenti i cmdlet per MSIX eseguendo il cmdlet seguente:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Se i cmdlet sono presenti, l'output dovrebbe essere simile al seguente:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Se questo output non viene visualizzato, chiudere tutte le sessioni PowerShell e PowerShell core e riprovare.

## <a name="set-up-helper-variables"></a>Impostare le variabili Helper

Dopo aver importato il modulo, sarà necessario configurare le variabili helper. Negli esempi seguenti viene illustrato come eseguire ognuna di esse.

Per ottenere l'ID sottoscrizione:

```powershell
Get-AzContext -ListAvailable | fl
```

Per selezionare il contesto di un tenant di Azure e di una sottoscrizione con un nome:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Per impostare la variabile di sottoscrizione:

```powershell
$subId = $obj.Subscription.Id
```

Per impostare il nome dell'area di lavoro:

```powershell
$ws = "<WorksSpaceName>"
```

Per impostare il nome del pool host:

```powershell
$hp = "<HostPoolName>"
```

Per configurare il gruppo di risorse in cui sono configurate le VM host della sessione:

```powershell
$rg = "<ResourceGroupName>"
```

Infine, per verificare di aver impostato correttamente tutte le variabili:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Aggiungere un pacchetto MSIX a un pool host

Dopo aver configurato tutti gli elementi, è possibile aggiungere il pacchetto MSIX a un pool di host. A tale scopo, è necessario innanzitutto ottenere il percorso UNC per l'immagine MSIX.

Utilizzando il percorso UNC, eseguire questo cmdlet per espandere l'immagine MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Eseguire questo cmdlet per aggiungere il pacchetto MSIX al pool host desiderato:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Al termine, confermare che il pacchetto è stato creato con questo cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Rimuovere un pacchetto MSIX da un pool host

Per rimuovere un pacchetto da un pool host:

Ottenere un elenco di tutti i pacchetti associati a un pool di host con questo cmdlet, quindi trovare il nome del pacchetto che si desidera rimuovere nell'output:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

In alternativa, è anche possibile ottenere un particolare pacchetto in base al nome visualizzato con questo cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Per rimuovere il pacchetto, eseguire il cmdlet seguente:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Pubblicare app MSIX in un gruppo di app

È possibile seguire le istruzioni riportate in questa sezione solo se si è terminato di seguire le istruzioni riportate nelle sezioni precedenti. Se si dispone di un pool di host con un host sessione attivo, almeno un gruppo di app desktop e è stato aggiunto un pacchetto MSIX al pool di host, è possibile iniziare.

Per pubblicare un'app dal pacchetto MSIX a un gruppo di app, è necessario individuarne il nome e quindi usare tale nome nel cmdlet di pubblicazione.

Per pubblicare un'app:

Eseguire questo cmdlet per elencare tutti i gruppi di app disponibili:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Quando è stato trovato il nome del gruppo di app in cui si vuole pubblicare le app, usare il nome in questo cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Infine, sarà necessario pubblicare l'app.

- Per pubblicare l'applicazione MSIX in un gruppo di app desktop, eseguire questo cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Per pubblicare l'app in un gruppo di app remoto, eseguire invece questo cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Se un utente è assegnato a un gruppo di app Remote e a un gruppo di app desktop nello stesso pool host, quando l'utente si connette al desktop remoto, visualizzerà le app MSIX da entrambi i gruppi.

## <a name="next-steps"></a>Passaggi successivi

Porre le domande della community su questa funzionalità nel [desktop virtuale di Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Di seguito sono riportati alcuni altri articoli che possono risultare utili:

- [Glossario per il fissaggio dell'app MSIX](app-attach-glossary.md)
- [Domande frequenti sull'aggiunta di app MSIX](app-attach-faq.md)
