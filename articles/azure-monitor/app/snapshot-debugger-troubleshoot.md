---
title: Risolvere i problemi relativi a applicazione Azure Insights Snapshot Debugger
description: Questo articolo presenta informazioni e procedure per la risoluzione dei problemi che consentono agli sviluppatori di abilitare e usare Application Insights Snapshot Debugger.
ms.topic: conceptual
author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5dd1f799634fac223670db5c38effbe7fc29cf6f
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560900"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Risolvere i problemi abilitando Application Insights Snapshot Debugger o visualizzando gli snapshot
Se è stata abilitata Application Insights Snapshot Debugger per l'applicazione, ma non vengono visualizzati snapshot per le eccezioni, è possibile usare queste istruzioni per la risoluzione dei problemi.

Possono esserci diversi motivi per cui gli snapshot non vengono generati. È possibile iniziare eseguendo il controllo integrità snapshot per identificare alcune delle possibili cause comuni.

## <a name="use-the-snapshot-health-check"></a>Usare il controllo integrità dello snapshot
Alcuni problemi comuni riguardano la mancata visualizzazione di Apri snapshot di debug. Ad esempio, se si usa un agente di raccolta snapshot obsoleto, se si raggiunge il limite giornaliero di caricamento o se il caricamento dello snapshot richiede molto tempo. Per la risoluzione di problemi comuni, usare il controllo integrità dello snapshot.

Nel riquadro dell'eccezione è presente un collegamento di visualizzazione traccia end-to-end che consente di visualizzare il controllo integrità dello snapshot.

![Immettere il controllo integrità dello snapshot](./media/snapshot-debugger/enter-snapshot-health-check.png)

L'interfaccia interattiva, simile a una chat, esegue la ricerca di problemi comuni e guida l'utente nella risoluzione.

![Controllo integrità](./media/snapshot-debugger/healthcheck.png)

Se il problema non viene risolto, fare riferimento ai passaggi manuali di risoluzione dei problemi seguenti.

## <a name="verify-the-instrumentation-key"></a>Verificare la chiave di strumentazione

Verificare di usare la chiave di strumentazione corretta nell'applicazione pubblicata. In genere, la chiave di strumentazione viene letta dal file ApplicationInsights.config. Verificare che il valore sia lo stesso della chiave di strumentazione per la risorsa di Application Insights visualizzata nel portale.

## <a name="check-ssl-client-settings-aspnet"></a><a id="SSL"></a>Controllare le impostazioni del client SSL (ASP.NET)

Se si dispone di un'applicazione ASP.NET ospitata nel servizio app Azure o in IIS in una macchina virtuale, l'applicazione potrebbe non riuscire a connettersi al servizio Snapshot Debugger a causa di un protocollo di sicurezza SSL mancante.
[L'endpoint snapshot debugger richiede TLS versione 1,2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Il set di protocolli di sicurezza SSL è uno dei peculiari abilitati dal valore httpRuntime targetFramework nella sezione System. Web della web.config. Se httpRuntime targetFramework è 4.5.2 o Lower, TLS 1,2 non è incluso per impostazione predefinita.

> [!NOTE]
> Il valore targetFramework di httpRuntime è indipendente dal framework di destinazione usato durante la compilazione dell'applicazione.

Per controllare l'impostazione, aprire il file di web.config e trovare la sezione System. Web. Verificare che `targetFramework` per `httpRuntime` sia impostato su 4,6 o versione successiva.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Modificando il valore di targetFramework di httpRuntime si modificano le anomalie di runtime applicate all'applicazione e possono verificarsi modifiche di comportamento impercettibili. Assicurarsi di testare accuratamente l'applicazione dopo avere apportato questa modifica. Per un elenco completo delle modifiche per la compatibilità, vedere https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Se il targetFramework è 4,7 o superiore, Windows determina i protocolli disponibili. Nel servizio app Azure è disponibile TLS 1,2. Tuttavia, se si usa la propria macchina virtuale, potrebbe essere necessario abilitare TLS 1,2 nel sistema operativo.

## <a name="preview-versions-of-net-core"></a>Versioni di anteprima di .NET Core
Se si usa una versione di anteprima di .NET Core o l'applicazione fa riferimento Application Insights SDK, direttamente o indirettamente tramite un assembly dipendente, seguire le istruzioni per [abilitare snapshot debugger per altri ambienti](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Controllare la pagina di stato dell'estensione del sito dei servizi di diagnostica
Se Snapshot Debugger è stata abilitata tramite il [riquadro Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) nel portale, è stata abilitata dall'estensione del sito dei servizi di diagnostica.

Per controllare la pagina stato di questa estensione, passare all'URL seguente: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Il dominio del collegamento alla pagina di stato può variare a seconda del cloud.
Questo dominio sarà uguale al sito di gestione Kudu per il servizio app.

In questa pagina di stato viene visualizzato lo stato di installazione del profiler e degli agenti di Snapshot Collector. Se si è verificato un errore imprevisto, questo verrà visualizzato e mostrerà come risolverlo.

È possibile usare il sito di gestione Kudu per il servizio app per ottenere l'URL di base di questa pagina di stato:
1. Aprire l'applicazione del servizio app nel portale di Azure.
2. Selezionare **strumenti avanzati** o cercare **Kudu**.
3. Selezionare **Vai**.
4. Quando ci si trova nel sito di gestione Kudu, nell'URL **aggiungere quanto segue `/DiagnosticServices` e premere invio**.
 Si concluderà come segue: `https://<kudu-url>/DiagnosticServices`

Verrà visualizzata una pagina di stato simile alla seguente: ![ pagina stato servizi di diagnostica](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Eseguire l'aggiornamento alla versione più recente del pacchetto NuGet
In base alla modalità di abilitazione di Snapshot Debugger, vedere le opzioni seguenti:

* Se Snapshot Debugger è stato abilitato tramite il [riquadro Application Insights nel portale](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), è necessario che l'applicazione esegua già il pacchetto NuGet più recente.

* Se Snapshot Debugger è stato abilitato includendo il pacchetto NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , usare Gestione pacchetti NuGet di Visual Studio per assicurarsi che si stia usando la versione più recente di Microsoft. ApplicationInsights. SnapshotCollector.

Per gli aggiornamenti e le correzioni di bug più recenti, [vedere le note sulla versione](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Controllare i log dell'utilità di caricamento

Dopo la creazione di uno snapshot, un file di minidump (DMP) viene creato sul disco. Un processo di caricamento separato crea il file di minidump e lo carica, con i file PDB associati, nella risorsa di archiviazione Snapshot Debugger di Application Insights. Il minidump, dopo essere stato correttamente caricato, viene eliminato dal disco. I file di log per il processo di caricamento vengono conservati sul disco. In un ambiente del servizio app questi log si trovano in `D:\Home\LogFiles`. Usare il sito di gestione di Kudu per il servizio app per trovare questi file di log.

1. Aprire l'applicazione del servizio app nel portale di Azure.
2. Selezionare **strumenti avanzati** o cercare **Kudu**.
3. Selezionare **Vai**.
4. Nell'elenco a discesa **Console di debug** selezionare **CMD**.
5. Selezionare **LogFiles**.

Verrà visualizzato almeno un file con il nome che inizia con `Uploader_` o `SnapshotUploader_` e l'estensione `.log`. Selezionare l'icona appropriata per scaricare i file di log o aprirli in un browser.
Il nome del file include un suffisso univoco che identifica l'istanza di Servizio app. Se l'istanza del servizio app è ospitata in più di un computer, è presente un file di log separato per ogni computer. Quando l'utilità di caricamento rileva un nuovo file di minidump, quest'ultimo viene registrato nel file di log. Ecco un esempio di snapshot e caricamento corretti:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> L'esempio precedente è tratto dalla versione 1.2.0 del pacchetto NuGet Microsoft.ApplicationInsights.SnapshotCollector. Nelle versioni precedenti il processo di caricamento è denominato `MinidumpUploader.exe` e il log è meno dettagliato.

Nell'esempio precedente la chiave di strumentazione è `c12a605e73c44346a984e00000000000`. Questo valore deve corrispondere alla chiave di strumentazione dell'applicazione.
Il minidump è associato a uno snapshot con l'ID `139e411a23934dc0b9ea08a626db16c5`. Questo ID può essere usato in un secondo momento per individuare il record di eccezione associato in Application Insights Analytics.

L'utilità di caricamento cerca i nuovi file PDB ogni 15 minuti circa. Ecco un esempio:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Per le applicazioni _non_ ospitate nel servizio app, i log di caricamento sono nella stessa cartella dei minidump: `%TEMP%\Dumps\<ikey>` (dove `<ikey>` è la chiave di strumentazione).

## <a name="troubleshooting-cloud-services"></a>Risoluzione dei problemi di servizi cloud
Nei servizi cloud la cartella temporanea predefinita potrebbe essere troppo piccola per contenere i file di minidump, causando la perdita di snapshot.

Lo spazio necessario dipende dal working set totale dell'applicazione e dal numero di snapshot simultanei.

Il working set di un ruolo Web ASP.NET a 32 bit è in genere compreso tra 200 MB e 500 MB. È necessario consentire almeno due snapshot simultanei.

Se, ad esempio, l'applicazione USA 1 GB di working set totali, assicurarsi che siano presenti almeno 2 GB di spazio su disco per l'archiviazione degli snapshot.

Seguire questi passaggi per configurare il ruolo del servizio cloud con una risorsa locale dedicata per gli snapshot.

1. Aggiungere una nuova risorsa locale al servizio cloud modificando il file di definizione del servizio cloud (con estensione csdef). L'esempio seguente definisce una risorsa denominata `SnapshotStore` con una dimensione pari a 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modificare il codice di avvio del ruolo per aggiungere una variabile di ambiente che punti alla risorsa locale `SnapshotStore`. Per i ruoli di lavoro, il codice deve essere aggiunto al metodo `OnStart` del ruolo:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Per i ruoli Web (ASP.NET), il codice deve essere aggiunto al metodo `Application_Start` dell'applicazione Web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aggiornare il file ApplicationInsights.config del ruolo per sostituire il percorso della cartella temporanea usato da `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Sostituzione della cartella di copia shadow

Quando Snapshot Collector viene avviato, tenta di trovare una cartella sul disco adatta all'esecuzione dell'utilità di caricamento dello snapshot. La cartella selezionata è nota come cartella di copia shadow.

Snapshot Collector controlla alcuni percorsi noti, verificando di disporre delle autorizzazioni per copiare i file binari dell'utilità di caricamento dello snapshot. Vengono usate le variabili di ambiente seguenti:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Se non è possibile trovare una cartella appropriata, Snapshot Collector segnala un errore che indica che _"non è stata trovata una cartella di copia shadow appropriata"._

Se la copia ha esito negativo, Snapshot Collector segnala un errore `ShadowCopyFailed`.

Se non è possibile avviare l'utilità di caricamento, Snapshot Collector segnala un errore `UploaderCannotStartFromShadowCopy`. Il corpo del messaggio contiene spesso `System.UnauthorizedAccessException`. Questo errore si verifica in genere perché l'applicazione è in esecuzione con un account con autorizzazioni ridotte. L'account dispone delle autorizzazioni di scrittura nella cartella di copia shadow, ma non è autorizzato a eseguire il codice.

Poiché questi errori si verificano in genere durante l'avvio, sono seguiti in genere da un `ExceptionDuringConnect` errore del tipo _"Impossibile avviare l'utilità di caricamento."_

Per risolvere questi errori, è possibile specificare la cartella di copia shadow manualmente tramite l'opzione di configurazione `ShadowCopyFolder`. Esempio per ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

In alternativa, se si usa appsettings.json con un'applicazione .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usare la ricerca di Application Insights per trovare le eccezioni con gli snapshot

Quando viene creato uno snapshot, l'eccezione generata viene contrassegnata con un ID snapshot. Tale ID snapshot viene incluso come proprietà personalizzata quando l'eccezione viene segnalata Application Insights. Utilizzando **Cerca** in Application Insights, è possibile trovare tutti i record con la `ai.snapshot.id` proprietà personalizzata.

1. Passare alla risorsa di Application Insights nel portale di Azure.
2. Selezionare **Cerca**.
3. Digitare `ai.snapshot.id` nella casella di testo di ricerca e premere INVIO.

![Cercare i dati di telemetria con i ID snapshot nel portale](./media/snapshot-debugger/search-snapshot-portal.png)

Se la ricerca non restituisce alcun risultato, non è stato segnalato alcun snapshot per Application Insights nell'intervallo di tempo selezionato.

Per cercare uno specifico ID snapshot dei log di caricamento, digitare tale ID nella casella di ricerca. Se non è possibile trovare i record per uno snapshot che si conosce è stato caricato, attenersi alla procedura seguente:

1. Controllare di esaminare la risorsa di Application Insights corretta verificando la chiave di strumentazione.

2. Usando il timestamp del log di caricamento, modificare il filtro Intervallo di tempo della ricerca per coprire tale intervallo di tempo.

Se non viene ancora visualizzata un'eccezione con l'ID dello snapshot, il record di eccezione non è stato segnalato per Application Insights. Questa situazione può verificarsi se l'applicazione si è arrestata in modo anomalo dopo che ha eseguito lo snapshot, ma prima di aver segnalato il record di eccezione. In questo caso, controllare i log del servizio app in `Diagnose and solve problems` per accertare se si sono verificati riavvi non previsti o eccezioni non gestite.

## <a name="edit-network-proxy-or-firewall-rules"></a>Modificare le regole proxy o firewall di rete

Se l'applicazione si connette a Internet tramite un proxy o un firewall, potrebbe essere necessario aggiornare le regole per comunicare con il servizio Snapshot Debugger.

Gli indirizzi IP usati da Application Insights Snapshot Debugger sono inclusi nel tag del servizio di monitoraggio di Azure. Per ulteriori informazioni, vedere la [documentazione dei tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview).
