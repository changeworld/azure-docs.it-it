---
title: Come disabilitare le funzioni in Funzioni di Azure
description: Informazioni su come disabilitare e abilitare le funzioni in Funzioni di Azure.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 03803abfda010c81fa8286a478d626ef39db59fb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777582"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Come disabilitare le funzioni in Funzioni di Azure

Questo articolo illustra come disabilitare una funzione in Funzioni di Azure. *Disabilitare* una funzione significa fare in modo che il runtime ignori il trigger automatico definito per la funzione. Ciò consente di impedire l'esecuzione di una funzione specifica senza arrestare l'intera app per le funzioni.

Il modo consigliato per disabilitare una funzione è con un'impostazione dell'app nel formato `AzureWebJobs.<FUNCTION_NAME>.Disabled` impostato su `true` . È possibile creare e modificare questa impostazione dell'applicazione in diversi [modi,](https://portal.azure.com)tra  cui usando l'interfaccia della riga di comando di [Azure](/cli/azure/) e dalla scheda Panoramica della funzione nella portale di Azure . 

> [!NOTE]  
> Quando si disabilita una funzione attivata tramite HTTP usando i metodi descritti in questo articolo, l'endpoint può ancora essere accessibile quando viene eseguito nel computer locale.  

## <a name="disable-a-function"></a>Disabilitare una funzione

# <a name="portal"></a>[Portale](#tab/portal)

Usare i **pulsanti** **Abilita e Disabilita** nella pagina Panoramica **della** funzione. Questi pulsanti funzionano modificando il valore `AzureWebJobs.<FUNCTION_NAME>.Disabled` dell'impostazione dell'app. Questa impostazione specifica della funzione viene creata la prima volta che viene disabilitata. 

![Opzione Stato funzione](media/disable-function/function-state-switch.png)

Anche quando si pubblica nell'app per le funzioni da un progetto locale, è comunque possibile usare il portale per disabilitare le funzioni nell'app per le funzioni. 

> [!NOTE]  
> La funzionalità di test integrata nel portale ignora l'impostazione `Disabled`. Ciò significa che una funzione disabilitata viene comunque eseguita quando viene avviata dalla finestra **Test** nel portale. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

Nell'interfaccia della riga di comando di Azure, il comando [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) consente di creare e modificare l'impostazione dell'app. Il comando seguente disabilita una funzione denominata creando `QueueTrigger` un'impostazione dell'app denominata `AzureWebJobs.QueueTrigger.Disabled` e impostandola su `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Per riabilitare la funzione, eseguire di nuovo lo stesso comando con il valore `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Il [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) comando aggiunge o aggiorna un'impostazione dell'applicazione. Il comando seguente disabilita una funzione denominata creando `QueueTrigger` un'impostazione dell'app denominata `AzureWebJobs.QueueTrigger.Disabled` e impostandola su `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Per riabilitare la funzione, eseguire di nuovo lo stesso comando con il valore `false`.

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funzioni in uno slot

Per impostazione predefinita, le impostazioni dell'app si applicano anche alle app in esecuzione negli slot di distribuzione. È tuttavia possibile eseguire l'override dell'impostazione dell'app usata dallo slot impostando un'impostazione dell'app specifica dello slot. Ad esempio, potrebbe essere necessario che una funzione sia attiva nell'ambiente di produzione, ma non durante i test di distribuzione, ad esempio una funzione attivata dal timer. 

Per disabilitare una funzione solo nello slot di staging:

# <a name="portal"></a>[Portale](#tab/portal)

Passare all'istanza di slot dell'app per le funzioni selezionando Slot **di** distribuzione in **Distribuzione**, scegliendo lo slot e selezionando **Funzioni** nell'istanza dello slot.  Scegliere la funzione, quindi usare i pulsanti **Abilita** **e Disabilita** nella pagina **Panoramica della** funzione. Questi pulsanti funzionano modificando il valore `AzureWebJobs.<FUNCTION_NAME>.Disabled` dell'impostazione dell'app. Questa impostazione specifica della funzione viene creata la prima volta che viene disabilitata. 

È anche possibile aggiungere direttamente l'impostazione dell'app `AzureWebJobs.<FUNCTION_NAME>.Disabled` denominata con il valore di nella configurazione `true` **per** l'istanza dello slot. Quando si aggiunge un'impostazione dell'app specifica dello slot, assicurarsi di selezionare la **casella Impostazione slot di** distribuzione. In questo modo viene mantenuto il valore dell'impostazione con lo slot durante gli scambi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Per riabilitare la funzione, eseguire di nuovo lo stesso comando con il valore `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell attualmente non supporta questa funzionalità.

---

Per altre informazioni, vedere [Funzioni di Azure slot di distribuzione.](functions-deployment-slots.md)

## <a name="localsettingsjson"></a>local.settings.json

Le funzioni possono essere disabilitate nello stesso modo quando vengono eseguite in locale. Per disabilitare una funzione denominata , aggiungere una voce alla raccolta `HttpExample` Values nella local.settings.jsnel file , come indicato di seguito:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Altri metodi

Sebbene sia consigliabile usare l'impostazione dell'applicazione per tutti i linguaggi e per tutte le versioni di runtime, è possibile disabilitare le funzioni in diversi altri modi. Questi metodi, che variano in base al linguaggio e alla versione di runtime, vengono mantenuti per compatibilità con le versioni precedenti. 

### <a name="c-class-libraries"></a>Libreria di classi C#

In una funzione di libreria di classi, è anche possibile usare l'attributo `Disable` per impedire che la funzione venga attivata. Questo attributo consente di personalizzare il nome dell'impostazione usata per disabilitare la funzione. Usare la versione dell'attributo che consente di definire un parametro del costruttore che fa riferimento a un'impostazione dell'app booleana, come illustrato nell'esempio seguente:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Questo metodo consente di abilitare e disabilitare la funzione modificando l'impostazione dell'app, senza ricompilare o ridistribuire il progetto. Se si modifica un'impostazione, l'app per le funzioni viene riavviata e la modifica dello stato disabilitato viene rilevata immediatamente.

Esiste anche un costruttore per il parametro che non accetta una stringa per il nome dell'impostazione. Questa versione dell'attributo non è consigliata. Se si usa questa versione, è necessario ricompilare e ridistribuire il progetto per modificare lo stato disabilitato della funzione.

### <a name="functions-1x---scripting-languages"></a>Funzioni 1.x: linguaggi di scripting

Nella versione 1.x è anche possibile usare la proprietà `disabled` del file *function.json* per indicare al runtime di non attivare una funzione. Questo metodo funziona solo per i linguaggi di scripting, ad esempio C# e JavaScript. La proprietà `disabled` può essere impostata su `true` o sul nome di un'impostazione dell'app:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
o 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Nel secondo esempio la funzione viene disabilitata se è presente un'impostazione dell'app denominata IS_DISABLED che è impostata su `true` o 1.

>[!IMPORTANT]  
>Il portale usa le impostazioni dell'applicazione per disabilitare le funzioni v1.x. Quando l'impostazione di un'applicazione è in conflitto con il file function.json, è possibile che si verifichi un errore. Per evitare errori, è necessario rimuovere la proprietà `disabled` dal file function.json. 


## <a name="next-steps"></a>Passaggi successivi

Questo articolo contiene informazioni sulla disabilitazione dei trigger automatici. Per altre informazioni, vedere [Trigger e associazioni](functions-triggers-bindings.md).
