---
title: Tecnologie di distribuzione in Funzioni di Azure
description: Informazioni sui diversi modi in cui è possibile distribuire il codice in Funzioni di Azure.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: ca81067fa60836d77c4d8af121ebf415c772a1d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789214"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologie di distribuzione in Funzioni di Azure

È possibile usare alcune tecnologie diverse per distribuire il codice Funzioni di Azure progetto in Azure. Questo articolo offre una panoramica dei metodi di distribuzione disponibili e consigli per il metodo migliore da usare in diversi scenari. Fornisce anche un elenco completo dei dettagli chiave e delle tecnologie di distribuzione sottostanti. 

## <a name="deployment-methods"></a>Metodi di distribuzione

La tecnologia di distribuzione che si usa per pubblicare il codice in Azure è in genere determinata dal modo in cui si pubblica l'app. Il metodo di distribuzione appropriato è determinato da esigenze specifiche e dal punto nel ciclo di sviluppo. Ad esempio, durante lo sviluppo e il test è possibile eseguire la distribuzione direttamente dallo strumento di sviluppo, ad esempio Visual Studio Code. Quando l'app è in produzione, è più probabile che la pubblicazione sia continua dal controllo del codice sorgente o tramite una pipeline di pubblicazione automatica, che include convalida e test aggiuntivi.  

La tabella seguente descrive i metodi di distribuzione disponibili per il progetto Di funzione.

| Tipo di &nbsp; distribuzione | Metodi | Ideale per... |
| -- | -- | -- |
| Basato su strumenti | &bull;&nbsp;[Pubblicazione in Visual &nbsp; Studio &nbsp; Code &nbsp;](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio pubblicare](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Pubblicazione di Core Tools](functions-run-local.md#publish) | Distribuzioni durante lo sviluppo e altre distribuzioni ad hock. Le distribuzioni vengono gestite in locale dagli strumenti. | 
| Gestito dal servizio app| &bull;&nbsp;[Centro &nbsp; distribuzione &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Distribuzioni &nbsp; di contenitori](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Distribuzione continua (CI/CD) dal controllo del codice sorgente o da un registro contenitori. Le distribuzioni vengono gestite dalla piattaforma del servizio app (Kudu).|
| Pipeline esterne|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[Azioni di GitHub](functions-how-to-github-actions.md) | Le pipeline devOps e di produzione che includono convalida, test e altre azioni aggiuntive vengono eseguite come parte di una distribuzione automatizzata. Le distribuzioni vengono gestite dalla pipeline. |

Sebbene le distribuzioni di Funzioni specifiche usino la tecnologia migliore in base al contesto, la maggior parte dei metodi di distribuzione si basa sulla [distribuzione zip.](#zip-deploy)

## <a name="deployment-technology-availability"></a>Disponibilità della tecnologia di distribuzione

Funzioni di Azure supporta lo sviluppo e l'hosting locali multipiattaforma in Windows e Linux. Attualmente sono disponibili tre piani di hosting:

+ [Consumo](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedicato (servizio app)](dedicated-plan.md)

Ogni piano ha comportamenti diversi. Non tutte le tecnologie di distribuzione sono disponibili per ogni versione di Funzioni di Azure. Il grafico seguente mostra le tecnologie di distribuzione supportate per ogni combinazione di sistema operativo e piano di hosting:

| Tecnologia di distribuzione | Utilizzo di Windows | Windows Premium | Windows dedicato  | a consumo per Linux | Linux Premium | Linux dedicato |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL pacchetto<sup>esterno 1</sup> |✔|✔|✔|✔|✔|✔|
| Zip deploy |✔|✔|✔|✔|✔|✔|
| Contenitore Docker | | | | |✔|✔|
| Distribuzione Web |✔|✔|✔| | | |
| Controllo del codice sorgente |✔|✔|✔| |✔|✔|
| Git<sup>1 locale</sup> |✔|✔|✔| |✔|✔|
| Sincronizzazione cloud<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Modifica nel portale |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Tecnologia di distribuzione che richiede la [sincronizzazione manuale dei trigger.](#trigger-syncing)
<sup>2</sup> La modifica del portale è abilitata solo per i trigger HTTP e Timer per Funzioni in Linux che usano piani Premium e Dedicati.

## <a name="key-concepts"></a>Concetti chiave

Alcuni concetti chiave sono fondamentali per comprendere il funzionamento delle distribuzioni in Funzioni di Azure.

### <a name="trigger-syncing"></a>Attivazione della sincronizzazione

Quando si modifica uno dei trigger, l'infrastruttura di Funzioni deve essere a conoscenza delle modifiche. La sincronizzazione avviene automaticamente per molte tecnologie di distribuzione. In alcuni casi, tuttavia, è necessario sincronizzare manualmente i trigger. Quando si distribuiscono gli aggiornamenti facendo riferimento a un URL del pacchetto esterno, a git locale, a una sincronizzazione cloud o a FTP, è necessario sincronizzare manualmente i trigger. È possibile sincronizzare i trigger in uno dei tre modi seguenti:

* Riavviare l'app per le funzioni nel portale di Azure
* Inviare una richiesta HTTP POST a `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` usando la chiave [master](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Inviare una richiesta HTTP POST a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app per le funzioni.

### <a name="remote-build"></a>Compilazione remota

Funzioni di Azure eseguire automaticamente compilazioni sul codice ricevuto dopo le distribuzioni zip. Queste build si comportano in modo leggermente diverso a seconda che l'app sia in esecuzione in Windows o Linux. Le compilazioni remote non vengono eseguite quando un'app è stata impostata in precedenza per l'esecuzione in [modalità Esegui da](run-functions-from-deployment-package.md) pacchetto. Per informazioni su come usare la compilazione remota, passare a [zip deploy](#zip-deploy).

> [!NOTE]
> Se si verificano problemi con la compilazione remota, è possibile che l'app sia stata creata prima che la funzionalità sia stata resa disponibile (1 agosto 2019). Provare a creare una nuova app per le funzioni o ad `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` aggiornare l'app per le funzioni. Questo comando potrebbe richiedere due tentativi per l'esito positivo.

#### <a name="remote-build-on-windows"></a>Compilazione remota in Windows

Tutte le app per le funzioni in esecuzione in Windows hanno una piccola app di gestione, il sito SCM [(o Kudu).](https://github.com/projectkudu/kudu) Questo sito gestisce gran parte della logica di distribuzione e compilazione per Funzioni di Azure.

Quando un'app viene distribuita in Windows, vengono eseguiti comandi specifici del linguaggio, ad esempio `dotnet restore` (C#) o `npm install` (JavaScript).

#### <a name="remote-build-on-linux"></a>Compilazione remota in Linux

Per abilitare la compilazione remota in Linux, è necessario impostare le [impostazioni dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings) seguenti:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Per impostazione predefinita, [sia Azure Functions Core Tools](functions-run-local.md) che l'estensione [Funzioni di Azure per](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) Visual Studio Code eseguire compilazioni remote durante la distribuzione in Linux. Per questo, entrambi gli strumenti creano automaticamente queste impostazioni in Azure.

Quando le app vengono compilate in modalità remota in Linux, [vengono eseguite dal pacchetto di distribuzione](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Piano a consumo

Le app per le funzioni Linux in esecuzione nel piano a consumo non hanno un sito SCM/Kudu, che limita le opzioni di distribuzione. Tuttavia, le app per le funzioni in Linux in esecuzione nel piano a consumo supportano le compilazioni remote.

##### <a name="dedicated-and-premium-plans"></a>Piani Dedicati e Premium

Anche le app per le funzioni in esecuzione in Linux nel piano dedicato [(servizio app)](dedicated-plan.md) e nel piano [Premium](functions-premium-plan.md) hanno un sito SCM/Kudu limitato.

## <a name="deployment-technology-details"></a>Dettagli della tecnologia di distribuzione

I metodi di distribuzione seguenti sono disponibili in Funzioni di Azure.

### <a name="external-package-url"></a>URL del pacchetto esterno

È possibile usare un URL del pacchetto esterno per fare riferimento a un file di pacchetto remoto (zip) che contiene l'app per le funzioni. Il file viene scaricato dall'URL specificato e l'app viene eseguita in [modalità Esegui da](run-functions-from-deployment-package.md) pacchetto.

>__Come usarlo:__ Aggiungere [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) alle impostazioni dell'applicazione. Il valore di questa impostazione deve essere un URL (il percorso del file del pacchetto specifico che si vuole eseguire). È possibile aggiungere impostazioni nel portale o [tramite](functions-how-to-use-azure-function-app-settings.md#settings) l'interfaccia della [riga di comando di Azure.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)
>
>Se si usa l'archiviazione BLOB di Azure, usare un contenitore privato con una firma di accesso condiviso [per](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) concedere a Funzioni l'accesso al pacchetto. Ogni volta che l'applicazione viene riavviata, recupera una copia del contenuto. Il riferimento deve essere valido per la durata dell'applicazione.

>__Quando usarlo:__ L'URL del pacchetto esterno è l'unico metodo di distribuzione supportato per Funzioni di Azure in esecuzione in Linux nel piano a consumo, se l'utente non vuole che si verifichi una [compilazione](#remote-build) remota. Quando si aggiorna il file del pacchetto a cui fa riferimento un'app per le funzioni, è necessario sincronizzare manualmente [i trigger](#trigger-syncing) per indicare ad Azure che l'applicazione è stata modificata.

### <a name="zip-deploy"></a>Zip deploy

Usare zip deploy per eseguire il push di un file ZIP che contiene l'app per le funzioni in Azure. Facoltativamente, è possibile impostare l'app in modo che inizi [l'esecuzione](run-functions-from-deployment-package.md)dal pacchetto o specificare che si verifica una [compilazione](#remote-build) remota.

>__Come usarlo:__ Eseguire la distribuzione usando lo strumento client preferito: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)o dalla riga di comando [usando](functions-run-local.md#project-file-deployment)Azure Functions Core Tools . Per impostazione predefinita, questi strumenti usano la distribuzione ZIP ed [eseguono dal pacchetto](run-functions-from-deployment-package.md). Core Tools e l'estensione Visual Studio Code consentono la [compilazione remota](#remote-build) durante la distribuzione in Linux. Per distribuire manualmente un file ZIP nell'app per le funzioni, seguire le istruzioni in Distribuire da un [file ZIP o da un URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Quando si esegue la distribuzione con zip deploy, è possibile impostare l'app per [l'esecuzione dal pacchetto](run-functions-from-deployment-package.md). Per eseguire dal pacchetto, impostare il [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) valore dell'impostazione dell'applicazione su `1` . È consigliabile usare la distribuzione ZIP. Produce tempi di caricamento più rapidi per le applicazioni ed è l'impostazione predefinita per VS Code, Visual Studio e l'interfaccia della riga di comando di Azure.

>__Quando usarlo:__ Zip deploy è la tecnologia di distribuzione consigliata per Funzioni di Azure.

### <a name="docker-container"></a>Contenitore Docker

È possibile distribuire un'immagine del contenitore Linux che contiene l'app per le funzioni.

>__Come usarlo:__ Creare un'app per le funzioni Linux nel piano Premium o Dedicato e specificare l'immagine del contenitore da cui eseguire. Questa operazione può essere eseguita in due modi:
>
>* Creare un'app per le funzioni Linux in un piano Servizio app di Azure in portale di Azure. Per **Pubblica** selezionare **Immagine Docker** e quindi configurare il contenitore. Immettere il percorso in cui è ospitata l'immagine.
>* Creare un'app per le funzioni Linux in un piano di servizio app usando l'interfaccia della riga di comando di Azure. Per informazioni su come, vedere [Creare una funzione in Linux usando un'immagine personalizzata.](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Per eseguire la distribuzione in un'app esistente usando un contenitore personalizzato, [in](functions-run-local.md)Azure Functions Core Tools usare il [`func deploy`](functions-run-local.md#publish) comando .

>__Quando usarlo:__ Usare l'opzione Del contenitore Docker quando è necessario un maggiore controllo sull'ambiente Linux in cui viene eseguita l'app per le funzioni. Questo meccanismo di distribuzione è disponibile solo per le funzioni in esecuzione in Linux.

### <a name="web-deploy-msdeploy"></a>Distribuzione Web (MSDeploy)

Distribuzione Web pacchetti e distribuisce le applicazioni Windows in qualsiasi server IIS, incluse le app per le funzioni in esecuzione in Windows in Azure.

>__Come usarlo:__ Usare [Visual Studio per Funzioni di Azure](functions-create-your-first-function-visual-studio.md). Deselezionare la casella di controllo Esegui dal file del pacchetto **(scelta** consigliata).
>
>È anche possibile scaricare [Distribuzione Web 3.6](https://www.iis.net/downloads/microsoft/web-deploy) e chiamare `MSDeploy.exe` direttamente .

>__Quando usarlo:__ Distribuzione Web è supportato e non presenta problemi, ma il meccanismo preferito è la distribuzione [zip con l'opzione Esegui da pacchetto abilitata.](#zip-deploy) Per altre informazioni, vedere la guida [Visual Studio sviluppo.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Controllo del codice sorgente

Usare il controllo del codice sorgente per connettere l'app per le funzioni a un repository Git. Un aggiornamento del codice in tale repository attiva la distribuzione. Per altre informazioni, vedere il [wiki di Kudu.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Come usarlo:__ Usare Centro distribuzione nell'area Funzioni del portale per configurare la pubblicazione dal controllo del codice sorgente. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

>__Quando usarlo:__ L'uso del controllo del codice sorgente è la procedura consigliata per i team che collaborano alle app per le funzioni. Il controllo del codice sorgente è un'opzione di distribuzione valida che consente pipeline di distribuzione più sofisticate.

### <a name="local-git"></a>Repository Git locale

È possibile usare Git locale per eseguire il push del codice dal computer locale Funzioni di Azure usando Git.

>__Come usarlo:__ Seguire le istruzioni in [Distribuzione git locale per Servizio app di Azure](../app-service/deploy-local-git.md).

>__Quando usarlo:__ In generale, è consigliabile usare un metodo di distribuzione diverso. Quando si pubblica da Git locale, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronizzazione cloud

Usare la sincronizzazione cloud per sincronizzare il contenuto da Dropbox e OneDrive Funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [Sincronizzare il contenuto da una cartella cloud.](../app-service/deploy-content-sync.md)

>__Quando usarlo:__ In generale, è consigliabile usare altri metodi di distribuzione. Quando si esegue la pubblicazione usando la sincronizzazione cloud, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="ftp"></a>FTP

È possibile usare FTP per trasferire direttamente i file Funzioni di Azure.

>__Come usarlo:__ Seguire le istruzioni in [Distribuire il contenuto tramite FTP/s](../app-service/deploy-ftp.md).

>__Quando usarlo:__ In generale, è consigliabile usare altri metodi di distribuzione. Quando si esegue la pubblicazione tramite FTP, è necessario [sincronizzare manualmente i trigger](#trigger-syncing).

### <a name="portal-editing"></a>Modifica del portale

Nell'editor basato sul portale è possibile modificare direttamente i file presenti nell'app per le funzioni ,essenzialmente distribuendo ogni volta che si salvano le modifiche.

>__Come usarlo:__ Per poter modificare le funzioni nel portale di Azure, è necessario aver [creato le funzioni nel portale](./functions-get-started.md). Per mantenere un'unica origine di verità, l'uso di qualsiasi altro metodo di distribuzione rende la funzione di sola lettura e impedisce la modifica continua del portale. Per tornare a uno stato in cui è possibile modificare i file nel portale di Azure, è possibile riattivare manualmente la modalità di modifica e rimuovere le impostazioni dell'applicazione correlate alla distribuzione, ad `Read/Write` esempio [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Quando usarlo:__ Il portale è un ottimo modo per iniziare a usare Funzioni di Azure. Per attività di sviluppo più intense, è consigliabile usare uno degli strumenti client seguenti:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (riga di comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

La tabella seguente illustra i sistemi operativi e le lingue che supportano la modifica del portale:

| Linguaggio | Utilizzo di Windows | Windows Premium | Windows dedicato | a consumo per Linux | Linux Premium | Linux dedicato |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Script C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (anteprima) | | | | | | |
| PowerShell (anteprima) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> La modifica del portale è abilitata solo per i trigger HTTP e Timer per Funzioni in Linux che usano piani Premium e Dedicati.

## <a name="deployment-behaviors"></a>Comportamenti di distribuzione

Quando si esegue una distribuzione, a tutte le esecuzioni esistenti è consentito il completamento o il timeout, dopo il quale il nuovo codice viene caricato per iniziare a elaborare le richieste.

Se è necessario un maggiore controllo su questa transizione, è consigliabile usare gli slot di distribuzione.

## <a name="deployment-slots"></a>Slot di distribuzione

Quando si distribuisce l'app per le funzioni in Azure, è possibile eseguire la distribuzione in uno slot di distribuzione separato anziché direttamente nell'ambiente di produzione. Per altre informazioni sugli slot di distribuzione, vedere la [documentazione Funzioni di Azure degli slot](functions-deployment-slots.md) di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

Leggere questi articoli per altre informazioni sulla distribuzione delle app per le funzioni:

+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)
+ [Recapito continuo tramite Azure DevOps](functions-how-to-azure-devops.md)
+ [Distribuzioni ZIP per Funzioni di Azure](deployment-zip-push.md)
+ [Eseguire Funzioni di Azure da un file di pacchetto](run-functions-from-deployment-package.md)
+ [Automatizzare la distribuzione delle risorse per l'app per le funzioni in Funzioni di Azure](functions-infrastructure-as-code.md)
