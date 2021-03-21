---
title: API REST di configurazione app Azure-autorizzazione
description: Pagine di riferimento per l'autorizzazione con l'API REST di configurazione app Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932643"
---
# <a name="authorization"></a>Autorizzazione

L'autorizzazione si riferisce alla procedura utilizzata per determinare le autorizzazioni di cui dispone un chiamante durante l'esecuzione di una richiesta. Sono disponibili più modelli di autorizzazione. Il modello di autorizzazione utilizzato per una richiesta dipende dal metodo di [autenticazione](./rest-api-authentication-index.md) utilizzato. I modelli di autorizzazione sono elencati di seguito.

## <a name="hmac"></a>HMAC

Il modello di [modello di autorizzazione](./rest-api-authorization-hmac.md) associato all'autenticazione HMAC suddivide le autorizzazioni in sola lettura o in lettura/scrittura. Per informazioni dettagliate, vedere la pagina relativa all' [autorizzazione HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Il [modello di autorizzazione](./rest-api-authorization-azure-ad.md) associato all'autenticazione Azure Active Directory (Azure ad) usa il controllo degli accessi in base al ruolo di Azure. Per informazioni dettagliate, vedere la pagina relativa all' [autorizzazione Azure ad](./rest-api-authorization-azure-ad.md) .
