---
title: Riferimento per le impostazioni dell’app per Funzioni di Azure
description: Documentazione di riferimento per le impostazioni o le variabili di ambiente dell'app Funzioni di Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: fb00f0fe16342bf603d534c34a860278dc21deac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595977"
---
# <a name="app-settings-reference-for-azure-functions"></a>Riferimento per le impostazioni dell’app per Funzioni di Azure

Le impostazioni dell'app in un'app per le funzioni contengono le opzioni di configurazione globali che interessano tutte le funzioni della specifica app per le funzioni. Quando l'esecuzione avviene in locale, queste impostazioni sono nelle [variabili di ambiente](functions-run-local.md#local-settings-file). In questo articolo sono elencate le impostazioni dell’app che sono disponibili nelle app per le funzioni.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Sono disponibili altre opzioni di configurazione globali nel file [host.json](functions-host-json.md) e nel file [local.settings.json](functions-run-local.md#local-settings-file).

> [!NOTE]  
> È possibile utilizzare le impostazioni dell'applicazione per eseguire l'override host.jssull'impostazione di valori senza dover modificare il host.jsnel file stesso. Questa operazione è utile per gli scenari in cui è necessario configurare o modificare host.jsspecifiche sulle impostazioni per un ambiente specifico. Questo consente anche di modificare host.jssulle impostazioni senza dover ripubblicare il progetto. Per altre informazioni, vedere l' [ articolohost.jssu riferimento](functions-host-json.md#override-hostjson-values). Per le modifiche alle impostazioni dell'app per le funzioni è necessario riavviare l'app per le funzioni.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Chiave di strumentazione per Application Insights. Usare solo uno di `APPINSIGHTS_INSTRUMENTATIONKEY` o `APPLICATIONINSIGHTS_CONNECTION_STRING` . Quando Application Insights viene eseguito in un cloud sovrano, usare `APPLICATIONINSIGHTS_CONNECTION_STRING` . Per altre informazioni, vedere [How to configure Monitoring for Azure Functions](configure-monitoring.md). 

|Chiave|Valore di esempio|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Stringa di connessione per Application Insights. Usare `APPLICATIONINSIGHTS_CONNECTION_STRING` anziché `APPINSIGHTS_INSTRUMENTATIONKEY` nei casi seguenti:

+ Quando l'app per le funzioni richiede la personalizzazione aggiuntiva supportata tramite la stringa di connessione. 
+ Quando l'istanza di Application Insights viene eseguita in un cloud sovrano, che richiede un endpoint personalizzato.

Per altre informazioni, vedere [stringhe di connessione](../azure-monitor/app/sdk-connection-string.md). 

|Chiave|Valore di esempio|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [chiave]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Per impostazione predefinita, i [proxy di funzioni](functions-proxies.md) usano un collegamento per inviare chiamate API da proxy direttamente alle funzioni nella stessa app per le funzioni. Questo tasto di scelta rapida viene utilizzato anziché creare una nuova richiesta HTTP. Questa impostazione consente di disabilitare il comportamento del collegamento.

|Chiave|Valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Le chiamate con un URL back-end che punta a una funzione nell'app per le funzioni locale non verranno inviate direttamente alla funzione. Al contrario, le richieste vengono indirizzate di nuovo al front-end HTTP per l'app per le funzioni.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Le chiamate con un URL back-end che punta a una funzione nell'app per le funzioni locale vengono indirizzate direttamente alla funzione. Si tratta del valore predefinito. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Questa impostazione determina se i caratteri `%2F` vengono decodificati come barre nei parametri di route quando vengono inseriti nell'URL back-end. 

|Chiave|Valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|I parametri di route con barre codificate vengono decodificati. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Tutti i parametri di route vengono passati insieme a Unchanged, che è il comportamento predefinito. |

Si consideri, ad esempio, il proxies.jssu file per un'app per le funzioni nel `myfunction.com` dominio.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Quando `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` è impostato su `true` , l'URL viene `example.com/api%2ftest` risolto in `example.com/api/test` . Per impostazione predefinita, l'URL rimane invariato come `example.com/test%2fapi` . Per altre informazioni, vedere [proxy di funzioni](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Nella versione 2. x e nelle versioni successive del runtime di funzioni, configura il comportamento dell'app in base all'ambiente di Runtime. Questo valore viene letto durante l'inizializzazione e può essere impostato su qualsiasi valore. Solo i valori di `Development` , `Staging` e `Production` vengono rispettati dal runtime. Quando questa impostazione dell'applicazione non è presente durante l'esecuzione in Azure, si presuppone che l'ambiente sia `Production` . Usare questa impostazione anziché `ASPNETCORE_ENVIRONMENT` se è necessario modificare l'ambiente di runtime in Azure con un valore diverso da `Production` . Il Azure Functions Core Tools impostato su in `AZURE_FUNCTIONS_ENVIRONMENT` `Development` caso di esecuzione in un computer locale e non è possibile eseguirne l'override nell'local.settings.jssul file. Per altre informazioni, vedere [classe e metodi di avvio basati sull'ambiente](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods).

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Nella versione 2. x e nelle versioni successive del runtime di funzioni, le impostazioni dell'applicazione possono eseguire l'override [host.jssulle](functions-host-json.md) impostazioni nell'ambiente corrente. Queste sostituzioni sono espresse come impostazioni dell'applicazione denominate `AzureFunctionsJobHost__path__to__setting` . Per ulteriori informazioni, vedere [Override host.jssui valori](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Stringa di connessione dell’account di archiviazione facoltativo per l'archiviazione dei log e la visualizzazione nella scheda **Monitor** nel portale. Questa impostazione è valida solo per le app destinate alla versione 1. x del runtime di funzioni di Azure. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Per migliorare le prestazioni e l'esperienza, il runtime versione 2. x e versioni successive USA APPINSIGHTS_INSTRUMENTATIONKEY e Application Insights per il monitoraggio anziché `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` comporta la disabilitazione dell'impostazione predefinita per la pagina di destinazione per l'URL radice di un'app per le funzioni. Il valore predefinito è `false`.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando questa impostazione dell’app viene omessa o impostata su `false`, una pagina simile all'esempio seguente viene visualizzata in risposta all'URL `<functionappname>.azurewebsites.net`.

![Pagina di destinazione dell’app per le funzioni](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` comporta l'utilizzo in modalità di rilascio durante la compilazione del codice .NET. `false` comporta l'utilizzo in modalità debug. Il valore predefinito è `true`.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Un elenco delimitato da virgole di funzionalità beta da abilitare. Le funzionalità Beta abilitate per queste flag non sono pronte per la produzione, ma possono essere abilitate per l'utilizzo sperimentale prima di essere lanciate.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Specifica il repository o il provider da utilizzare per l'archiviazione delle chiavi. I repository supportati attualmente sono archiviazione BLOB ("Blob") e file system locale ("Files"). Il valore predefinito è BLOB nella versione 2 e file system nella versione 1.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsSecretStorageType|File|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Il runtime di funzioni di Azure usa questa stringa di connessione dell'account di archiviazione per il normale funzionamento. Alcuni usi di questo account di archiviazione includono i checkpoint di gestione delle chiavi, dei trigger timer e di hub eventi. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Vedere [Account di archiviazione](functions-infrastructure-as-code.md#storage-account) e [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Percorso per il compilatore usato per TypeScript. Consente di eseguire l'override dell'impostazione predefinita, se necessario.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Determina se la modifica nella portale di Azure è abilitata. I valori validi sono "readwrite" e "readonly".

|Chiave|Valore di esempio|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Versione del runtime di funzioni che ospita l'app per le funzioni. Una tilde ( `~` ) con versione principale significa usare la versione più recente di quella versione principale (ad esempio, "~ 3"). Quando sono disponibili nuove versioni per la stessa versione principale, vengono installate automaticamente nell'app per le funzioni. Per aggiungere l'app a una versione specifica, usare il numero di versione completo (ad esempio, "3.0.12345"). Il valore predefinito è "~ 3". Un valore di `~1` aggiunge l'app alla versione 1.x del runtime. Per altre informazioni, vedere [Panoramica delle versioni del runtime per Funzioni di Azure](functions-versions.md).

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~3|

## <a name="functions_v2_compatibility_mode"></a>Modalità di compatibilità di funzioni \_ v2 \_ \_

Questa impostazione consente all'app per le funzioni di essere eseguita in una modalità compatibile con la versione 2. x sul runtime della versione 3. x. Usare questa impostazione solo se si verificano problemi durante [l'aggiornamento dell'app per le funzioni dalla versione 2. x alla versione 3. x del runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Questa impostazione è destinata solo a una soluzione alternativa a breve termine durante l'aggiornamento dell'applicazione per l'esecuzione corretta nella versione 3. x. Questa impostazione è supportata fino a quando il [Runtime 2. x è supportato](functions-versions.md). Se si verificano problemi che impediscono l'esecuzione dell'app nella versione 3. x senza usare questa impostazione, [segnalare il problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Richiede che [la \_ \_ versione dell'estensione funzioni](functions-app-settings.md#functions_extension_version) sia impostata su `~3` .

|Chiave|Valore di esempio|
|---|------------|
|Modalità di compatibilità di funzioni \_ v2 \_ \_|true|

## <a name="functions_worker_process_count"></a>\_numero di \_ processi di lavoro di funzioni \_

Specifica il numero massimo di processi di lavoro del linguaggio e il valore predefinito `1` . Il valore massimo consentito è `10` . Le chiamate di funzione sono distribuite uniformemente tra i processi di lavoro del linguaggio. I processi di lavoro del linguaggio vengono generati ogni 10 secondi finché non viene raggiunto il conteggio impostato dal conteggio dei processi di lavoro di funzioni \_ \_ \_ . L'utilizzo di più processi di lavoro in linguaggio non equivale al [ridimensionamento](functions-scale.md). Provare a usare questa impostazione quando il carico di lavoro include una combinazione di chiamate con binding CPU e I/O. Questa impostazione si applica a tutte le lingue non-.NET.

|Chiave|Valore di esempio|
|---|------------|
|\_numero di \_ processi di lavoro di funzioni \_|2|

## <a name="python_threadpool_thread_count"></a>\_ \_ conteggio thread THREADPOOL \_ Python

Specifica il numero massimo di thread che un ruolo di lavoro del linguaggio Python utilizzerà per eseguire chiamate di funzioni, con il valore predefinito `1` per la versione di Python e di `3.8` seguito. Per la versione di Python `3.9` e versioni successive, il valore è impostato su `None` . Si noti che questa impostazione non garantisce il numero di thread che verrebbero impostati durante le esecuzioni. L'impostazione consente a Python di espandere il numero di thread al valore specificato. L'impostazione si applica solo alle app per le funzioni di Python. Inoltre, l'impostazione si applica alla chiamata di funzioni sincrone e non per le coroutine.

|Chiave|Valore di esempio|Valore massimo|
|---|------------|---------|
|\_ \_ conteggio thread THREADPOOL \_ Python|2|32|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Il runtime del ruolo di lavoro del linguaggio da caricare nell'app per le funzioni.  Corrisponderà al linguaggio usato nell'applicazione (ad esempio, "dotnet"). Per le funzioni in più lingue, sarà necessario pubblicarle in più app, ciascuna con un valore del runtime del ruolo di lavoro corrispondente.  I valori validi sono `dotnet` (C#/f #), `node` (JavaScript/typescript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>\_URL dell' \_ indice \_ aggiuntivo PIP

Il valore di questa impostazione indica un URL dell'indice del pacchetto personalizzato per le app Python. Utilizzare questa impostazione quando è necessario eseguire una compilazione remota utilizzando dipendenze personalizzate presenti in un indice di pacchetto aggiuntivo.   

|Chiave|Valore di esempio|
|---|------------|
|\_URL dell' \_ indice \_ aggiuntivo PIP|http://my.custom.package.repo/simple |

Per altre informazioni, vedere [dipendenze personalizzate](functions-reference-python.md#remote-build-with-extra-index-url) nella Guida di riferimento per gli sviluppatori Python.

## <a name="scale_controller_logging_enabled"></a>registrazione del controller di SCALAbilità \_ \_ \_ abilitata

_Questa impostazione è attualmente disponibile in anteprima._  

Questa impostazione controlla la registrazione dal controller di scalabilità di funzioni di Azure. Per altre informazioni, vedere [ridimensionare i log del controller](functions-monitoring.md#scale-controller-logs).

|Chiave|Valore di esempio|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights: Verbose|

Il valore di questa chiave viene fornito nel formato `<DESTINATION>:<VERBOSITY>` , che viene definito come segue:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>SITO Web \_ CONTENTAZUREFILECONNECTIONSTRING

Stringa di connessione per l'account di archiviazione in cui il codice e la configurazione dell'app per le funzioni vengono archiviati nei piani di scalabilità basati su eventi in esecuzione in Windows. Per altre informazioni, vedere [creare un'app](functions-infrastructure-as-code.md#windows)per le funzioni.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

Utilizzato solo quando si esegue la distribuzione a un piano Premium o a un piano a consumo in esecuzione in Windows. Non supportato per i piani di consumo che eseguono Linux. La modifica o la rimozione di questa impostazione può impedire l'avvio dell'app per le funzioni. Per altre informazioni, vedere [questo articolo sulla risoluzione dei problemi](functions-recover-storage-account.md#storage-account-application-settings-were-deleted). 

## <a name="website_contentovervnet"></a>SITO Web \_ CONTENTOVERVNET

Solo per i piani Premium. Il valore `1` consente la scalabilità dell'app per le funzioni quando l'account di archiviazione è limitato a una rete virtuale. È consigliabile abilitare questa impostazione quando si limita l'account di archiviazione a una rete virtuale. Per altre informazioni, vedere [limitare l'account di archiviazione a una rete virtuale](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Il percorso del file per il codice e la configurazione dell'app per le funzioni in un piano di scalabilità basato su eventi in Windows. Usato con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Il valore predefinito è una stringa univoca che inizia con il nome dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#windows).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

Utilizzato solo quando si esegue la distribuzione a un piano Premium o a un piano a consumo in esecuzione in Windows. Non supportato per i piani di consumo che eseguono Linux. La modifica o la rimozione di questa impostazione può impedire l'avvio dell'app per le funzioni. Per altre informazioni, vedere [questo articolo sulla risoluzione dei problemi](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

Quando si usa un Azure Resource Manager per creare un'app per le funzioni durante la distribuzione, non includere WEBSITE_CONTENTSHARE nel modello. Questa impostazione dell'applicazione viene generata durante la distribuzione. Per altre informazioni, vedere [automatizzare la distribuzione delle risorse per l'app per le funzioni](functions-infrastructure-as-code.md#windows).   

## <a name="website_dns_server"></a>\_server DNS del sito Web \_

Imposta il server DNS usato da un'app durante la risoluzione degli indirizzi IP. Questa impostazione è spesso obbligatoria quando si usano determinate funzionalità di rete, ad esempio le [zone private di DNS di Azure](functions-networking-options.md#azure-dns-private-zones) e gli [endpoint privati](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network).   

|Chiave|Valore di esempio|
|---|------------|
|\_server DNS del sito Web \_|168.63.129.16|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Numero massimo di istanze a cui l'app può applicare la scalabilità orizzontale. Il valore predefinito è no limit.

> [!IMPORTANT]
> Questa impostazione è in anteprima.  È stata aggiunta una [Proprietà app per la funzione Max scale out](./event-driven-scaling.md#limit-scale-out) ed è il metodo consigliato per limitare la scalabilità orizzontale.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Solo Windows._  
Imposta la versione di Node.js da usare quando si esegue l'app per le funzioni in Windows. È consigliabile usare una tilde (~) per fare in modo che il runtime usi la versione disponibile più recente della versione principale di destinazione. Se, ad esempio, si imposta su `~10` , viene utilizzata la versione più recente di Node.js 10. Quando una versione principale è destinata a una tilde, non è necessario aggiornare manualmente la versione secondaria. 

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Consente l'esecuzione dell'app per le funzioni da un file di pacchetto montato.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

I valori validi sono un URL che si risolve nel percorso di un file di pacchetto di distribuzione o `1`. Se impostato su `1`, il pacchetto deve trovarsi nella cartella `d:\home\data\SitePackages`. Quando si usa la distribuzione di file zip con questa impostazione, il pacchetto viene caricato automaticamente in questo percorso. In anteprima questa impostazione era denominata `WEBSITE_RUN_FROM_ZIP`. Per altre informazioni, vedere [Run your Functions from a package file](run-functions-from-deployment-package.md) (Esecuzione di Funzioni di Azure da un file di pacchetto).

## <a name="website_time_zone"></a>\_fuso orario sito Web \_

Consente di impostare il fuso orario per l'app per le funzioni. 

|Chiave|OS|Valore di esempio|
|---|--|------------|
|\_fuso orario sito Web \_|Windows|Ora solare fuso orientale|
|\_fuso orario sito Web \_|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>SITO Web \_ VNET \_ route \_ All

Indica se tutto il traffico in uscita dall'app viene instradato attraverso la rete virtuale. Un valore di impostazione `1` indica che tutto il traffico viene instradato attraverso la rete virtuale. È necessario usare questa impostazione quando si usano le funzionalità di [integrazione di rete virtuale a livello](functions-networking-options.md#regional-virtual-network-integration)di area. Viene usato anche quando [viene usato un gateway NAT di rete virtuale per definire un indirizzo IP in uscita statico](functions-how-to-use-nat-gateway.md). 

|Chiave|Valore di esempio|
|---|------------|
|SITO Web \_ VNET \_ route \_ All|1|

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su come aggiornare le impostazioni dell'app](functions-how-to-use-azure-function-app-settings.md#settings)

[Vedere le impostazioni globali nel file host.json](functions-host-json.md)

[Vedere altre impostazioni app per le app del servizio app](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
