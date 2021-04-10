---
title: 'Avvio rapido: Creare e gestire i token di accesso'
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come gestire le identità e i token di accesso usando Azure Communication Services Identity SDK.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e356219d22ee558ce3de5a96d58f24b9e7902d8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726618"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Avvio rapido: Creare e gestire i token di accesso

Introduzione ai servizi di comunicazione di Azure tramite l'SDK di identità dei servizi di comunicazione. Consente di creare identità e di gestire i token di accesso. L'identità rappresenta l'entità dell'applicazione nel servizio di comunicazione di Azure, ad esempio utente o dispositivo. I token di accesso consentono alla chat e a chiamare gli SDK di eseguire l'autenticazione direttamente nei servizi di comunicazione di Azure. È consigliabile generare token di accesso in un servizio lato server. I token di accesso vengono quindi usati per inizializzare gli SDK di servizi di comunicazione nei dispositivi client.

Tutti i prezzi visualizzati nelle immagini di questa esercitazione sono riportati unicamente a scopo dimostrativo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

L'output dell'app descrive ogni azione completata:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Altre informazioni sulla [pulizia delle risorse](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Gestire le identità
> * Risolvere i problemi dei token di accesso
> * Usare Communication Services Identity SDK


> [!div class="nextstepaction"]
> [Aggiungere chiamate vocali all'app](./voice-video-calling/getting-started-with-calling.md)

Può essere utile vedere anche gli articoli seguenti:

 - [Informazioni sull'autenticazione](../concepts/authentication.md)
 - [Aggiungere una chat all'app](./chat/get-started.md)
 - [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
