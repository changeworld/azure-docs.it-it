---
title: Abilitare la registrazione diagnostica
description: Informazioni su come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione e su come accedere alle informazioni registrate da Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: ffff4215ddbe3f01da927cb47fb4e06f4946a207
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833851"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Abilitare la registrazione diagnostica per le app nel Servizio app di Azure
## <a name="overview"></a>Panoramica
Azure offre diagnostica integrata per facilitare il debug di un'[app del servizio app](overview.md). In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione. Viene anche descritto come accedere alle informazioni registrate da Azure.

L'articolo illustra anche l'uso del [portale di Azure](https://portal.azure.com) e dell'interfaccia della riga di comando di Azure per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Oltre alle istruzioni di registrazione in questo articolo, è presente una nuova funzionalità di registrazione integrata con Monitoraggio di Azure. Per altre informazioni su questa funzionalità, vedere la sezione Inviare [log Monitoraggio di Azure (anteprima).](#send-logs-to-azure-monitor-preview) 
>
>

|Tipo|Piattaforma|Location|Descrizione|
|-|-|-|-|
| Registrazione di applicazioni | Windows, Linux | BLOB file system e/o Archiviazione di Azure servizio app | Registra i messaggi generati dal codice dell'applicazione. I messaggi possono essere generati dal framework Web scelto o dal codice dell'applicazione direttamente usando il modello di registrazione standard del linguaggio. A ogni messaggio viene assegnata una delle categorie seguenti: **Critical**, **Error**, **Warning**, **Info**, **Debug** e **Trace**. È possibile selezionare il livello di dettaglio desiderato per la registrazione impostando il livello di gravità quando si abilita la registrazione delle applicazioni.|
| Registrazione del server Web| Windows | BLOB di file system o Archiviazione di Azure app| Dati delle richieste HTTP non elaborati nel formato di file di log esteso [W3C](/windows/desktop/Http/w3c-logging). Ogni messaggio di log include dati quali il metodo HTTP, l'URI della risorsa, l'INDIRIZZO IP client, la porta client, l'agente utente, il codice di risposta e così via. |
| Messaggi di errore dettagliati| Windows | Servizio app file system | Copie delle pagine *di errore htm* che sarebbero state inviate al browser client. Per motivi di sicurezza, le pagine di errore dettagliate non devono essere inviate ai client in produzione, ma il servizio app può salvare la pagina di errore ogni volta che si verifica un errore dell'applicazione con codice HTTP 400 o versione successiva. La pagina può contenere informazioni che consentono di determinare il motivo per cui il server restituisce il codice di errore. |
| Traccia delle richieste non riuscite | Windows | Servizio app file system | Informazioni di traccia dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS usati per elaborare la richiesta e il tempo impiegato in ogni componente. È utile per migliorare le prestazioni del sito o isolare uno specifico errore HTTP. Viene generata una cartella per ogni richiesta non riuscita, che contiene il file di log XML, e il foglio di stile XSL con cui visualizzare il file di log. |
| Registrazione della distribuzione | Windows, Linux | Servizio app file system | Log per quando si pubblica contenuto in un'app. La registrazione della distribuzione viene eseguita automaticamente e non sono disponibili impostazioni configurabili per la registrazione della distribuzione. Consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si usa uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), è possibile usare la registrazione della distribuzione per determinare il motivo per cui lo script ha esito negativo. |

> [!NOTE]
> Il servizio app offre uno strumento di diagnostica interattivo dedicato che consente di risolvere i problemi dell'applicazione. Per altre informazioni, vedere [Panoramica approfondita della diagnostica del Servizio app di Azure](overview-diagnostics.md).
>
> È anche possibile usare altri servizi di Azure per migliorare le funzionalità di registrazione e monitoraggio dell'app, ad esempio Monitoraggio di Azure [.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Abilitare la registrazione delle applicazioni (Windows)

> [!NOTE]
> La registrazione delle applicazioni per l'archiviazione BLOB può usare solo account di archiviazione nella stessa area del servizio app

Per abilitare la registrazione delle applicazioni per le app di Windows [portale di Azure](https://portal.azure.com), passare all'app e selezionare **Log del servizio app.**

Selezionare **On** per Application Logging **(file system)** o **Application Logging (BLOB)** o entrambi. 

**L'opzione Filesystem** è a scopo di debug temporaneo e si disattiva dopo 12 ore. **L'opzione BLOB** è per la registrazione a lungo termine e richiede un contenitore di archiviazione BLOB in cui scrivere i log.  **L'opzione BLOB** include anche informazioni aggiuntive nei messaggi di log, ad esempio l'ID dell'istanza della macchina virtuale di origine del messaggio di log ( ), l'ID thread ( ) e un `InstanceId` timestamp più `Tid` granulare ( [`EventTickCount`](/dotnet/api/system.datetime.ticks) ).

> [!NOTE]
> Attualmente solo i log applicazioni .NET possono essere scritti nell'archiviazione BLOB. I log applicazioni Java, PHP, Node.js Python possono essere archiviati solo nel file system del servizio app (senza modifiche al codice per scrivere i log nell'archiviazione esterna).
>
> Inoltre, se si rigenerano le chiavi di [accesso dell'account](../storage/common/storage-account-create.md)di archiviazione, è necessario reimpostare la rispettiva configurazione di registrazione per usare le chiavi di accesso aggiornate. Per eseguire questa operazione:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Selezionare il **livello** o il livello di dettagli da registrare. La tabella seguente illustra le categorie di log incluse in ogni livello:

| Level | Categorie incluse |
|-|-|
|**Disabilitato** | nessuno |
|**Error (Errore) (Error (Errore)e)** | Errore, Errore critico |
|**Avviso** | Avviso, Errore, Errore critico|
|**Informazioni** | Informazioni, Avviso, Errore, Errore critico|
|**Verbose** | Analisi, Debug, Informazioni, Avviso, Errore, Errore critico (tutte le categorie) |

Al termine, selezionare **Salva**.

## <a name="enable-application-logging-linuxcontainer"></a>Abilitare la registrazione delle applicazioni (Linux/Contenitore)

Per abilitare la registrazione delle applicazioni per le app Linux o le app contenitore personalizzate nel portale di Azure [,](https://portal.azure.com)passare all'app e selezionare Log del **servizio app**.

In **Registrazione applicazioni** selezionare File **system**.

In **Quota (MB)** specificare la quota del disco per i log dell'applicazione. In **Periodo di conservazione (giorni)** impostare il numero di giorni di conservazione dei log.

Al termine, selezionare **Salva**.

## <a name="enable-web-server-logging"></a>Abilitazione della registrazione del server Web

Per abilitare la registrazione del server Web per le app di Windows [nel portale di Azure](https://portal.azure.com), passare all'app e selezionare Log del **servizio app**.

Per **Registrazione server Web** selezionare Archiviazione **per** archiviare i log nell'archivio BLOB o File **system** per archiviare i log nel servizio app file system. 

In **Periodo di conservazione (giorni)** impostare il numero di giorni di conservazione dei log.

> [!NOTE]
> Se si [rigenerano le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-create.md), è necessario reimpostare la configurazione di registrazione corrispondente per l'uso delle chiavi aggiornate. Per eseguire questa operazione:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Al termine, selezionare **Salva**.

## <a name="log-detailed-errors"></a>Registrare gli errori dettagliati

Per salvare la pagina di errore o la traccia delle richieste non riuscite per le app di Windows [nel](https://portal.azure.com)portale di Azure , passare all'app e selezionare **Log del servizio app**.

In **Registrazione errori dettagliata o** Traccia richieste non **riuscite** selezionare **Attiva** e quindi selezionare **Salva**.

Entrambi i tipi di log vengono archiviati nel servizio app file system. Vengono conservati fino a 50 errori (file/cartelle). Quando il numero di file HTML supera 50, i 26 errori meno recenti vengono eliminati automaticamente.

## <a name="add-log-messages-in-code"></a>Aggiungere messaggi di log nel codice

Nel codice dell'applicazione si usano le consuete funzionalità di registrazione per inviare messaggi di log ai log dell'applicazione. Ad esempio:

- Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) per registrare le informazioni nel log di diagnostica applicazioni. Ad esempio:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Per impostazione predefinita, ASP.NET Core usa il provider di registrazione [Microsoft.Extensions.Logging.AzureAppServices.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Per altre informazioni, vedere [Registrazione in ASP.NET Core- Registrazione in Azure](/aspnet/core/fundamentals/logging/). Per informazioni sulla registrazione di WebJobs SDK, vedere Introduzione a [Processi Web di Azure SDK](./webjobs-sdk-get-started.md#enable-console-logging)

## <a name="stream-logs"></a>Eseguire lo streaming dei log

Prima di trasmettere i log in tempo reale, abilitare il tipo di log desiderato. Tutte le informazioni scritte nei file che terminano con .txt, .log o .htm archiviate nella directory */LogFiles* (d:/home/logfiles) vengono trasmesse dal servizio app.

> [!NOTE]
> Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.
>

### <a name="in-azure-portal"></a>Nel portale di Azure

Per trasmettere i log nel [portale di Azure,](https://portal.azure.com)passare all'app e selezionare **Flusso di log**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Per trasmettere i log [live](../cloud-shell/overview.md)Cloud Shell , usare il comando seguente:

> [!IMPORTANT]
> Questo comando potrebbe non funzionare con le app Web ospitate in un piano di servizio app Linux.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Per filtrare tipi di log specifici, ad esempio HTTP, usare il **parametro --Provider.** Ad esempio:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --provider http
```

### <a name="in-local-terminal"></a>Nel terminale locale

Per eseguire lo streaming dei log nella console locale, [installare l'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure [e accedere al proprio account](/cli/azure/authenticate-azure-cli). Dopo aver eseguito l'accesso, seguire [le istruzioni per Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Accedere ai file di log

Se si configura l'opzione Archiviazione di Azure blob per un tipo di log, è necessario uno strumento client che funzioni con Archiviazione di Azure. Per altre informazioni, vedere [Archiviazione di Azure Client Tools](../storage/common/storage-explorers.md).

Per i log archiviati nel file system app, il modo più semplice è scaricare il file ZIP nel browser all'indirizzo:

- App Linux/contenitore: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- App di Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

Per le app Linux/contenitore, il file ZIP contiene i log di output della console sia per l'host Docker che per il contenitore Docker. Per un'app con scalabilità orizzontale, il file ZIP contiene un set di log per ogni istanza. Nel servizio app file system questi file di log sono il contenuto della directory */home/LogFiles.*

Per le app di Windows, il file ZIP contiene il contenuto della directory *D:\Home\LogFiles* nel servizio app file system. Presenta la struttura seguente:

| Tipo di log | Directory | Descrizione |
|-|-|-|
| **Log applicazioni** |*/LogFiles/Application/* | Contiene uno o più file di testo. Il formato dei messaggi di log dipende dal provider di registrazione utilizzato. |
| **Tracce delle richieste non riuscite** | */LogFiles/W3SVC###########* | Contiene file XML e un file XSL. È possibile visualizzare i file XML formattati nel browser. |
| **Log degli errori dettagliati** | */LogFiles/DetailedErrors/* | Contiene file di errore HTM. È possibile visualizzare i file HTM nel browser.<br/>Un altro modo per visualizzare le tracce delle richieste non riuscite è passare alla pagina dell'app nel portale. Dal menu a sinistra selezionare Diagnostica e risoluzione dei **problemi,** cercare **Log** di traccia delle richieste non riuscite, quindi fare clic sull'icona per esplorare e visualizzare la traccia desiderata. |
| **Log del server Web** | */LogFiles/http/RawLogs/* | Contiene file di testo formattati con il formato di file di log esteso [W3C](/windows/desktop/Http/w3c-logging). Queste informazioni possono essere lette usando un editor di testo o un'utilità come [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22).<br/>Il servizio app non supporta i `s-computername` campi `s-ip` , o `cs-version` . |
| **Log di distribuzione** | */LogFiles/Git/* e */deployments/* | Contengono i log generati dai processi di distribuzione interni, nonché i log per le distribuzioni Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Inviare log a Monitoraggio di Azure (anteprima)

Con la nuova integrazione [Monitoraggio di Azure,](https://aka.ms/appsvcblog-azmon)è possibile creare impostazioni di diagnostica [(anteprima)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) per inviare log ad account di archiviazione, Hub eventi e Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Impostazioni di diagnostica (anteprima)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipi di log supportati

La tabella seguente illustra i tipi di log e le descrizioni supportati: 

| Tipo di log | Windows | Contenitore di Windows | Linux | Contenitore Linux | Descrizione |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | Sì | Sì | Sì | Output standard ed errore standard |
| AppServiceHTTPLogs | Sì | Sì | Sì | Sì | Web Server Logs |
| AppServiceEnvironmentPlatformLogs | Sì | N/D | Sì | Sì | ambiente del servizio app: ridimensionamento, modifiche alla configurazione e log di stato|
| AppServiceAuditLogs | Sì | Sì | Sì | Sì | Attività di accesso tramite FTP e Kudu |
| AppServiceFileAuditLogs | Sì | Sì | TBA | TBA | Modifiche ai file apportate al contenuto del sito; **disponibile solo per il livello Premium e superiore** |
| AppServiceAppLogs | ASP .NET & Java Tomcat <sup>1</sup> | ASP .NET & Java Tomcat <sup>1</sup> | Java SE & Tomcat Images <sup>2</sup> | Java SE & Tomcat Images <sup>2</sup> | Log applicazioni |
| AppServiceIPSecAuditLogs  | Sì | Sì | Sì | Sì | Richieste dalle regole IP |
| AppServicePlatformLogs  | TBA | Sì | Sì | Sì | Log delle operazioni del contenitore |
| AppServiceAntivirusScanAuditLogs | Sì | Sì | Sì | Sì | [Log di analisi antivirus con](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) Microsoft Defender; **disponibile solo per il livello Premium** | 

<sup>1</sup> Per le app Java Tomcat, aggiungere "TOMCAT_USE_STARTUP_BAT" alle impostazioni dell'app e impostarlo su false o 0. È necessario usare la versione *più recente* di Tomcat e *usare java.util.logging*.

<sup>2</sup> Per le app Java SE, aggiungere "$WEBSITE_AZMON_PREVIEW_ENABLED" alle impostazioni dell'app e impostarlo su true o su 1.

## <a name="next-steps"></a><a name="nextsteps"></a> Passaggi successivi
* [Eseguire query sui log con Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md)
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
