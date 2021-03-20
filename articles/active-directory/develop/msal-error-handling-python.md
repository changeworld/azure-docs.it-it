---
title: Gestire errori ed eccezioni in MSAL per Python
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, le richieste di attestazioni di accesso condizionale e i tentativi in MSAL per le applicazioni Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761089"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Gestire errori ed eccezioni in MSAL per Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Gestione degli errori in MSAL per Python

In MSAL per Python la maggior parte degli errori viene comunicata come valore restituito dalla chiamata API. L'errore è rappresentato da un dizionario che contiene la risposta JSON di Microsoft Identity Platform.

* Una risposta con esito positivo contiene la chiave `"access_token"`. Il formato della risposta è definito dal protocollo OAuth2. Per ulteriori informazioni, vedere [5.1 Risposta riuscita](https://tools.ietf.org/html/rfc6749#section-5.1)
* Una risposta di errore contiene `"error"` e in genere `"error_description"`. Il formato della risposta è definito dal protocollo OAuth2. Per altre informazioni, vedere [5.2 Risposta di errore](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando viene restituito un errore, la chiave `"error_description"` contiene un messaggio leggibile, che a sua volta contiene in genere un codice errore di Microsoft Identity Platform. Per informazioni dettagliate sui vari codici errore, vedere [Codici errore di autenticazione e autorizzazione](./reference-aadsts-error-codes.md).

In MSAL per Python le eccezioni sono rare perché la maggior parte degli errori viene gestita restituendo un valore di errore. L' `ValueError` eccezione viene generata solo quando si verifica un problema con il modo in cui si sta tentando di utilizzare la libreria, ad esempio quando i parametri API non sono validi.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passaggi successivi

Si consiglia di abilitare [la registrazione in MSAL per Python](msal-logging-python.md) per facilitare la diagnosi e il debug dei problemi.
