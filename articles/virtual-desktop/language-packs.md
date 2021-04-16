---
title: Installare Language Pack nelle Windows 10 virtuali in Desktop virtuale Windows - Azure
description: Come installare i Language Pack per Windows 10 macchine virtuali multi-sessione in Desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db1ac3f5de507a5cfdbfec7216afea9a0f4ac541
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515040"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Aggiungere Language Pack a un'Windows 10 di più sessioni

Desktop virtuale Windows è un servizio che gli utenti possono distribuire in qualsiasi momento e ovunque. Ecco perché è importante che gli utenti siano in grado di personalizzare la lingua in cui viene Windows 10 Enterprise'immagine multi-sessione.

Esistono due modi per soddisfare le esigenze linguistiche degli utenti:

- Creare pool di host dedicati con un'immagine personalizzata per ogni lingua.
- Avere utenti con requisiti di lingua e localizzazione diversi nello stesso pool di host, ma personalizzare le immagini per assicurarsi di poter selezionare la lingua necessaria.

Quest'ultimo metodo è molto più efficiente ed economicamente conveniente. Tuttavia, è l'utente a decidere quale metodo è più adatto alle proprie esigenze. Questo articolo illustra come personalizzare le lingue per le immagini.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti per personalizzare le Windows 10 Enterprise di più sessioni per aggiungere più lingue:

- Una macchina virtuale di Azure con Windows 10 Enterprise multi-sessione, versione 1903 o successiva

- L'ISO del linguaggio, il disco su richiesta 1 e l'ISO delle app della posta in arrivo della versione del sistema operativo utilizzata dall'immagine. Puoi scaricarle da qui: 
     
     - ISO del linguaggio:
        - [Windows 10, versione 1903 o 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versione 2004 o 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD Disk 1 ISO:
        - [Windows 10, versione 1903 o 1909 SUD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, versione 2004 o 20H2 SUD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Inbox Apps ISO (ISO app posta in arrivo):
        - [Windows 10, versione 1903 o 1909 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, versione 2004 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, versione 20H2 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Se si usano file ISO di Pacchetto di esperienze locali (LXP) per localizzare le immagini, è anche necessario scaricare l'ISO LXP appropriato per un'esperienza linguistica ottimale
        - Se si usa la Windows 10, versione 1903 o 1909:
          - [Windows 10, versione 1903 o 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Se si usa Windows 10 versione 2004 o 20H2, usare le informazioni in Aggiunta di lingue [in Windows 10:](/windows-hardware/manufacture/desktop/language-packs-known-issue) Problemi noti per determinare quale degli ISO LXP seguenti è più utile:
          - [Windows 10, versione 2004 o 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, versione 2004 o 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, versione 2004 o 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, versione 2004 o 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, versione 2004 o 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, versione 2004 o 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [Windows 10, versione 2004 o 20H2 **3C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2103C.iso)

- Una File di Azure o una condivisione file in una macchina virtuale file server Windows

>[!NOTE]
>La condivisione file (repository) deve essere accessibile dalla macchina virtuale di Azure che si intende usare per creare l'immagine personalizzata.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Creare un repository di contenuto per i pacchetti e le funzionalità del linguaggio su richiesta

Per creare il repository del contenuto per i pacchetti di linguaggio e le applicazioni a pagina mobile e un repository per i pacchetti delle app per la posta in arrivo:

1. In una macchina virtuale di Azure scaricare le immagini isO Windows 10 Multi-Language, FOD e Inbox Apps for Windows 10 Enterprise multi-session, version 1903/1909 e 2004 dai collegamenti in [Prerequisiti](#prerequisites).

2. Aprire e montare i file ISO nella macchina virtuale.

3. Passare alla language pack ISO e copiare il contenuto dalle cartelle **LocalExperiencePacks** e **x64 \\ langpacks,** quindi incollare il contenuto nella condivisione file.

4. Passare al **file ISO SUD,** copiarne tutto il contenuto e quindi incollarlo nella condivisione file.
5. Passare alla cartella **amd64fre** in Inbox Apps ISO (ISO app posta in arrivo) e copiare il contenuto nel repository per le app della posta in arrivo preparate.

     >[!NOTE]
     > Se si lavora con risorse di archiviazione limitate, copiare solo i file per le lingue necessarie agli utenti. È possibile distinguerli esaminando i codici di lingua nei nomi file. Ad esempio, il file francese ha il codice "fr-FR" nel nome. Per un elenco completo dei codici lingua per tutte le lingue disponibili, vedere [Language Pack disponibili per Windows.](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)

     >[!IMPORTANT]
     > Alcune lingue richiedono tipi di carattere aggiuntivi inclusi nei pacchetti satellite che seguono convenzioni di denominazione diverse. Ad esempio, i nomi dei file dei tipi di carattere giapponesi includono "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Esempio dei Language Pack giapponesi con il tag di lingua "Jpan" nei nomi file.](media/language-pack-example.png)

6. Impostare le autorizzazioni per la condivisione del repository del contenuto della lingua in modo da avere accesso in lettura dalla macchina virtuale che verrà utilizzata per compilare l'immagine personalizzata.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Creare manualmente un'Windows 10 Enterprise personalizzata con più sessioni

Per creare manualmente un'Windows 10 Enterprise personalizzata con più sessioni:

1. Distribuire una macchina virtuale di Azure, quindi passare alla raccolta di Azure e selezionare la versione corrente Windows 10 Enterprise più sessioni in uso.
2. Dopo aver distribuito la macchina virtuale, connettersi a essa usando RDP come amministratore locale.
3. Assicurarsi che la macchina virtuale abbia tutti gli aggiornamenti di Windows più recenti. Scaricare gli aggiornamenti e riavviare la macchina virtuale, se necessario.
4. Connettersi al repository di condivisione file language package, FOD e Inbox Apps e montarlo in un'unità lettera (ad esempio, l'unità E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Creare automaticamente un'Windows 10 Enterprise personalizzata con più sessioni

Se si preferisce installare le lingue tramite un processo automatizzato, è possibile configurare uno script in PowerShell. È possibile usare l'esempio di script seguente per installare i Language Pack e i pacchetti satellite in spagnolo (Spagna), francese (Francia) e cinese (RPC) per Windows 10 Enterprise più sessioni, versione 2004. Lo script integra il Language Interface Pack e tutti i pacchetti satellite necessari nell'immagine. Tuttavia, è anche possibile modificare questo script per installare altre lingue. È sufficiente assicurarsi di eseguire lo script da una sessione di PowerShell con privilegi elevati. In caso contrario, non funzionerà.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Lo script potrebbe richiedere del tempo a seconda del numero di lingue che è necessario installare.

Al termine dell'esecuzione dello script, verificare che i Language Pack siano installati correttamente selezionando Start Settings Time (Ora impostazioni di avvio) & Language Language Language  >    >  **(Lingua).**  >   Se i file di lingua sono presenti, è tutto impostato.

Dopo aver aggiunto altre lingue all'immagine Windows, è necessario aggiornare anche le app della posta in arrivo per supportare le lingue aggiunte. Questa operazione può essere eseguita aggiornando le app preinstallato con il contenuto dell'ISO delle app della posta in arrivo. Per eseguire questo aggiornamento in un ambiente disconnesso (non è possibile accedere a Internet dalla macchina virtuale), è possibile usare l'esempio di script di PowerShell seguente per automatizzare il processo.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>Le app della posta in arrivo incluse in ISO non sono le versioni più recenti delle app di Windows preinstallato. Per ottenere la versione più recente di tutte le app, è necessario aggiornare le app usando l'app di Windows Store ed eseguire una ricerca manuale degli aggiornamenti dopo aver installato le lingue aggiuntive.

Al termine, assicurarsi di disconnettere la condivisione.

## <a name="finish-customizing-your-image"></a>Completare la personalizzazione dell'immagine

Dopo aver installato i Language Pack, è possibile installare qualsiasi altro software da aggiungere all'immagine personalizzata.

Dopo aver completato la personalizzazione dell'immagine, è necessario eseguire lo strumento di preparazione del sistema (sysprep).

Per eseguire sysprep:

1. Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente per generalizzare l'immagine:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Arrestare la macchina virtuale e quindi acquisire la macchina virtuale in un'immagine gestita seguendo le istruzioni riportate in Creare un'immagine gestita di una macchina virtuale [generalizzata in Azure](../virtual-machines/windows/capture-image-resource.md).

3. È ora possibile usare l'immagine personalizzata per distribuire un pool di host di Desktop virtuale Windows. Per informazioni su come distribuire un pool di host, vedere [Esercitazione: Creare un pool di host con](create-host-pools-azure-marketplace.md)portale di Azure .

## <a name="enable-languages-in-windows-settings-app"></a>Abilitare le lingue nell'app impostazioni di Windows

Infine, dopo aver distribuito il pool di host, è necessario aggiungere la lingua all'elenco delle lingue di ogni utente in modo che possa selezionare la lingua preferita nel menu Impostazioni.

Per assicurarsi che gli utenti possano selezionare le lingue installate, accedere come utente, quindi eseguire il cmdlet di PowerShell seguente per aggiungere i Language Pack installati al menu Lingue. È anche possibile configurare questo script come un'attività automatizzata o uno script di accesso che si attiva quando l'utente accede alla sessione.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Dopo che un utente ha modificato le impostazioni della lingua, dovrà disconnettersi dalla sessione di Desktop virtuale Windows e accedere di nuovo per fare in modo che le modifiche siano effettive. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui problemi noti relativi ai Language Pack, vedere Aggiunta di Language Pack in Windows 10, versione [1803](/windows-hardware/manufacture/desktop/language-packs-known-issue)e versioni successive: Problemi noti .

Per altre domande sull'Windows 10 Enterprise multi-sessione, vedere le domande [frequenti.](windows-10-multisession-faq.yml)
