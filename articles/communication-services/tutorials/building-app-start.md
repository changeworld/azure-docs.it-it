---
title: Esercitazione-preparare un'app Web per i servizi di comunicazione di Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Informazioni su come creare un'applicazione Web di base che supporta i servizi di comunicazione di Azure
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a5a23d6a06c8cdff4deabac5251597b7ffe0c833
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728046"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Esercitazione: preparare un'app Web per i servizi di comunicazione di Azure (Node.js)

È possibile usare i servizi di comunicazione di Azure per aggiungere comunicazioni in tempo reale alle applicazioni. In questa esercitazione si apprenderà come configurare un'applicazione Web che supporta i servizi di comunicazione di Azure. Si tratta di un'esercitazione introduttiva per i nuovi sviluppatori che desiderano iniziare a usare le comunicazioni in tempo reale.

Al termine di questa esercitazione, si disporrà di un'applicazione Web di base configurata con gli SDK di servizi di comunicazione di Azure. È quindi possibile usare tale applicazione per iniziare a compilare la soluzione di comunicazione in tempo reale.

Per inviare commenti e suggerimenti, visitare la [pagina di GitHub servizi di comunicazione di Azure](https://github.com/Azure/communication) .

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Configurare l'ambiente di sviluppo.
> * Configurare un server Web locale.
> * Aggiungere i pacchetti di servizi di comunicazione Azure al sito Web.
> * Pubblicare il sito Web in siti web statici di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). L'account gratuito ti offre $200 di crediti di Azure per provare qualsiasi combinazione di servizi.
- [Visual Studio Code](https://code.visualstudio.com/) per modificare il codice nell'ambiente di sviluppo locale.
- [Webpack](https://webpack.js.org/) per aggregare e ospitare localmente il codice.
- [Node.js](https://nodejs.org/en/) l'installazione e la gestione delle dipendenze come Azure Communication Services SDK e Webpack.
- [NVM e NPM](/windows/nodejs/setup-on-windows) per gestire il controllo della versione.
- [Estensione di archiviazione di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) per Visual Studio Code. Questa estensione è necessaria per pubblicare l'applicazione in archiviazione di Azure. [Altre informazioni sull'hosting di siti web statici in archiviazione di Azure](../../storage/blobs/storage-blob-static-website.md).
- L'[estensione Servizio app di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). L'estensione consente la distribuzione di siti Web con la possibilità di configurare l'integrazione continua completamente gestita e la distribuzione continua (CI/CD).
- [Estensione funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per creare applicazioni senza server personalizzate. Ad esempio, è possibile ospitare l'applicazione di autenticazione in funzioni di Azure.
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Informazioni su come creare una risorsa di servizi di comunicazione](../quickstarts/create-communication-resource.md).
- Un token di accesso utente. Per istruzioni, vedere la [Guida introduttiva per la creazione e la gestione dei token di accesso](../quickstarts/access-tokens.md?pivots=programming-language-javascript) o l'esercitazione per la creazione di [un servizio di autenticazione trusted](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Configurare l'ambiente di sviluppo

L'ambiente di sviluppo locale verrà configurato come segue:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagramma che illustra l'architettura dell'ambiente di sviluppo.":::


### <a name="install-nodejs-nvm-and-npm"></a>Installare Node.js, NVM e NPM

Si userà Node.js per scaricare e installare diverse dipendenze necessarie per l'applicazione sul lato client. Verrà usato per generare file statici che verranno quindi ospitati in Azure, quindi non è necessario preoccuparsi di configurarli nel server.

Gli sviluppatori di Windows possono seguire [questa Node.js esercitazione](/windows/nodejs/setup-on-windows) per configurare node, NVM e NPM. 

Questa esercitazione è basata sulla versione LTS 12.20.0. Dopo aver installato NVM, usare il comando di PowerShell seguente per distribuire la versione che si vuole usare:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Screenshot che mostra i comandi per la distribuzione di una versione del nodo.":::

### <a name="configure-visual-studio-code"></a>Configurare Visual Studio Code

È possibile scaricare [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Creare un'area di lavoro per i progetti di servizi di comunicazione di Azure

Creare una cartella per archiviare i file di progetto, come segue: `C:\Users\Documents\ACS\CallingApp` . In Visual Studio Code selezionare **file**  >  **Aggiungi cartella all'area di lavoro** e aggiungere la cartella all'area di lavoro.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Screenshot che mostra le selezioni per l'aggiunta di un file a un'area di lavoro.":::

Passare a **Esplora risorse** nel riquadro a sinistra per visualizzare la `CallingApp` cartella nell'area di lavoro senza **titolo** .

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Screenshot che Mostra Esplora risorse e l'area di lavoro senza titolo.":::

È possibile aggiornare il nome dell'area di lavoro. È possibile convalidare la versione del Node.js facendo clic con il pulsante destro del mouse sulla `CallingApp` cartella e selezionando **Apri in terminale integrato**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Screenshot che mostra la selezione per l'apertura di una cartella in un terminale integrato.":::

Nel terminale immettere il comando seguente per convalidare la versione del Node.js installata nel passaggio precedente:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Screenshot che mostra la convalida della versione del nodo.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Installare le estensioni di Azure per Visual Studio Code

Installare l' [estensione di archiviazione di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) tramite Visual Studio Marketplace o Visual Studio Code (**visualizzare** le  >  **estensioni**  >  **archiviazione di Azure**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Screenshot che mostra il pulsante per l'installazione dell'estensione di archiviazione di Azure.":::

Seguire la stessa procedura per [funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) e le estensioni del [servizio app Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-web-server"></a>Configurare un server Web locale

### <a name="create-an-npm-package"></a>Creazione di un pacchetto NPM

Nel terminale, dal percorso della cartella dell'area di lavoro, immettere:

``` console
npm init -y
```

Questo comando Inizializza un nuovo pacchetto NPM e `package.json` lo aggiunge alla cartella radice del progetto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Screenshot che mostra il pacchetto J S O N.":::

Per ulteriori informazioni su `npm init` , vedere la pagina relativa ai [documenti NPM per il comando](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Installare Webpack

È possibile usare [Webpack](https://webpack.js.org/) per aggregare il codice in file statici che è possibile distribuire in Azure. Dispone inoltre di un server di sviluppo che verrà configurato per l'utilizzo con l'esempio chiamante.

Nel terminale immettere il comando seguente per installare Webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Questa esercitazione è stata testata con le versioni specificate nel comando precedente. Specificando `-dev` viene indicato al gestore di pacchetti che questa dipendenza è a scopo di sviluppo e non deve essere inclusa nel codice distribuito in Azure.

Verranno visualizzati due nuovi pacchetti aggiunti al `package.json` file come `devDependencies` . I pacchetti verranno installati nella `./CallingApp/node_modules/` Directory.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Screenshot che mostra la configurazione di Webpack.":::

### <a name="configure-the-development-server"></a>Configurare il server di sviluppo

L'esecuzione di un'applicazione statica, come il `index.html` file, dal browser usa il `file://` protocollo. Per il corretto funzionamento dei moduli NPM, è necessario il protocollo HTTP usando Webpack come server di sviluppo locale.

Verranno create due configurazioni: una per lo sviluppo e l'altra per la produzione. I file preparati per la produzione saranno minimizzati, ovvero verranno rimossi gli spazi vuoti e i caratteri non usati. Questa configurazione è adatta per scenari di produzione in cui la latenza deve essere ridotta a icona o in cui il codice deve essere offuscato.

Lo strumento verrà usato `webpack-merge` per lavorare con [file di configurazione diversi per Webpack](https://webpack.js.org/guides/production/).

Iniziamo con l'ambiente di sviluppo. Prima di tutto, è necessario installare `webpack merge` . Nel terminale eseguire il comando seguente:

```Console
npm install --save-dev webpack-merge
```

Nel `package.json` file è possibile visualizzare un'altra dipendenza aggiunta a `devDependencies` .

Successivamente, creare un file denominato `webpack.common.js` e aggiungere il codice seguente:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Aggiungere quindi altri due file, uno per ogni configurazione:

* `webpack.dev.js`
* `webpack.prod.js`

A questo punto, modificare il `webpack.dev.js` file aggiungendovi il codice seguente:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
In questa configurazione è possibile importare parametri comuni da `webpack.common.js` , unire i due file, impostare la modalità su `development` e configurare la mappa di origine come `inline-source-map` .

La modalità di sviluppo indica a Webpack di non minimizzare i file e non producono file di produzione ottimizzati. Per informazioni dettagliate sulla modalità Webpack, vedere la [pagina Web relativa alla modalità Webpack](https://webpack.js.org/configuration/mode/).

Le opzioni della mappa di origine sono elencate nella [pagina Web devtool di Webpack](https://webpack.js.org/configuration/devtool/#root). L'impostazione della mappa di origine rende più semplice eseguire il debug tramite il browser.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Screenshot che mostra il codice per la configurazione di Webpack.":::

Per eseguire il server di sviluppo, passare a `package.json` e aggiungere il codice seguente in `scripts` :

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Il file dovrebbe essere simile al seguente:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

È stato aggiunto il comando che può essere usato da NPM.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Screenshot che mostra la modifica di package.js.":::

### <a name="test-the-development-server"></a>Testare il server di sviluppo

 In Visual Studio Code creare tre file nel progetto:

* `index.html`
* `app.js`
* `app.css` (facoltativo, per definire lo stile dell'app)

Incollare il codice seguente in `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Screenshot che mostra il file H T M.":::

Aggiungere il codice seguente a `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Aggiungere il codice seguente a `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```
Non dimenticare di salvare. Il file non salvato è indicato dai puntini bianchi accanto ai nomi di file nella finestra di esplorazione.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Screenshot che mostra il file di App.js con codice JavaScript.":::

Quando si apre questa pagina, viene visualizzato il messaggio con un avviso nella console del browser.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Screenshot che mostra il file app. CSS.":::

Usare il comando terminale seguente per testare la configurazione di sviluppo:

```Console
npm run build:dev
```

Nella console viene visualizzato il percorso in cui è in esecuzione il server. Per impostazione predefinita, è `http://localhost:8080` . Il `build:dev` comando è il comando aggiunto in `package.json` precedenza.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Screenshot che mostra l'avvio di un server di sviluppo.":::
 
Passare all'indirizzo nel browser per visualizzare la pagina e l'avviso configurati nei passaggi precedenti.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Screenshot della pagina H T M.":::
  
 
Mentre il server è in esecuzione, è possibile modificare il codice e il server. La pagina HTML verrà ricaricata automaticamente. 

Passare quindi al `app.js` file in Visual Studio Code ed eliminare `alert('Hello world alert!');` . Salvare il file e verificare che l'avviso scompaia dal browser.

Per arrestare il server, è possibile eseguire `Ctrl+C` nel terminale. Per avviare il server, immettere `npm run build:dev` in qualsiasi momento.

## <a name="add-the-azure-communication-services-packages"></a>Aggiungere i pacchetti di servizi di comunicazione Azure

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano SDK per JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Questa azione aggiunge i servizi di comunicazione di Azure comuni e chiama i pacchetti come dipendenze del pacchetto. Verranno visualizzati due nuovi pacchetti aggiunti al `package.json` file. Per ulteriori informazioni su, vedere `npm install` la pagina relativa ai [documenti NPM per il comando](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Screenshot che mostra il codice per l'installazione dei pacchetti di servizi di comunicazione Azure.":::

Questi pacchetti sono forniti dal team di servizi di comunicazione di Azure e includono le librerie di autenticazione e chiamata. Il `--save` comando segnala che l'applicazione dipende da questi pacchetti per l'uso in ambiente di produzione e sarà inclusa in `devDependencies` all'interno del `package.json` file. Quando si compila l'applicazione per la produzione, i pacchetti verranno inclusi nel codice di produzione.


## <a name="publish-your-website-to-azure-static-websites"></a>Pubblicare il sito Web in siti web statici di Azure

### <a name="create-a-configuration-for-production-deployment"></a>Creare una configurazione per la distribuzione di produzione

Aggiungere il codice seguente a `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Questa configurazione verrà unita a `webpack.common.js` (in cui è stato specificato il file di input e dove archiviare i risultati). La configurazione imposta anche la modalità su `production` .
 
In `package.json` aggiungere il codice seguente:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

Il file avrà un aspetto simile al seguente:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Screenshot che mostra i file configurati.":::


Nel terminale eseguire:

```Console
npm run build:prod
```

Il comando crea una `dist` cartella e un `app.js` file statico pronto per l'ambiente di produzione. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Screenshot che mostra la compilazione di produzione.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Distribuire l'app in archiviazione di Azure

Copiare `index.html` e nella `app.css` `dist` cartella.

Nella `dist` cartella creare un file e denominarlo `404.html` . Copiare il markup seguente nel file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Salvare il file (CTRL + S).

Fare clic con il pulsante destro del mouse sulla `dist` cartella e scegliere **Distribuisci in sito Web statico tramite archiviazione di Azure**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Screenshot delle selezioni per avviare la distribuzione in Azure.":::
 
In **Seleziona sottoscrizione** selezionare **Accedi ad Azure** oppure **creare un account Azure gratuito** se non è già stata creata una sottoscrizione.
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Screenshot che mostra le selezioni per l'accesso ad Azure.":::
 
Selezionare **Crea nuovo account di archiviazione**  >  **Avanzate**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Screenshot che mostra le selezioni per la creazione del gruppo di account di archiviazione.":::
 
Consente di specificare il nome del gruppo di archiviazione.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Screenshot che mostra l'aggiunta di un nome per l'account.":::
 
Se necessario, creare un nuovo gruppo di risorse.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Screenshot che mostra la selezione per la creazione di un nuovo gruppo di risorse.":::
  
Per **abilitare l'hosting di siti web statici**, selezionare **Sì**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Screenshot che Mostra come selezionare l'opzione per abilitare l'hosting di siti web statici.":::
  
Per **immettere il nome del documento di indice**, accettare il nome file predefinito. Il file è già stato creato `index.html` .

Per **immettere il percorso del documento di errore 404**, immettere **404.html**.  
  
Selezionare il percorso dell'applicazione. Il percorso selezionato definirà il processore di contenuti multimediali che verrà usato nell'applicazione chiamante futura nelle chiamate di gruppo. 

I servizi di comunicazione di Azure selezionano il processore di contenuti multimediali in base al percorso dell'applicazione.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Screenshot che mostra un elenco di percorsi.":::
  
Attendere che la risorsa e il sito Web siano stati creati. 
 
Selezionare **Sfoglia al sito Web**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Screenshot che mostra un messaggio che indica che la distribuzione è stata completata, con il pulsante per l'esplorazione di un sito Web.":::
 
Dagli strumenti di sviluppo del browser è possibile esaminare l'origine e visualizzare il file preparato per la produzione.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Screenshot dell'origine del sito Web con file.":::

Passare alla [portale di Azure](https://portal.azure.com/#home), selezionare il gruppo di risorse e selezionare l'applicazione creata. Quindi selezionare **Impostazioni**  >  **sito Web statico**. È possibile vedere che i siti web statici sono abilitati. Si noti l'endpoint primario, il nome del documento di indice e il percorso del documento di errore.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Screenshot che mostra la selezione del sito Web statica.":::

Selezionare **Contenitori** in **Servizio BLOB**. L'elenco Mostra due contenitori creati, uno per i log ( `$logs` ) e uno per il contenuto del sito Web ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Screenshot che mostra la configurazione del contenitore.":::

Se si apre il `$web` contenitore, verranno visualizzati i file creati in Visual Studio e distribuiti in Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Screenshot che mostra i file distribuiti in Azure.":::

È possibile ridistribuire l'applicazione da Visual Studio Code in qualsiasi momento.

A questo punto si è pronti per creare la prima applicazione Web servizi di comunicazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere chiamate vocali all'app](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Potrebbe inoltre essere necessario:

- [Aggiungere una chat all'app](../quickstarts/chat/get-started.md)
- [Creare token di accesso utente](../quickstarts/access-tokens.md)
- [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
- [Informazioni sull'autenticazione](../concepts/authentication.md)
