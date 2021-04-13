---
title: Desktop virtuale Windows montaggio app MSIX PowerShell - Azure
description: Come configurare l'montaggio app MSIX desktop virtuale Windows con PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f44cbf3764063c511c896f11bb7ebfaae2973f0c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365400"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Configurare le montaggio app MSIX con PowerShell

Oltre al portale di Azure, è anche possibile configurare le montaggio app MSIX manualmente con PowerShell. Questo articolo illustra come usare PowerShell per configurare montaggio app MSIX.

## <a name="requirements"></a>Requisiti

>[!IMPORTANT]
>Prima di iniziare, assicurarsi di compilare e inviare [questo modulo](https://aka.ms/enablemsixappattach) per abilitare montaggio app MSIX nella sottoscrizione. Se non si ha una richiesta approvata, montaggio app MSIX non funzionerà. L'approvazione delle richieste può richiedere fino a 24 ore durante i giorni lavorativi. Quando la richiesta è stata accettata e completata, si otterrà un messaggio di posta elettronica.

Ecco cosa è necessario per configurare montaggio app MSIX:

- Una distribuzione di Desktop virtuale Windows funzionante. Per informazioni su come distribuire Desktop virtuale Windows (versione classica), vedere [Creare un tenant in Desktop virtuale Windows.](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) Per informazioni su come distribuire Desktop virtuale Windows con Azure Resource Manager, vedere [Creare un pool di host](./create-host-pools-azure-marketplace.md)con il portale di Azure .
- Un pool di host di Desktop virtuale Windows con almeno un host sessione attivo.
- Questo pool di host deve essere nell'ambiente di convalida.
- Un gruppo di app desktop remoto.
- MSIX Packaging Tool.
- Un'applicazione in pacchetto MSIX espansa in un'immagine MSIX che viene caricata in una condivisione file.
- Una condivisione file nella distribuzione di Desktop virtuale Windows in cui verrà archiviato il pacchetto MSIX.
- La condivisione file in cui è stata caricata l'immagine MSIX deve essere accessibile anche a tutte le macchine virtuali (VM) nel pool di host. Gli utenti dovranno disporre delle autorizzazioni di sola lettura per accedere all'immagine.
- Scaricare e installare PowerShell Core.
- Scaricare l'anteprima pubblica Azure PowerShell modulo ed espanderlo in una cartella locale.
- Installare il modulo di Azure eseguendo il cmdlet seguente:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Accedere ad Azure e importare il modulo

Dopo aver pronto tutti i requisiti, aprire PowerShell Core in un prompt dei comandi con privilegi elevati ed eseguire questo cmdlet:

```powershell
Connect-AzAccount
```

Dopo l'esecuzione, autenticare l'account usando le credenziali. In questo caso, potrebbe essere richiesto un URL del dispositivo o un token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importare il modulo Az.WindowsVirtualDesktop

Per seguire le istruzioni di questo articolo, è necessario il modulo Az.DesktopVirtualization.

>[!NOTE]
>Per l'anteprima pubblica, il modulo verrà fornito come file ZIP separati che è necessario importare manualmente.

Prima di iniziare, è possibile eseguire il cmdlet seguente per verificare se il modulo Az.DesktopVirtualization è già installato nella sessione o nella macchina virtuale:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Se si desidera disinstallare una copia esistente del modulo e ricominciare, eseguire questo cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Se il modulo è bloccato nella macchina virtuale, eseguire questo cmdlet per sbloccarlo:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Con questa pulizia, è possibile importare il modulo.

1. Eseguire il cmdlet seguente, quindi premere **il tasto R** quando viene richiesto di accettare l'esecuzione del codice personalizzato.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Dopo aver eseguito il cmdlet di importazione, verificare se contiene i cmdlet per MSIX eseguendo il cmdlet seguente:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Se i cmdlet sono presenti, l'output sarà simile al seguente:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Se l'output non viene visualizzato, chiudere tutte le sessioni di PowerShell PowerShell Core e riprovare.

## <a name="set-up-helper-variables"></a>Configurare le variabili helper

Dopo aver importato il modulo, è necessario configurare le variabili helper. Gli esempi seguenti illustrano come eseguire ogni operazione.

Per ottenere l'ID sottoscrizione:

```powershell
Get-AzContext -ListAvailable | fl
```

Per selezionare il contesto di un tenant e di una sottoscrizione di Azure con un nome:

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

Per impostare il nome del pool di host:

```powershell
$hp = "<HostPoolName>"
```

Per configurare il gruppo di risorse in cui sono configurate le macchine virtuali host della sessione:

```powershell
$rg = "<ResourceGroupName>"
```

Infine, per verificare di aver impostato correttamente tutte le variabili:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Aggiungere un pacchetto MSIX a un pool di host

Dopo aver configurato tutti gli elementi, è possibile aggiungere il pacchetto MSIX a un pool di host. A tale scopo, è prima necessario ottenere il percorso UNC dell'immagine MSIX.

Usando il percorso UNC, eseguire questo cmdlet per espandere l'immagine MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Eseguire questo cmdlet per aggiungere il pacchetto MSIX al pool di host desiderato:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Al termine, verificare che il pacchetto sia stato creato con questo cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Rimuovere un pacchetto MSIX da un pool di host

Per rimuovere un pacchetto da un pool di host:

Ottenere un elenco di tutti i pacchetti associati a un pool di host con questo cmdlet, quindi trovare il nome del pacchetto da rimuovere nell'output:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

In alternativa, è anche possibile ottenere un pacchetto specifico in base al nome visualizzato con questo cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Per rimuovere il pacchetto, eseguire questo cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Pubblicare app MSIX in un gruppo di app

È possibile seguire le istruzioni riportate in questa sezione solo se si sono completate le istruzioni nelle sezioni precedenti. Se si ha un pool di host con un host sessione attivo, almeno un gruppo di app desktop e si è aggiunto un pacchetto MSIX al pool host, si è pronti per iniziare.

Per pubblicare un'app dal pacchetto MSIX in un gruppo di app, è necessario individuarne il nome e quindi usare tale nome nel cmdlet di pubblicazione.

Per pubblicare un'app:

Eseguire questo cmdlet per elencare tutti i gruppi di app disponibili:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Dopo aver trovato il nome del gruppo di app in cui si vogliono pubblicare le app, usarne il nome in questo cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Infine, è necessario pubblicare l'app.

- Per pubblicare un'applicazione MSIX in un gruppo di app desktop, eseguire questo cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Per pubblicare l'app in un gruppo di app remote, eseguire invece questo cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Se un utente viene assegnato sia a un gruppo di app remote che a un gruppo di app desktop nello stesso pool di host, quando l'utente si connette al desktop remoto, visualizza le app MSIX di entrambi i gruppi.

## <a name="next-steps"></a>Passaggi successivi

Porre domande alla community su questa funzionalità in [TechCommunity di Desktop virtuale Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Ecco alcuni altri articoli che possono risultare utili:

- [montaggio app MSIX glossario](app-attach-glossary.md)
- [montaggio app MSIX domande frequenti](app-attach-faq.md)
