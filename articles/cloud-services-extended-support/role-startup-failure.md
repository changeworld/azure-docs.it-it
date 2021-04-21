---
title: Errore di avvio dell'istanza del ruolo Servizi cloud di Azure (supporto esteso)
description: Risolvere l'errore di avvio dell'istanza del ruolo Servizi cloud di Azure (supporto esteso).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748970"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Risolvere i Servizi cloud di Azure (supporto esteso) che non possono essere avviati

Di seguito sono riportati alcuni problemi comuni e soluzioni correlati Servizi cloud di Azure (supporto esteso) che non possono essere avviati.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>L'operazione del servizio cloud non riesce con RoleInstanceStartupTimeoutError

L'avvio di una o più istanze del ruolo in Servizi cloud di Azure (supporto esteso) potrebbe essere lento o potrebbe essere in corso il riciclo e l'istanza del ruolo ha esito negativo. Viene visualizzato l'errore dell'applicazione `RoleInstanceStartupTimeoutError` del ruolo.

L'applicazione del ruolo contiene due parti che potrebbero causare il riciclo del ruolo: *attività di* avvio e codice del *ruolo (implementazione di RoleEntryPoint).* 

Se il ruolo viene arrestato, l'platform as a service (PaaS) riavvia il ruolo.

È possibile ottenere lo stato corrente e i dettagli per un'istanza del ruolo per diagnosticare gli errori usando PowerShell o il portale di Azure:

* **PowerShell:** usare il cmdlet [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) per ottenere informazioni sullo stato di runtime dell'istanza del ruolo:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **portale di Azure:** nel portale passare all'istanza del servizio cloud. Per visualizzare i dettagli dello stato, selezionare **Ruoli e istanze** e quindi selezionare l'istanza del ruolo.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Screenshot che mostra un errore di avvio del ruolo nel portale di Azure.":::

## <a name="missing-dlls-or-dependencies"></a>File DLL o dipendenze mancanti

I ruoli che non rispondono e i ruoli che esere ciclici tra stati possono essere causati da DLL o assembly mancanti.

Di seguito sono riportati alcuni sintomi di DLL o assembly mancanti:

* L'istanza del ruolo scorre gli stati **Inizializzazione**, **Occupato** e **Arresto .**
* L'istanza del ruolo è **passata** allo stato Pronto, ma se si passa all'applicazione Web, la pagina non è visibile.


Un sito Web distribuito in un ruolo Web in cui manca una DLL potrebbe visualizzare questo errore di runtime del server:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Screenshot che mostra un errore di runtime dopo un errore di avvio del ruolo.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Risolvere dll e assembly mancanti

Per risolvere gli errori di ASSEMBLY e DLL mancanti:

1. In Visual Studio aprire la soluzione.
2. In Esplora soluzioni aprire la *cartella Riferimenti.*
3. Selezionare l'assembly identificato nell'errore.
4. In **Proprietà** impostare la **proprietà Copia** localmente su **True.**
5. Ridistribuire il servizio cloud.

Dopo aver verificato che gli errori non vengano più visualizzati, ridistribuire il servizio. Quando si configura la distribuzione, non selezionare la casella di controllo Abilita IntelliTrace per i ruoli **.NET 4.**

## <a name="diagnose-role-instance-errors"></a>Diagnosticare gli errori delle istanze del ruolo

Scegliere una delle opzioni seguenti per diagnosticare i problemi con le istanze del ruolo.

### <a name="turn-off-custom-errors"></a>Disattivare gli errori personalizzati

Per visualizzare informazioni complete sugli errori, nel file *web.config* per il ruolo Web impostare la modalità di errore personalizzata su `off` , quindi ridistribuire il servizio:

1. In Visual Studio aprire la soluzione.
2. In Esplora soluzioni aprire il file *web.config.*
3. Nella sezione `system.web` aggiungere il codice seguente:

   ```xml
   <customErrors mode="Off" />
   ```

4. Salvare il file.
5. Ricreare il pacchetto e ridistribuire il servizio.

Quando il servizio viene ridistribuito, un messaggio di errore include il nome di assembly o DLL mancanti.

### <a name="use-remote-desktop"></a>Usare Desktop remoto

Usare Desktop remoto per accedere al ruolo e visualizzare informazioni complete sull'errore:

1. [Aggiungere l'Desktop remoto per Servizi cloud di Azure (supporto esteso).](enable-rdp.md)
2. Nel portale di Azure, quando l'istanza del servizio  cloud mostra lo stato Pronto, usare Desktop remoto per accedere al servizio cloud. Per altre informazioni, vedere [Connettersi a istanze del ruolo usando Desktop remoto](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Accedere alla macchina virtuale usando le credenziali usate per configurare Desktop remoto.
4. Aprire una finestra del prompt dei comandi.
5. Al prompt dei comandi immettere **ipconfig**. Si noti il valore restituito per l'indirizzo IPv4.
6. Aprire Microsoft Internet Explorer.
7. Nella barra degli indirizzi immettere l'indirizzo IPv4 seguito da una barra e dal nome del file predefinito dell'applicazione Web. Ad esempio: `http://<IPv4 address>/default.aspx`.

Se si passa ora al sito Web, verranno visualizzati messaggi di errore contenenti altre informazioni. Ecco un esempio:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Screenshot che mostra un esempio di messaggio di errore.":::
  
### <a name="use-the-compute-emulator"></a>Usare l'emulatore di calcolo

È possibile usare l'emulatore di calcolo di Azure per diagnosticare e risolvere i problemi di dipendenze mancanti *web.config* errori. Quando si usa questo metodo per diagnosticare i problemi, per ottenere risultati ottimali, usare un computer o una macchina virtuale con un'installazione pulita di Windows.

Per diagnosticare i problemi usando l'emulatore di calcolo di Azure:

1. Installare [Azure SDK.](https://azure.microsoft.com/downloads/)
2. Nel computer di sviluppo compilare il progetto di servizio cloud.
3. Nel Esplora file del servizio cloud passare alla *cartella bin\debug.*
4. Copiare *la cartella con estensione csx* e il file con estensione *cscfg* nel computer in uso per eseguire il debug dei problemi.
5. Nel computer pulito aprire una finestra del prompt dei comandi di Azure SDK.
6. Al prompt dei comandi immettere **csrun.exe /devstore:start**.
7. Immettere quindi **run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**.
8. All'avvio del ruolo, Internet Explorer informazioni dettagliate sull'errore.

Se è necessaria una diagnosi maggiore, è possibile usare gli strumenti di risoluzione dei problemi standard di Windows.

### <a name="use-intellitrace"></a>Usare IntelliTrace

Per i ruoli di lavoro e Web che usano .NET Framework 4, è possibile usare [IntelliTrace.](/visualstudio/debugger/intellitrace) IntelliTrace è disponibile in Visual Studio Enterprise.

Per distribuire il servizio cloud con IntelliTrace attivato:

1. Verificare che sia installato Azure SDK 1.3 o versione successiva.
2. In Visual Studio distribuire la soluzione. Quando si configura la distribuzione, selezionare la casella di controllo Abilita IntelliTrace per i ruoli **di .NET 4.**
3. Dopo l'avvio dell'istanza del ruolo, aprire Esplora server.
4. Espandere il **nodo Azure\Servizi** cloud.
5. Espandere la distribuzione per elencare le istanze del ruolo. Fare clic con il pulsante destro del mouse su un'istanza del ruolo.
6. Selezionare **Visualizza log IntelliTrace**.
7. In **Riepilogo IntelliTrace** passare a  **Dati eccezione**.
8. Espandere **Dati eccezione** e cercare un `System.IO.FileNotFoundException` errore:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Screenshot dei dati delle eccezioni per un errore di avvio del ruolo." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come risolvere [i problemi del ruolo del servizio cloud usando i dati di diagnostica del computer PaaS di Azure.](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)
