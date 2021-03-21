---
title: Come codificare ed eseguire lo streaming di file video con Node.JS
description: Come eseguire lo streaming di file video con Node.JS. Seguire i passaggi di questa esercitazione per creare un nuovo account di Servizi multimediali di Azure, codificare un file ed eseguirne lo streaming su Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Servizi multimediali di Azure, flusso, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: b7efa37747e6d116b4cf26b4cf54377037f22cdd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212757"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Come codificare ed eseguire lo streaming di file video con Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questa guida introduttiva illustra quanto sia facile codificare e iniziare a eseguire lo streaming di video su un'ampia gamma di browser e dispositivi tramite Servizi multimediali di Azure. È possibile specificare un file video di input usando URL HTTPS, URL di firma di accesso condiviso o percorsi per i file che si trovano nell'archiviazione BLOB di Azure.

Alla fine di questa Guida introduttiva si saprà:

- Come codificare con Node.JS
- Come eseguire lo streaming con Node.JS
- Come caricare un file da un URL HTTPS con Node.JS
- Come usare un lettore client HLS o DASH con Node.JS

L'esempio in questo articolo codifica contenuti resi accessibili tramite un URL HTTPS. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS.

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

Aprire [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) nel progetto scaricato. Aggiornare il file *Sample. env* nella cartella radice con i valori e le credenziali ottenuti dall' [accesso alle API](./access-api-howto.md). Rinominare il file *. env di esempio* in *. env* (Sì, solo l'estensione).

L'esempio esegue le azioni seguenti:

1. Crea una **trasformazione** con un [set di impostazioni di codifica compatibile](./content-aware-encoding.md)con il contenuto. Verifica innanzitutto se la trasformazione specificata esiste.
1. Crea un **Asset** di output usato dal **processo** di codifica per contenere l'output
1. Facoltativamente, carica un file locale usando storage BLOB SDK
1. Crea l'input del **processo** in base a un URL HTTPS o a un file caricato
1. Invia il **processo** di codifica usando l'input e l'output creati in precedenza
1. Controlla lo stato del processo
1. Scarica l'output del processo di codifica in una cartella locale
1. Consente di creare un **localizzatore di streaming** da usare nel lettore
1. Compila gli URL di streaming per HLS e DASH
1. Riproduce il contenuto in un'applicazione lettore-Azure Media Player

## <a name="run-the-sample"></a>Eseguire l'esempio

1. L'applicazione Scarica i file codificati. Creare una cartella in cui si desidera che i file di output vengano aggiornati e aggiornare il valore della variabile **CartellaOutput** nel file [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) . Per impostazione predefinita, è impostato su "Temp".
1. Aprire un **prompt dei comandi** e passare alla directory dell'esempio.
1. Modificare la directory nella cartella AMSv3Samples

    ```bash
    cd AMSv3Samples
    ```

1. Installare i pacchetti utilizzati nella *packages.jssu* file.

    ```bash
    npm install 
    ```

1. Passare alla cartella *StreamFilesSample*

    ```bash
    cd StreamFilesSample
    ```

1. Avviare Visual Studio Code dalla cartella *AMSv3Samples* . Questa operazione è necessaria per l'avvio dalla cartella in cui si trovano la cartella *. VSCODE* e *tsconfig.jsnei* file.

    ```bash
    cd ..
    code .
    ```

Aprire la cartella per *StreamFilesSample* e aprire il file *index. ts* nell'editor Visual Studio Code.
Nel file *index. TS* , premere F5 per avviare il debugger.

## <a name="test-with-azure-media-player"></a>Eseguire il test con Azure Media Player

Usare Azure Media Player per testare il flusso. È anche possibile usare qualsiasi lettore compatibile con HLS o DASH, ad esempio Shaka Player, HLS.js, Dash.js o altri utenti.

Dovrebbe essere possibile fare clic sul collegamento generato nell'esempio e avviare il lettore AMP con il manifesto del TRATTEggio già caricato.

> [!NOTE]
> Se un lettore è ospitato in un sito https, assicurarsi di aggiornare l'URL impostandolo su "https".

1. Aprire un Web browser e passare [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Nella casella **URL:** incollare uno dei valori di URL di streaming ottenuti quando è stata eseguita l'applicazione. È possibile incollare l'URL in HLS, Dash o Smooth format e Azure Media Player passerà a un protocollo di streaming appropriato per la riproduzione automatica sul dispositivo.
3. Scegliere il pulsante **Update Player** (Aggiorna il lettore).

Azure Media Player può essere usato a scopo di test ma non deve essere usato in un ambiente di produzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'intero contenuto del gruppo di risorse non è più necessario, compresi gli account di archiviazione e di Servizi multimediali creati per questa esercitazione, eliminare il gruppo di risorse.

Eseguire il comando dell'interfaccia della riga di comando seguente:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Ulteriore documentazione per gli sviluppatori per Node.js in Azure

- [Azure per & JavaScript Node.js sviluppatori](/azure/developer/javascript/)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository dell'hub git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentazione dei pacchetti di Azure per sviluppatori Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Vedere anche

- [Codici di errore dei processi](/rest/api/media/jobs/get#joberrorcode).
- [installazione di NPM @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure per & JavaScript Node.js sviluppatori](/azure/developer/javascript/)
- [Codice sorgente di servizi multimediali nel @azure/azure-sdk-for-js repository](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Passaggi successivi

> [Concetti relativi ai Servizi multimediali](concepts-overview.md)
