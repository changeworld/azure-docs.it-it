---
title: 'Avvio rapido: API REST Content Moderator'
titleSuffix: Azure Cognitive Services
description: Questa guida di avvio rapido descrive come iniziare a usare l'API REST Content Moderator di Azure. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: ebef33072b802ffc35b8c011d974dbcd203fa6e1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561478"
---
Introduzione all'API REST Content Moderator di Azure. 

Content Moderator è un servizio di intelligenza artificiale che consente di gestire i contenuti potenzialmente offensivi, rischiosi o in altro modo indesiderati. Usare il servizio di moderazione del contenuto basato sull'intelligenza artificiale per analizzare testo, immagini e video e applicare automaticamente i flag di contenuto. Integrare quindi l'app con lo strumento di revisione, un ambiente di moderazione online per un team di revisori umani. È possibile integrare un software di filtro dei contenuti nell'app per garantire la conformità con le normative o mantenere l'ambiente previsto per gli utenti.

Usare l'API REST Content Moderator per:

* Moderare il testo
* Moderare le immagini

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="creare una risorsa Content Moderator"  target="_blank">creare una risorsa Content Moderator <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Attendere che venga distribuita e fare clic sul pulsante **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione a Content Moderator. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.


## <a name="moderate-text"></a>Moderare il testo

Verrà usato un comando simile al seguente per chiamare l'API Content Moderator per analizzare un corpo di testo e stampare i risultati nella console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Copiare il comando in un editor di testo e apportare le modifiche seguenti:

1. Assegnare `Ocp-Apim-Subscription-Key` alla propria chiave di sottoscrizione Viso valida.
1. Modificare la prima parte dell'URL della query in modo che equivalga all'endpoint che corrisponde alla chiave di sottoscrizione.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Facoltativamente, cambiare il corpo della richiesta sostituendolo con qualsiasi stringa di testo da analizzare.

Dopo aver apportato le modifiche, aprire un prompt dei comandi e immettere il nuovo comando. 

### <a name="examine-the-results"></a>Esaminare i risultati

I risultati della moderazione del testo verranno visualizzati come dati JSON nella finestra della console. Ad esempio:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Per altre informazioni sugli attributi di testo verificati da Content Moderator, vedere la guida [Concetti di moderazione del testo](../../text-moderation-api.md).

## <a name="moderate-images"></a>Moderare le immagini

Verrà usato un comando simile al seguente per chiamare l'API Content Moderator per moderare un'immagine remota e stampare i risultati nella console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Copiare il comando in un editor di testo e apportare le modifiche seguenti:

1. Assegnare `Ocp-Apim-Subscription-Key` alla propria chiave di sottoscrizione Viso valida.
1. Cambiare la prima parte dell'URL della query in modo che equivalga all'endpoint che corrisponde alla chiave di sottoscrizione.
1. Facoltativamente, cambiare l'URL di `"Value"` nel corpo della richiesta sostituendolo con qualsiasi immagine remota da moderare.

> [!TIP]
> È anche possibile moderare immagini locali passando i dati dei relativi byte nel corpo della richiesta. Per informazioni, vedere la [documentazione di riferimento](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

Dopo aver apportato le modifiche, aprire un prompt dei comandi e immettere il nuovo comando. 

### <a name="examine-the-results"></a>Esaminare i risultati

I risultati della moderazione dell'immagine verranno visualizzati come dati JSON nella finestra della console. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Per altre informazioni sugli attributi dell'immagine verificati da Content Moderator, vedere la [guida concettuale alla moderazione di immagini](../../image-moderation-api.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come usare l'API REST Content Moderator per eseguire attività di moderazione. Per altre informazioni sulla moderazione di immagini o di altri contenuti multimediali, è possibile leggere una guida concettuale.

* [Concetti di moderazione delle immagini](../../image-moderation-api.md)
* [Concetti di moderazione del testo](../../text-moderation-api.md)
* [Che cos'è Azure Content Moderator?](../../overview.md)