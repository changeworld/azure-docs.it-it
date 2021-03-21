---
title: Creare una risorsa Analisi del testo di Servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa per Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101751064"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Creare una risorsa Analisi del testo di Servizi cognitivi

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa** e quindi passare a **Intelligenza artificiale e Machine Learning** > **Analisi del testo**.
   In alternativa, passare a [crea analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Immettere tutte le impostazioni necessarie:

    |Impostazione|Valore|
    |--|--|
    |Nome|Immettere un nome (2-64 caratteri).|
    |Sottoscrizione|Scegliere la sottoscrizione appropriata.|
    |Località|Selezionare una località limitrofa.|
    |Piano tariffario| Immettere **S**, ovvero il piano tariffario standard.|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile.|

1. Selezionare **Crea** e attendere che venga creata la risorsa. Il browser reindirizza automaticamente alla pagina della risorsa appena creata.
1. Raccogliere i dati configurati `endpoint` e una chiave API:

    |Scheda risorsa nel portale|Impostazione|Valore|
    |--|--|--|
    |**Overview**|Endpoint|Copiare l'endpoint. Sembra simile a `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Chiavi**|Chiave API|Copiare una delle due chiavi. Si tratta di una stringa alfanumerica di 32 caratteri senza spazi o trattini: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
