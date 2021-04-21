---
title: Configurare le impostazioni dell'app per le funzioni in Funzioni di Azure
description: Informazioni su come configurare le impostazioni dell'app per le funzioni in Funzioni di Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6775fdf8d5174600344f3c7177a3130ef63e8f76
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832681"
---
# <a name="manage-your-function-app"></a>Gestire l'app per le funzioni 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. Tutte le funzioni in un'app per le funzioni devono essere dello stesso [linguaggio.](supported-languages.md) 

Le singole funzioni in un'app per le funzioni vengono distribuite insieme e vengono ridimensionate insieme. Tutte le funzioni nella stessa app per le funzioni condividono le risorse, per istanza, mentre l'app per le funzioni viene ridimensionata. 

Le stringhe di connessione, le variabili di ambiente e altre impostazioni dell'applicazione vengono definite separatamente per ogni app per le funzioni. Tutti i dati che devono essere condivisi tra app per le funzioni devono essere archiviati esternamente in un archivio persistente.

## <a name="get-started-in-the-azure-portal"></a>Attività iniziali nel portale di Azure

1. Innanzitutto passare al [portale di Azure] e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale immettere il nome dell'app per le funzioni e selezionarla nell'elenco. 

2. In **Impostazioni** nel riquadro sinistro selezionare **Configurazione**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Panoramica dell'app per le funzioni nel portale di Azure":::

È possibile passare a tutti gli elementi necessari per gestire l'app per le funzioni dalla pagina di panoramica, in particolare le **[impostazioni dell'applicazione](#settings)** e **[le funzionalità della piattaforma](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Usare le impostazioni dell'applicazione

Le impostazioni dell'applicazione possono essere gestite [dal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) portale di Azure usando l'interfaccia della riga di comando di [Azure](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) [e Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). È anche possibile gestire le impostazioni dell'applicazione [da Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) e da [Visual Studio](functions-develop-vs.md#function-app-settings). 

Queste impostazioni vengono archiviate crittografate. Per altre informazioni, vedere [Sicurezza delle impostazioni dell'applicazione.](security-concepts.md#application-settings)

# <a name="portal"></a>[Portale](#tab/portal)

Per trovare le impostazioni dell'applicazione, vedere [Introduzione all'portale di Azure](#get-started-in-the-azure-portal). 

La **scheda Impostazioni applicazione** mantiene le impostazioni usate dall'app per le funzioni. È necessario selezionare **Mostra valori** per visualizzare i valori nel portale. Per aggiungere un'impostazione nel portale, selezionare **Nuova impostazione applicazione** e aggiungere la nuova coppia chiave-valore.

![Impostazioni dell'app per le funzioni nel portale di Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

Il [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) comando restituisce le impostazioni dell'applicazione esistenti, come nell'esempio seguente:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Il [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) comando aggiunge o aggiorna un'impostazione dell'applicazione. Nell'esempio seguente viene creata un'impostazione con una chiave `CUSTOM_FUNCTION_APP_SETTING` denominata e un valore `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Il [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) cmdlet restituisce le impostazioni dell'applicazione esistenti, come nell'esempio seguente: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Il [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) comando aggiunge o aggiorna un'impostazione dell'applicazione. Nell'esempio seguente viene creata un'impostazione con una chiave `CUSTOM_FUNCTION_APP_SETTING` denominata e un valore `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Usare le impostazioni dell'applicazione

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando si sviluppa un'app per le funzioni in locale, è necessario mantenere copie locali di questi valori nel local.settings.jsnel file di progetto. Per altre informazioni, vedere [File di impostazioni locali.](functions-run-local.md#local-settings-file)

## <a name="hosting-plan-type"></a>Tipo di piano di hosting

Quando si crea un'app per le funzioni, si crea anche un piano di hosting in cui viene eseguita l'app. Un piano può avere una o più app per le funzioni. La funzionalità, la scalabilità e i prezzi delle funzioni dipendono dal tipo di piano. Per altre informazioni, vedere Opzioni [Funzioni di Azure di hosting.](functions-scale.md)

È possibile determinare il tipo di piano usato dall'app per le funzioni dal portale di Azure o usando l'interfaccia della riga di comando di Azure o le API Azure PowerShell servizio. 

I valori seguenti indicano il tipo di piano:

| Tipo di piano | Portale | Interfaccia della riga di comando di Azure/PowerShell |
| --- | --- | --- |
| [Consumo](consumption-plan.md) | **Consumo** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedicato (servizio app)](dedicated-plan.md) | Vari | Vari |

# <a name="portal"></a>[Portale](#tab/portal)

Per determinare il tipo di piano usato dall'app  per le funzioni, vedere Piano di servizio **app** nella scheda Panoramica per l'app per le [funzioni nel portale di Azure](https://portal.azure.com). Per visualizzare il piano tariffario, selezionare il nome del piano di **servizio app** e quindi selezionare **Proprietà** nel riquadro sinistro.

![Visualizzare il piano nel portale](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

Eseguire il comando seguente dell'interfaccia della riga di comando di Azure per ottenere il tipo di piano di hosting:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Nell'esempio precedente sostituire `<RESOURCE_GROUP>` e con i nomi del gruppo di risorse e `<FUNCTION_APP_NAME>` dell'app per le funzioni, rispettivamente. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Eseguire il comando Azure PowerShell seguente per ottenere il tipo di piano di hosting:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Nell'esempio precedente sostituire `<RESOURCE_GROUP>` e con i nomi del gruppo di risorse e `<FUNCTION_APP_NAME>` dell'app per le funzioni, rispettivamente. 

---

## <a name="plan-migration"></a>Pianificare la migrazione

È possibile usare i comandi dell'interfaccia della riga di comando di Azure per eseguire la migrazione di un'app per le funzioni tra un piano a consumo e un piano Premium in Windows. I comandi specifici dipendono dalla direzione della migrazione. La migrazione diretta a un piano dedicato (servizio app) non è attualmente supportata.

Questa migrazione non è supportata in Linux.

### <a name="consumption-to-premium"></a>Da consumo a Premium

Usare la procedura seguente per eseguire la migrazione da un piano a consumo a un piano Premium in Windows:

1. Eseguire il comando seguente per creare un nuovo piano di servizio app (Elastic Premium) nella stessa area e nello stesso gruppo di risorse dell'app per le funzioni esistente.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Eseguire il comando seguente per eseguire la migrazione dell'app per le funzioni esistente al nuovo piano Premium

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Se il piano dell'app per le funzioni a consumo precedente non è più necessario, eliminare il piano dell'app per le funzioni originale dopo aver confermato di aver eseguito correttamente la migrazione a quello nuovo. Eseguire il comando seguente per ottenere un elenco di tutti i piani a consumo nel gruppo di risorse.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    È possibile eliminare il piano senza siti, ovvero quello da cui è stata eseguita la migrazione.

1. Eseguire il comando seguente per eliminare il piano a consumo da cui è stata eseguita la migrazione.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Da Premium a Consumo

Usare la procedura seguente per eseguire la migrazione da un piano Premium a un piano a consumo in Windows:

1. Eseguire il comando seguente per creare una nuova app per le funzioni (Consumo) nella stessa area e nello stesso gruppo di risorse dell'app per le funzioni esistente. Questo comando crea anche un nuovo piano a consumo in cui viene eseguita l'app per le funzioni.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Eseguire il comando seguente per eseguire la migrazione dell'app per le funzioni esistente al nuovo piano a consumo.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Eliminare l'app per le funzioni creata nel passaggio 1, poiché è necessario solo il piano creato per eseguire l'app per le funzioni esistente.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Se il piano dell'app per le funzioni Premium precedente non è più necessario, eliminare il piano dell'app per le funzioni originale dopo aver confermato di aver eseguito correttamente la migrazione a quello nuovo. Si noti che se il piano non viene eliminato, verrà comunque addebitato il piano Premium. Eseguire il comando seguente per ottenere un elenco di tutti i piani Premium nel gruppo di risorse.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Eseguire il comando seguente per eliminare il piano Premium da cui è stata eseguita la migrazione.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Funzionalità della piattaforma

Le app per le funzioni vengono eseguite nella piattaforma Servizio app di Azure gestita da . Di conseguenza, le app per le funzioni hanno accesso alla maggior parte delle funzionalità di piattaforma di hosting Web di base di Azure. Nel riquadro sinistro è possibile accedere alle numerose funzionalità della piattaforma del servizio app che è possibile usare nelle app per le funzioni. 

> [!NOTE]
> Non tutte le funzionalità del servizio app sono disponibili quando un'app per le funzioni viene eseguita nel piano di hosting a consumo.

La parte restante di questo articolo è incentrata sulle funzionalità del servizio app seguenti portale di Azure utili per Le funzioni:

+ [Editor del servizio app](#editor)
+ [Console](#console)
+ [Strumenti avanzati (Kudu)](#kudu)
+ [Opzioni di distribuzione](#deployment)
+ [CORS](#cors)
+ [autenticazione](#auth)

Per altre informazioni su come usare le impostazioni del servizio app, vedere [Configurare le impostazioni di del servizio app di Azure](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor del servizio app

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. Questo editor fornisce un ambiente di sviluppo avanzato per le funzioni rispetto all'editor di funzioni predefinito.  

È consigliabile prendere in considerazione lo sviluppo di funzioni nel computer locale. Quando si sviluppa in locale e si pubblica in Azure, i file di progetto sono di sola lettura nel portale. Per altre informazioni, vedere [Code and test Funzioni di Azure locally](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Console

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. 

Quando si sviluppa in locale, è consigliabile usare il Azure Functions Core Tools [e](functions-run-local.md) l'interfaccia della riga di comando [di Azure.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Strumenti avanzati (Kudu)

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centro distribuzione

Quando si usa una soluzione di controllo del codice sorgente per sviluppare e gestire il codice delle funzioni, il Centro distribuzione consente di compilare e distribuire dal controllo del codice sorgente. Il progetto viene compilato e distribuito in Azure quando si apportano aggiornamenti. Per altre informazioni, vedere [Tecnologie di distribuzione in Funzioni di Azure](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Condivisione di risorse tra le origini

Per impedire l'esecuzione di codice dannoso nel client, i browser moderni bloccano le richieste dalle applicazioni Web alle risorse in esecuzione in un dominio separato. [La condivisione di risorse tra origini (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) consente a un'intestazione di dichiarare quali origini sono autorizzate a chiamare `Access-Control-Allow-Origin` gli endpoint nell'app per le funzioni.

#### <a name="portal"></a>Portale

Quando si configura **l'elenco Origini** consentite per l'app per le funzioni, l'intestazione viene aggiunta automaticamente a tutte le risposte dagli endpoint `Access-Control-Allow-Origin` HTTP nell'app per le funzioni. 

![Configurare l'elenco CORS dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando si usa il carattere jolly ( `*` ), tutti gli altri domini vengono ignorati. 

Usare il [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add) comando per aggiungere un dominio all'elenco delle origini consentite. L'esempio seguente aggiunge il contoso.com seguente:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Usare il [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show) comando per elencare le origini consentite correnti.

### <a name="authentication"></a><a name="auth"></a>Authentication

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. Il servizio app supporta Azure Active Directory e l'accesso con provider di social network, ad esempio Facebook, Microsoft e Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service/configure-common.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Interfaccia della riga di comando di Azure]: /cli/azure/
[Azure portal]: https://portal.azure.com
