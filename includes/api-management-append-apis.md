---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601939"
---
## <a name="append-other-apis"></a>Aggiungere altre API

È possibile comporre un'API di API esposte da servizi diversi, tra cui:
* Specifica OpenAPI
* UN'API SOAP
* La funzionalità App per le API di Servizio app di Azure
* App per le funzioni di Azure
* App per la logica di Azure
* Azure Service Fabric

Aggiungere un'API diversa all'API esistente seguendo questa procedura. 

>[!NOTE] 
> Quando si importa un'altra API, le operazioni vengono aggiunte all'API corrente.

1. Passare all'istanza di Gestione API di Azure nel portale di Azure.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Passare all'istanza di Gestione API di Azure":::

1. Selezionare **API** nel menu a sinistra.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Selezionare le API":::

1. Fare clic su **...** accanto all'API a cui si vuole aggiungere un'altra API.
1. Selezionare **Importa** dal menu a discesa.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Selezionare Importa":::

1. Selezionare un servizio da cui importare un'API.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Selezionare un servizio":::