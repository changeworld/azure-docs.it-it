---
title: Errore di avvio dell'istanza del ruolo per servizi cloud di Azure (supporto esteso)
description: Risolvere gli errori di avvio dell'istanza del ruolo per servizi cloud di Azure (supporto esteso)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382356"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Risolvere i problemi dei ruoli del servizio cloud di Azure (supporto esteso) che non vengono avviati
Di seguito sono riportati alcuni problemi comuni e le soluzioni correlate ai ruoli dei servizi cloud (supporto esteso) che non si avviano.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Operazione del servizio cloud non riuscita con RoleInstanceStartupTimeoutError
È possibile che una o più istanze del ruolo del servizio (supporto esteso) non vengano avviate nel tempo previsto. Queste istanze del ruolo potrebbero richiedere tempo per l'avvio o il riciclo e l'istanza del ruolo potrebbe non riuscire con un RoleInstanceStartupTimeoutError. si tratta di un errore dell'applicazione del ruolo. L'applicazione del ruolo contiene due parti principali:' attività di avvio ' è codice ruolo (implementazione di RoleEntryPoint)', che potrebbero causare il riciclo del ruolo. Se il ruolo viene arrestato in modo anomalo, l'agente PaaS lo avvierà sempre. 

Per ottenere lo stato corrente e i dettagli delle istanze del ruolo in caso di errori, usare:

* PowerShell: usare il cmdlet [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) per recuperare informazioni sullo stato di run-time di un'istanza del ruolo in un servizio cloud.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Portale di Azure: accedere al servizio cloud e selezionare la scheda ruoli e istanze. Fare clic sull'istanza del ruolo per ottenere i dettagli sullo stato 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Immagine che mostra un errore di avvio del ruolo nel portale.":::
   
Di seguito sono riportati alcuni problemi comuni e le soluzioni correlate ai ruoli dei servizi cloud di Azure (supporto esteso) che non riescono ad avviare o che eseguono cicli tra gli Stati di inizializzazione, occupato e arresto.

## <a name="missing-dlls-or-dependencies"></a>File DLL o dipendenze mancanti
I problemi dovuti a ruoli che non rispondono e ruoli che passano in ciclo tra gli stati Inizializzazione, Occupato e Arresto possono essere provocati da file DLL o assembly mancanti.
I sintomi di file DLL o assembly mancati includono i seguenti:

* L'istanza del ruolo passa in ciclo tra gli stati **Inizializzazione**, **Occupato** e **Arresto**.
* L'istanza del ruolo è passata allo stato **Pronto** , ma, se si passa all'applicazione Web, la pagina non viene visualizzata.

Sono disponibili diversi metodi consigliati per esaminare questi problemi.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticare i problemi di DLL mancanti in un ruolo Web
Quando si passa a un sito Web distribuito in un ruolo Web e il browser visualizza un errore server simile al seguente, è possibile che indichi una DLL mancante.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Immagine che mostra un errore di runtime durante l'avvio del ruolo":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticare i problemi disabilitando gli errori personalizzati
Per visualizzare informazioni sugli errori più completi, configurare il file web.config per il ruolo Web per poter disattivare la modalità di errore personalizzata e ridistribuire il servizio.
Per visualizzare errori più completi senza usare Desktop remoto:
1.  Aprire la soluzione in Microsoft Visual Studio.
2.  In Esplora soluzioni individuare il file web.config e aprirlo.
3.  Nel file web.config individuare la sezione system.web e aggiungere la riga seguente:
 ```xml
<customErrors mode="Off" />
```
4.  Salvare il file.
5.  Ricreare il pacchetto e ridistribuire il servizio.
Quando il servizio viene ridistribuito, verrà visualizzato un messaggio di errore con il nome dell'assembly o della DLL mancante.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticare i problemi visualizzando l'errore in remoto
È possibile usare Desktop remoto per accedere al ruolo e visualizzare informazioni sugli errori più complete in remoto. Seguire questa procedura per visualizzare gli errori usando Desktop remoto:
1.  Abilitare l'estensione desktop remoto per il servizio cloud (supporto esteso). Per ulteriori informazioni, vedere [Apply desktop remoto extension to Cloud Services (Extended Support) using portale di Azure](enable-rdp.md)
2.  Nel portale di Azure, una volta che l'istanza Visualizza lo stato Ready, remote nell'istanza di. Per ulteriori informazioni sull'utilizzo di desktop remoto con servizi cloud (supporto esteso), vedere [connettersi alle istanze del ruolo con desktop remoto](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Accedere alla macchina virtuale usando le credenziali specificate durante la configurazione di Desktop remoto.
4.  Aprire una finestra di comando.
5.  Digitare IPconfig.
6.  Prendere nota del valore dell'indirizzo IPv4.
7.  Aprire Internet Explorer.
8.  Digitare l'indirizzo e il nome dell'applicazione Web, Ad esempio, http:// <IPV4 Address> /default.aspx.
Se si passa al sito Web, ora verranno restituiti messaggi di errore più espliciti:
* Errore del server nell'applicazione '/'.
* Descrizione: Eccezione non gestita durante l'esecuzione della richiesta Web corrente. Per altre informazioni sull'errore e sul suo punto di origine nel codice, vedere l'analisi dello stack.
* Dettagli dell'eccezione: System.IO.FIleNotFoundException: Impossibile caricare il file o l'assembly 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' o una delle relative dipendenze. Non è possibile trovare il file specificato.
Ad esempio:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Immagine che mostra un'eccezione durante l'errore di avvio del ruolo":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticare i problemi usando l'emulatore di calcolo
È possibile usare l'emulatore di calcolo di Azure per diagnosticare e risolvere i problemi relativi a dipendenze mancanti ed errori di web.config.
Per ottenere risultati ottimali con questo metodo di diagnosi, è consigliabile usare un computer o una macchina virtuale in cui è presente un'installazione pulita di Windows. 
1.  Installare [Azure SDK](https://azure.microsoft.com/downloads/) 
2.  Nel computer di sviluppo compilare il progetto di servizio cloud.
3.  In Esplora risorse passare alla cartella bin\debug del progetto di servizio cloud.
4.  Copiare la cartella con estensione csx e il file con estensione cscfg nel computer usato per eseguire il debug dei problemi.
5.  Nel computer pulito aprire una finestra del prompt dei comandi di Azure SDK e digitare csrun.exe/devstore: Start.
6.  Al prompt dei comandi digitare Esegui CSRun <percorso alla cartella. CSX> <percorso del file con estensione cscfg>/launchBrowser.
7.  All'avvio del ruolo, verranno visualizzate informazioni dettagliate sull'errore in Internet Explorer. È anche possibile usare gli strumenti di risoluzione dei problemi standard di Windows per diagnosticare ulteriormente il problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticare i problemi usando IntelliTrace
Per i ruoli di lavoro e i ruoli Web che fanno uso di .NET Framework 4, è possibile usare [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), disponibile in Microsoft Visual Studio Enterprise.
Seguire questa procedura per distribuire il servizio con IntelliTrace abilitato:
1.  Verificare che sia installato Azure SDK 1.3 o versione successiva.
2.  Distribuire la soluzione usando Visual Studio. Durante la distribuzione selezionare la casella di controllo Abilita IntelliTrace per ruoli .NET 4 .
3.  Una volta avviata l'istanza, aprire Esplora server.
4.  Espandere il nodo Servizi Azure\Cloud e individuare la distribuzione.
5.  Espandere la distribuzione finché non vengono visualizzate le istanze del ruolo. Fare clic con il pulsante destro del mouse su una delle istanze.
6.  Selezionare Visualizza log IntelliTrace. Verrà aperto Riepilogo IntelliTrace.
7.  Individuare la sezione delle eccezioni del riepilogo. Se sono presenti eccezioni, la sezione sarà denominata Dati eccezione.
8.  Espandere Dati eccezione e cercare errori System.IO.FileNotFoundException simili al seguente:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Immagine che mostra i dati dell'eccezione sull'errore di avvio del ruolo" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Risolvere DLL e assembly mancanti
Per risolvere errori di assembly e DLL mancanti, seguire questa procedura:
1.  Aprire la soluzione in Visual Studio.
2.  In Esplora soluzioni aprire la cartella Riferimenti.
3.  Fare clic sull'assembly specificato nell'errore.
4.  Nel riquadro Proprietà trovare la proprietà Copia localmente e impostare il valore su True.
5.  Ridistribuire il servizio cloud.
Dopo avere verificato che tutti gli errori sono stati corretti, è possibile distribuire il servizio senza selezionare la casella di controllo Abilita IntelliTrace per ruoli .NET 4 .

## <a name="next-steps"></a>Passaggi successivi 
- Per informazioni su come risolvere i problemi dei ruoli del servizio cloud utilizzando i dati di diagnostica del calcolo Azure PaaS, vedere la [serie di blog di Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
