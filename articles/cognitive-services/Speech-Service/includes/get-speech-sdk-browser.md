---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 252f2e6275b01522b83e846201d190b39d2bbf21
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434510"
---
:::row:::
    :::column span="3":::
        Speech SDK per JavaScript è disponibile come pacchetto NPM, vedere <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech-SDK </a> e il relativo repository GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-Services-Speech-SDK-JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Sebbene l'SDK di riconoscimento vocale per JavaScript sia disponibile come pacchetto NPM, i Web browser client e Node.js possono utilizzarlo, in modo da considerare le diverse implicazioni architettoniche di ogni ambiente. Il <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dom (Document Object Model) </a> , ad esempio, non è disponibile per le applicazioni lato server, proprio come il <a href="https://nodejs.org/api/fs.html" target="_blank">file System </a> non è disponibile per le applicazioni lato client.

### <a name="nodejs-package-manager-npm"></a>Gestione pacchetti Node.js (NPM)

Per installare l'SDK di riconoscimento vocale per JavaScript, eseguire il `npm install` comando seguente.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Tag script HTML

In alternativa, è possibile includere direttamente un `<script>` tag nell'elemento HTMLS `<head>` , che si basa su <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM Syndicate</a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Per ulteriori informazioni, vedere la <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Guida introduttiva di Web browser Speech SDK </a>.
