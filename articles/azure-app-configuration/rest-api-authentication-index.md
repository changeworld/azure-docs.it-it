---
title: API REST di configurazione di app Azure-autenticazione
description: Pagine di riferimento per l'autenticazione tramite l'API REST di configurazione app Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932694"
---
# <a name="authentication"></a>Authentication

È necessario autenticare tutte le richieste HTTP. Sono supportati i seguenti schemi di autenticazione.

## <a name="hmac"></a>HMAC

[L'autenticazione HMAC](./rest-api-authentication-hmac.md) usa un segreto generato in modo casuale per firmare i payload della richiesta. Per informazioni dettagliate su come sono autorizzate le richieste che usano questo metodo di autenticazione, vedere la sezione relativa all' [autorizzazione HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Con [l'autenticazione Azure Active Directory (Azure ad)](../active-directory/authentication/overview-authentication.md) viene utilizzata una Bearer token ottenuta da Azure Active Directory per autenticare le richieste. Per informazioni dettagliate su come sono autorizzate le richieste che usano questo metodo di autenticazione, vedere la sezione [autorizzazione Azure ad](./rest-api-authorization-azure-ad.md) .