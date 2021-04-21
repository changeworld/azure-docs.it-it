---
title: "Guida introduttiva: Creare un'app Web HTML statica"
description: Distribuire la prima app Hello World HTML nel servizio app di Azure in pochi minuti. A questo scopo si usa Git, uno dei molti strumenti disponibili per eseguire distribuzioni nel servizio app.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768908"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Creare un'app Web HTML statica in Azure

Questa guida introduttiva illustra come distribuire un sito HTML+CSS di base in <abbr title="Servizio basato su HTTP per l'hosting di applicazioni Web, API REST e applicazioni back-end per dispositivi mobili.">Servizio app di Azure</abbr>. Questa guida introduttiva verrà completata in [Cloud Shell](../cloud-shell/overview.md), ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

In [Cloud Shell](../cloud-shell/overview.md)creare una directory di avvio rapido e quindi modificarla.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Creare un'app Web

Passare alla directory contenente il codice di esempio ed eseguire il comando `az webapp up`. **Sostituire** `<app-name>` con un nome univoco globale.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Risoluzione dei problemi</summary>
<ul>
<li>Se il comando non viene riconosciuto, assicurarsi di avere installato l'interfaccia della riga di comando di <code>az</code> Azure come descritto in Preparare <a href="#1-prepare-your-environment">l'ambiente</a>.</li>
<li>Sostituire con un nome univoco in tutto <code>&lt;app-name&gt;</code> Azure ( i caratteri validi sono , e <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.</li>
<li>Con l'argomento <code>--sku F1</code> l'app Web viene creata nel piano tariffario Gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.</li>
<li><code>--html</code>L'argomento indica di considerare tutto il contenuto della cartella come contenuto statico e di disabilitare l'automazione della compilazione.</li>
<li>Facoltativamente, è possibile includere l'argomento <code>--location &lt;location-name&gt;</code>, dove <code>&lt;location-name&gt;</code> è un'area di Azure disponibile. È possibile recuperare un elenco di aree consentite per l'account Azure eseguendo il <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> comando .</li>
</ul>
</details>

Il completamento del comando può richiedere alcuni minuti. 

<details>
<summary>Che cosa <code>az webapp up</code> sta facendo?</summary>
<p>Il comando <code>az webapp up</code> esegue le azioni seguenti:</p>
<ul>
<li>Crea un gruppo di risorse predefinito.</li>
<li>Creare un piano di servizio app predefinito.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Creare un'app del servizio app</a> con il nome specificato.</li>
<li><a href="/azure/app-service/deploy-zip">Distribuire file ZIP</a> i dalla directory di lavoro corrente all'app.</li>
<li>Durante l'esecuzione, fornisce messaggi relativi alla creazione, alla registrazione e alla distribuzione ZIP delle risorse.</li>
</ul>

Al termine, vengono visualizzate informazioni simili all'esempio seguente:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Sarà necessario il valore `resourceGroup` per pulire le risorse in un [secondo](#6-clean-up-resources) momento.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Passare all'app

In un browser passare all'URL dell'app: `http://<app_name>.azurewebsites.net`.

La pagina è in esecuzione come un'app Web del servizio app di Azure.

![Home page dell'app di esempio](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Aggiornare e ridistribuire l'app

Nella casella Cloud Shell **digitare per** aprire `nano index.html` l'editor di testo nano. 

Nel `<h1>` tag di intestazione modificare "Servizio app di Azure - Sito HTML statico di esempio" in "Servizio app di Azure".

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Salvare** le modifiche usando il comando `^O` .

**Uscire da** nano usando il comando `^X` .

Ridistribuire l'app con `az webapp up` il comando .

```bash
az webapp up --html
```

Tornare alla finestra del browser aperta nel **passaggio Passare all'app.**

**Aggiornare** la pagina.

![Home page dell'app di esempio aggiornata](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Gestire la nuova app di Azure

**Passare** al [portale di Azure](https://portal.azure.com). 

**Cercare** e selezionare **Servizi** **app.**

![Selezionare Servizi app nel portale di Azure](./media/quickstart-html/portal0.png)

**Selezionare** il nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/quickstart-html/portal1.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app.

![Pannello del servizio app nel portale di Azure](./media/quickstart-html/portal2.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app.

<hr/>

## <a name="6-clean-up-resources"></a>6. Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse eseguendo questo comando in Cloud Shell. Si ricorda che il nome del gruppo di risorse è stato generato automaticamente nel passaggio [Creare un'app Web](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

L'esecuzione del comando può richiedere un minuto.

<hr/>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire il mapping di un dominio personalizzato](app-service-web-tutorial-custom-domain-uiex.md)
