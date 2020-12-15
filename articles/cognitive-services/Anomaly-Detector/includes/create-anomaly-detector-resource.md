---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: d1add17c1c84d2a22d76aaa1f96aeca4db645ba7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506263"
---
## <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Selezionare <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">Crea risorsa rilevamento <span class="docon docon-navigate-external x-hidden-focus"></span> anomalie</a> .
1. Immettere tutte le impostazioni richieste:

    |Impostazione|Valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0` -100 chiamate al secondo, 20.000 transazioni al mese. <br> Oppure:<br> `S0` -80 chiamate al secondo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Al termine della creazione, passare alla pagina delle risorse
1. Raccolta configurata `endpoint` e chiave API:

    |Scheda chiavi e endpoint nel portale|Impostazione|Valore|
    |--|--|--|
    |**Panoramica**|Endpoint|Copiare l'endpoint. Ha un aspetto simile a ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Chiavi**|Chiave API|Copiare 1 delle due chiavi. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



