---
title: 'Avvio rapido: Creare e gestire i token di accesso'
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come gestire le identità e i token di accesso usando la libreria client di identità dei servizi di comunicazione di Azure.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 8937210b366981d9768e5329989a8ad14f5e8f35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657060"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Avvio rapido: Creare e gestire i token di accesso

Introduzione ai servizi di comunicazione di Azure tramite la libreria client di identità dei servizi di comunicazione. Consente di creare identità e di gestire i token di accesso. L'identità rappresenta l'entità dell'applicazione nel servizio di comunicazione di Azure, ad esempio utente o dispositivo. I token di accesso consentono alle librerie client per le chat e le chiamate di eseguire l'autenticazione direttamente in Servizi di comunicazione di Azure. È consigliabile generare token di accesso in un servizio lato server. I token di accesso vengono quindi usati per inizializzare le librerie client di Servizi di comunicazione nei dispositivi client.

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

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at Fri Nov 27 2020 16:47:05 GMT-0800 (Pacific Standard Time):
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
> * Usare la libreria client di identità dei servizi di comunicazione


> [!div class="nextstepaction"]
> [Aggiungere chiamate vocali all'app](./voice-video-calling/getting-started-with-calling.md)

Può essere utile vedere anche gli articoli seguenti:

 - [Informazioni sull'autenticazione](../concepts/authentication.md)
 - [Aggiungere una chat all'app](./chat/get-started.md)
 - [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
