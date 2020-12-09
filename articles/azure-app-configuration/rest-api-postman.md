---
title: API REST di Azure Active Directory-test tramite l'uso di post
description: Usare i post per testare l'API REST di configurazione app Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932490"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Testare l'API REST di configurazione di app Azure usando il post

Per eseguire il test dell'API REST tramite il [post](https://www.getpostman.com/), è necessario includere le intestazioni HTTP necessarie per [l'autenticazione](./rest-api-authentication-hmac.md) nelle richieste. Di seguito viene illustrato come configurare il post per il test dell'API REST, generando automaticamente le intestazioni di autenticazione:

1. Crea una nuova [richiesta](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Aggiungere la `signRequest` funzione dall' [esempio di autenticazione JavaScript](./rest-api-authentication-hmac.md#javascript) allo [script di pre-richiesta](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) per la richiesta
1. Aggiungere il codice seguente alla fine dello script di pre-richiesta. Aggiornare la chiave di accesso come indicato dal commento TODO

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Inviare la richiesta
