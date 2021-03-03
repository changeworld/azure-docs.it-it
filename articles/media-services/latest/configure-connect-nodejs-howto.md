---
title: Connettersi all'API di servizi multimediali di Azure V3-Node.js
description: Questo articolo illustra come connettersi all'API di servizi multimediali V3 con Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: ab0113823bb5751828a71a9afd8c474091272e16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724626"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi all'API di servizi multimediali V3-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come connettersi a servizi multimediali di Azure V3 node.js SDK usando il metodo di accesso dell'entità servizio. Si utilizzeranno i file nel repository di esempi di *Media-Services-V3-node-Tutorials* . L'esempio *HelloWorld-ListAssets* contiene il codice per la connessione e l'elenco degli asset nell'account.

## <a name="prerequisites"></a>Prerequisiti

- Un'installazione di Visual Studio Code.
- Installare [Node.js](https://nodejs.org/en/download/).
- Installare [typescript](https://www.typescriptlang.org/download).
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e quello dell'account di Servizi multimediali.
- Creare un'entità servizio per l'applicazione. Vedere [accedere alle API](./access-api-howto.md).<br/>**Suggerimento Pro.** Mantieni questa finestra aperta o copia tutti gli elementi nella scheda JSON nel blocco note. 
- Assicurarsi di ottenere la versione più recente di [AZUREMEDIASERVICES SDK per JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Esaminare le [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions) di servizi multimediali di Azure per comprendere le principali restrizioni di denominazione sulle entità.

## <a name="clone-the-nodejs-samples-repo"></a>Clonare il repository di Node.JS Samples

Si utilizzeranno alcuni file in esempi di Azure. Clonare il repository Node.JS Samples.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Installare i pacchetti

### <a name="install-azurearm-mediaservices"></a>Installare @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Installare @azure/ms-rest-nodeauth

Installare la versione minima di " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Per questo esempio, nel file si useranno i pacchetti seguenti `package.json` .

|Pacchetto|Descrizione|
|---|---|
|`@azure/arm-mediaservices`|SDK di Servizi multimediali di Azure. <br/>Per assicurarsi di usare il pacchetto di servizi multimediali di Azure più recente, controllare l' [installazione @azure/arm-mediaservices di NPM ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Obbligatorio per l'autenticazione AAD con l'entità servizio o l'identità gestita|
|`@azure/storage-blob`|SDK di archiviazione. Usato quando si caricano file in asset.|
|`@azure/ms-rest-js`| Usato per l'accesso.|
|`@azure/storage-blob` | Consente di caricare e scaricare file in asset in servizi multimediali di Azure per la codifica.|
|`@azure/abort-controller`| Usato insieme al client di archiviazione per il timeout delle operazioni di download con esecuzione prolungata|

### <a name="create-the-packagejson-file"></a>Creare il package.jsnel file

1. Creare un `package.json` file con l'editor preferito.
1. Aprire il file e incollare il codice seguente:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Connettersi a Node.js client usando TypeScript



### <a name="sample-env-file"></a>File con *estensione ENV* di esempio

Copiare il contenuto del file in un file denominato *. env*. Deve essere archiviata nella radice del repository funzionante. Questi sono i valori ottenuti dalla pagina di accesso all'API per l'account servizi multimediali nel portale.

Dopo aver creato il file con *estensione ENV* , è possibile iniziare a usare gli esempi.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Eseguire l'applicazione di esempio *HelloWorld-ListAssets*

1. Modificare la directory nella cartella *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Installare i pacchetti utilizzati nella *packages.jssu* file.

```
npm install 
```

3. Modificare la directory nella cartella *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Avviare Visual Studio Code dalla cartella AMSv3Samples. Questa operazione è necessaria per l'avvio dalla cartella in cui si trovano la cartella ". VSCODE" e tsconfig.jsnei file

```dotnetcli
cd ..
code .
```

Aprire la cartella per *HelloWorld-ListAssets* e aprire il file *index. ts* nell'editor Visual Studio Code.

Nel file *index. TS* , premere F5 per avviare il debugger. Verrà visualizzato un elenco di asset visualizzati se si dispone già di risorse nell'account. Se l'account è vuoto, verrà visualizzato un elenco vuoto.  

Per visualizzare rapidamente le risorse elencate, usare il portale per caricare alcuni file video. Gli asset verranno creati automaticamente ogni volta e l'esecuzione di questo script restituirà i rispettivi nomi.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Approfondimenti sull'esempio *HelloWorld-ListAssets*

L'esempio *HelloWorld-ListAssets* illustra come connettersi al client di servizi multimediali con un'entità servizio ed elencare gli asset nell'account. Per una spiegazione dettagliata delle operazioni svolte, vedere i commenti nel codice.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Altri esempi

Gli esempi seguenti sono disponibili nel [repository](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Project name (Nome progetto)|Caso d'uso|
|---|---|
|Live/index. TS| Esempio di streaming live di base. Assicurarsi di verificare che tutte le risorse siano state pulite e che non vengano più fatturate nel portale quando si usa Live|
|StreamFilesSample/index. TS| Esempio di base per il caricamento di un file locale o la codifica da un URL di origine. Esempio che illustra come usare Storage SDK per scaricare il contenuto e Mostra come eseguire lo streaming in un lettore |
|StreamFilesWithDRMSample/index. TS| Viene illustrato come codificare e trasmettere in flusso tramite Widevine e PlayReady DRM |
|VideoIndexerSample/index. TS| Esempio di uso dei set di impostazioni video e Audio Analyzer per generare metadati e informazioni dettagliate da un file video o audio |

## <a name="see-also"></a>Vedi anche

- [Documentazione di riferimento per i moduli di servizi multimediali di Azure per Node.js](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)
- [Azure per & JavaScript Node.js sviluppatori](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository dell'hub git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentazione dei pacchetti di Azure per sviluppatori Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)
- [Concetti relativi ai Servizi multimediali](concepts-overview.md)
- [installazione di NPM @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure per & JavaScript Node.js sviluppatori](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione [Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.
