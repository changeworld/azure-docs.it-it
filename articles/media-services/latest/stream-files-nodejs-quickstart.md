---
title: Trasmettere file video con servizi multimediali di Azure-Node.js
description: Seguire i passaggi di questa esercitazione per creare un nuovo account di Servizi multimediali di Azure, codificare un file ed eseguirne lo streaming su Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, streaming
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 0eb334111a8f5ffc63d0f858966e4e65f99d3b16
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095961"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questa esercitazione illustra quanto sia facile codificare e iniziare a eseguire lo streaming di video su un'ampia gamma di browser e dispositivi tramite Servizi multimediali di Azure. È possibile specificare un file video di input usando URL HTTPS, URL di firma di accesso condiviso o percorsi per i file che si trovano nell'archiviazione BLOB di Azure.

L'esempio in questo articolo codifica contenuti resi accessibili tramite un URL HTTPS. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS.

Al termine dell'esercitazione si saprà come caricare, codificare e trasmettere in streaming un video usando un lettore di client HLS o DASH.

![Riprodurre il video](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/)
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).<br/>Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire la procedura descritta in [Accedere all'API di Servizi multimediali di Azure usando l'interfaccia della riga di comando di Azure](./access-api-howto.md) e salvare le credenziali. Sarà necessario usarle per accedere all'API.
- Esaminare le procedure per la [configurazione e la connessione Node.js](./configure-connect-nodejs-howto.md) per comprendere come usare l'SDK del client di Node.js

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio Node.js di streaming usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

L'esempio è disponibile nella cartella [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Aprire [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) nel progetto scaricato. Aggiornare il file Sample. env nella cartella radice con i valori e le credenziali ottenuti dall' [accesso alle API](./access-api-howto.md). Rinominare il file con estensione ENV di esempio in. env. 

L'esempio esegue le azioni seguenti:

1. Crea una **trasformazione** (in primo luogo, controlla se esiste la trasformazione specificata). 
2. Crea un **asset** di output usato come output del **processo** di codifica.
1. Facoltativamente, carica un file locale usando storage BLOB SDK.
1. Crea l'input del **processo** in base a un URL HTTPS o a un file caricato
1. Invia il **processo** di codifica con il [set di impostazioni di codifica compatibile](./content-aware-encoding.md)con il contenuto, usando l'input e l'output creati in precedenza.
1. Controlla lo stato del processo.
1. Scarica l'output del processo di codifica in una cartella locale.
1. Consente di creare un **localizzatore di streaming** da usare nel lettore.
1. Compila gli URL di streaming per HLS e DASH.
1. Riproduce il contenuto in un'applicazione Player-Azure Media Player.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

1. L'app scarica file codificati. Creare una cartella in cui si desidera che i file di output vengano aggiornati e aggiornare il valore della variabile **CartellaOutput** nel file [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) . Per impostazione predefinita, è impostato su "Temp".
1. Aprire un **prompt dei comandi**, passare alla directory dell'esempio ed eseguire i comandi seguenti.
1. Modificare la directory nella cartella AMSv3Samples
    ```bash
    cd AMSv3Samples
    ```

1. Installare i pacchetti usati nell'packages.js
    ```bash
    npm install 
    ```

1. Modificare la directory nella cartella StreamFilesSample
    ```bash
    cd StreamFilesSample
    ```

1. Avviare Visual Studio Code dalla cartella AMSv3Samples. Questa operazione è necessaria per l'avvio dalla cartella in cui si trovano la cartella ". VSCODE" e tsconfig.jsnei file

    ```bash
    cd ..
    code .
    ```

Aprire la cartella per StreamFilesSample e aprire il file index. TS nell'editor Visual Studio Code.
Nel file index. TS, premere F5 per avviare il debugger.


## <a name="test-with-azure-media-player"></a>Eseguire il test con Azure Media Player

Per testare lo streaming, in questo articolo viene usato Azure Media Player. È anche possibile usare qualsiasi lettore compatibile con HLS o DASH, ad esempio Shaka Player, HLS.js, Dash.js o altri utenti.

Dovrebbe essere possibile fare clic sul collegamento generazione nell'esempio e avviare il lettore AMP con il manifesto del TRATTEggio già caricato.

> [!NOTE]
> Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

1. Aprire un Web browser e passare [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL** incollare uno degli URL di streaming ottenuto quando si è eseguita l'applicazione. 
 
     È possibile incollare l'URL in formato HLS, Dash o Smooth e Azure Media Player passerà automaticamente a un protocollo di streaming appropriato per la riproduzione sul dispositivo.
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'intero contenuto del gruppo di risorse non è più necessario, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Ulteriore documentazione per gli sviluppatori per Node.js in Azure
- [Azure per & JavaScript Node.js sviluppatori](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository dell'hub git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentazione dei pacchetti di Azure per sviluppatori Node.js](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Vedere anche

- [Codici di errore dei processi](/rest/api/media/jobs/get#joberrorcode).
- [installazione di NPM @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure per & JavaScript Node.js sviluppatori](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Concetti relativi ai Servizi multimediali](concepts-overview.md)
