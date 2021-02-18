---
title: Documentazione di spavalderia-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La documentazione di Swagger consente di generare automaticamente gli SDK per diversi linguaggi di programmazione. Tutte le operazioni nel servizio sono supportate da Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636269"
---
# <a name="swagger-documentation"></a>Documentazione di Swagger

Il servizio riconoscimento vocale offre una specifica di spavalderia per interagire con alcune API REST usate per importare dati, creare modelli, testare l'accuratezza del modello, creare endpoint personalizzati, accodare trascrizioni batch e gestire le sottoscrizioni. La maggior parte delle operazioni disponibili tramite [l'area riconoscimento vocale personalizzato di speech studio](https://aka.ms/customspeech) può essere completata a livello di programmazione tramite queste API.

> [!NOTE]
> Il servizio di riconoscimento vocale ha diverse API REST per [sintesi vocale](rest-speech-to-text.md) e sintesi [vocale](rest-text-to-speech.md).  
>
> Tuttavia, solo le [API REST di sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) e v 2.0 sono documentate nella specifica spavalderia. Per informazioni su tutte le altre API REST di servizi vocali, vedere i documenti a cui si fa riferimento nel paragrafo precedente.

## <a name="generating-code-from-the-swagger-specification"></a>Generazione di codice dalla specifica di Swagger

La [specifica di Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) include opzioni che consentono di testare rapidamente diversi percorsi. A volte è tuttavia preferibile generare il codice per tutti i percorsi, creando una singola libreria di chiamate su cui poter basare le soluzioni future. Verrà ora illustrato il processo per generare una libreria Python.

È necessario impostare spavalderia sull'area della propria risorsa di riconoscimento vocale. È possibile confermare l'area nella sezione **Panoramica** delle impostazioni delle risorse vocali in portale di Azure. L'elenco completo delle aree supportate è disponibile [qui](regions.md#speech-to-text).

1. In un browser passare alla specifica di spavalderia per l' [area geografica](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. In questa pagina fare clic su **definizione API** e quindi su **spavalderia**. Copiare l'URL della pagina visualizzata.
1. In un nuovo browser passare a [https://editor.swagger.io](https://editor.swagger.io)
1. Fare clic su **file**, quindi su **Importa URL**, incollare l'URL e fare clic su **OK**.
1. Fare clic su **genera client** e selezionare **Python**. La libreria client viene scaricata nel computer in un `.zip` file.
1. Estrarre tutti gli elementi dal download. `tar -xf`Per estrarre tutti gli elementi, è possibile usare.
1. Installare il modulo estratto nell'ambiente Python:  
      `pip install path/to/package/python-client`
1. Il pacchetto installato è denominato `swagger_client` . Verificare che l'installazione abbia funzionato:  
       `python -c "import swagger_client"`

È possibile usare la libreria Python generata con gli esempi di [servizio vocale su GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Documenti di riferimento

* [Spavalderia: API REST per sintesi vocale v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST di riconoscimento vocale](rest-speech-to-text.md)
* [API REST di sintesi vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esempi di servizi vocali su GitHub](https://aka.ms/csspeech/samples).
* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](overview.md#try-the-speech-service-for-free)
