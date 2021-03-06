---
title: Avvio rapido su Azure Media Player
description: Informazioni sui passaggi di base per configurare Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/05/2021
ms.openlocfilehash: a6fd603318a25e15d1d4dcc1e3eaf75f96fc5ade
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448628"
---
# <a name="azure-media-player-quickstart"></a>Avvio rapido su Azure Media Player
Azure Media Player è semplice da configurare. Bastano pochi minuti per ottenere la riproduzione di base di contenuti multimediali dall'account Servizi multimediali di Azure. Questa sezione illustra i passaggi di base senza entrare nei dettagli. Le sezioni che seguono illustrano le specifiche relative all'installazione e la configurazione di Azure Media Player.  È sufficiente aggiungere le parti seguenti a `<head>` del documento:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **NON** usare la versione `latest` nell'ambiente di produzione, perché è soggetta a modifiche su richiesta. Sostituire `latest` con una versione di Azure Media Player, ad esempio sostituire `latest` con `1.0.0`. È possibile eseguire query sulle versioni di Azure Media Player [qui](https://amp.azure.net/libs/amp/latest/docs/changelog.html).

## <a name="use-the-video-element"></a>Usare l'elemento video

Successivamente, è sufficiente usare l'elemento `<video>` come si farebbe normalmente, ma con un attributo `data-setup` aggiuntivo contenente le opzioni, che possono includere qualsiasi opzione di Servizi multimediali di Azure in un oggetto JSON valido.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Se non si vuole usare l'installazione automatica, è possibile omettere l'attributo `data-setup` e inizializzare un elemento video manualmente.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Passaggi successivi ##

- [Configurazione completa di Azure Media Player](./azure-media-player-full-setup.md)
