---
title: Creare un'app del servizio app usando un modello di Azure Resource Manager
description: Creare la prima app nel servizio app di Azure in pochi secondi usando un modello di Azure Resource Manager, uno dei molti modi per eseguire la distribuzione nel servizio app.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6e0368abcca5f0c38a7b143386d666e7908f30b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748806"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Avvio rapido: Creare un'app del servizio app usando un modello di Resource Manager

Per iniziare a usare il [servizio app Azure](overview.md) , distribuire un'app nel cloud usando un <abbr title="Un file JSON che definisce in modo dichiarativo una o più risorse e dipendenze di Azure tra le risorse distribuite. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto.">Modello ARM</abbr> e l'interfaccia della riga di comando di [Azure](/cli/azure/get-started-with-azure-cli) in cloud Shell. Poiché si usa un livello di servizio app gratuito, non verrà addebitato alcun costo per il completamento di questa guida di avvio rapido. <abbr title="Nella sintassi dichiarativa si descrive la distribuzione prevista senza scrivere la sequenza di comandi di programmazione necessari per creare la distribuzione.">Il modello usa la sintassi dichiarativa.</abbr>

 Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei [modelli ARM](../azure-resource-manager/templates/overview.md), selezionare il pulsante **Distribuisci in Azure** . Il modello verrà aperto nel portale di Azure.

::: zone pivot="platform-windows"
[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. esaminare il modello

::: zone pivot="platform-windows"
Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Distribuisce un piano di servizio app e un'app del servizio app in Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Quali risorse e parametri sono definiti nel modello?</summary>

Nel modello sono definite due risorse di Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): per creare un piano di servizio app.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): per creare un'app del servizio app.

La tabella seguente illustra i parametri predefiniti e le relative descrizioni:

| Parametri | Type    | Valore predefinito                | DESCRIZIONE |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome app |
| posizione   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Area dell'app |
| sku        | string  | "F1"                         | Dimensione delle istanze (F1 = livello gratuito) |
| Linguaggio   | string  | ".net"                       | Stack del linguaggio di programmazione (.net, php, node, html) |
| helloWorld | boolean | False                        | True = distribuire l'app "Hello World |
| repoUrl    | string  | " "                          | Repository Git esterno (facoltativo) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Distribuisce un piano di servizio app e un'app del servizio app in Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Questo modello include le risorse e i parametri di Azure definiti per praticità.

<details>
<summary>Quali risorse e parametri sono definiti nel modello?</summary>

Nel modello sono definite due risorse di Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): per creare un piano di servizio app.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): per creare un'app del servizio app.

La tabella seguente illustra i parametri predefiniti e le relative descrizioni:

| Parametri | Type    | Valore predefinito                | DESCRIZIONE |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome app |
| posizione   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Area dell'app |
| sku        | string  | "F1"                         | Dimensione delle istanze (F1 = livello gratuito) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Stack del linguaggio di programmazione &#124; Versione" |
| repoUrl    | string  | " "                          | Repository Git esterno (facoltativo) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. distribuire il modello

::: zone pivot="platform-windows"
Eseguire il codice seguente per distribuire un'app .NET Framework in Windows usando l'interfaccia della riga di comando di Azure. 

Sostituisci <abbr title="I caratteri validi sono `a-z` , `0-9` e `-` .">`<app-name>`</abbr> con un nome di app univoco globale. Per altre informazioni <abbr title="È anche possibile usare il portale di Azure, Azure PowerShell e l'API REST.">metodi di distribuzione</abbr>, vedere [deploy templates](../azure-resource-manager/templates/deploy-powershell.md). È possibile trovare altri [esempi di modelli del Servizio app di Azure qui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Eseguire il codice seguente per creare un'app Python in Linux. 

Sostituisci <abbr title="I caratteri validi sono `a-z` , `0-9` e `-` .">`<app-name>`</abbr> con un nome di app univoco globale.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Quali sono le operazioni del codice?</summary>
<p>I comandi eseguono le azioni seguenti:</p>
<ul>
<li>Crea un valore predefinito <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr>.</li>
<li>Crea un valore predefinito <abbr title="Il piano che specifica la posizione, le dimensioni e le funzionalità del server farm Web che ospita l'app.">Piano di servizio app</abbr>.</li>
<li><a href="/cli/azure/webapp?view=azure-cli-latest#az-webapp-create">Creazione di un <abbr title="La rappresentazione dell'app Web, che contiene il codice dell'app, i nomi host DNS, i certificati e le risorse correlate. "> App del servizio app</abbr></a> con il nome specificato.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Ricerca per categorie distribuire uno stack di linguaggi diversi?</summary>
Per distribuire uno stack di lingue diverso, aggiornare <abbr title="Questo modello è compatibile con le app .NET Core, .NET Framework, PHP, Node.js e HTML statiche. "> parametro Language</abbr> con i valori appropriati. Per Java, vedere <a href="/azure/app-service/quickstart-java-uiex">Creare un'app Java</a>.

| Parametri | Type    | Valore predefinito                | Descrizione |
|------------|---------|------------------------------|-------------|
| Linguaggio   | string  | ".net"                       | Stack del linguaggio di programmazione (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Ricerca per categorie distribuire uno stack di linguaggi diversi?</summary>
Per distribuire uno stack di linguaggio diverso, aggiornare `linuxFxVersion` con i valori appropriati. Di seguito sono riportati alcuni esempi. Per visualizzare le versioni correnti, eseguire questo comando in Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Linguaggio    | Esempio:                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. convalidare la distribuzione

Passare a `http://<app_name>.azurewebsites.net/` e verificare che la creazione sia stata completata.

<hr/>

## <a name="5-clean-up-resources"></a>5. pulire le risorse

Quando non è più necessario, [eliminare il gruppo di risorse](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Passaggi successivi

- [](deploy-local-git.md)Distribuire dall'archivio Git locale
- [ASP.NET Core con database SQL](tutorial-dotnetcore-sqldb-app.md)
- Python con Postgres
- [PHP con MySQL](tutorial-php-mysql-app.md)
- [Connettersi al database SQL di Azure con Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Eseguire il mapping di un dominio personalizzato](app-service-web-tutorial-custom-domain-uiex.md)

