---
title: Passare i token di autenticazione a servizi multimediali V3 | Microsoft Docs
description: Informazioni su come inviare i token di autenticazione dal client al servizio di distribuzione delle chiavi di servizi multimediali V3
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572542"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Passare i token al servizio di distribuzione delle chiavi di servizi multimediali di Azure V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I clienti chiedono spesso informazioni su come passare i token da un lettore al servizio di distribuzione delle chiavi di Servizi multimediali di Azure per la verifica, in modo che il lettore possa ottenere la chiave. Servizi multimediali supporta i formati SWT (Simple Web Token, token Web semplice) e JWT (JSON Web Token, token JSON Web). L'autenticazione di token può essere applicata a qualsiasi tipo di chiave, indipendentemente dal fatto che si esegua la crittografia comune o la crittografia envelope AES (Advanced Encryption Standard).

 Il token può essere passato con il lettore nei modi seguenti, a seconda del lettore e della piattaforma di destinazione:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Passare un token tramite l'intestazione di autorizzazione HTTP

> [!NOTE]
> Per le specifiche di OAuth 2.0 è previsto il prefisso "Bearer". Scegliere **AES (token JWT)** o **AES (token SWT)** per impostare l'origine video. Il token viene passato tramite l'intestazione dell'autorizzazione.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Passare un token tramite l'aggiunta di un parametro di query URL con "token = TokenValue".

> [!NOTE]
> Non è previsto il prefisso "Bearer". Poiché il token viene inviato tramite un URL, è necessario blindare la stringa del token. Di seguito è riportato un codice di esempio C# che illustra come eseguire questa operazione:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Passare un token tramite il campo CustomData

Questa opzione è utilizzata esclusivamente per l'acquisizione della licenza PlayReady tramite il campo CustomData della richiesta di acquisizione di licenze PlayReady. In questo caso, il token deve essere all'interno del documento XML, come descritto di seguito:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Inserire il token di autenticazione nell'elemento Token.
