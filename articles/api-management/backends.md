---
title: Backend per gestione API di Azure | Microsoft Docs
description: Informazioni sui backend personalizzati in gestione API
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500617"
---
# <a name="backends-in-api-management"></a>Backend in gestione API

Un *back* -end o un *back-end API* in gestione API è un servizio HTTP che implementa l'API front-end e le relative operazioni.

Quando si importano determinate API, gestione API configura automaticamente il back-end dell'API. Ad esempio, gestione API configura il back-end quando si importano una [specifica openapi](import-api-from-oas.md), un' [API SOAP](import-soap-api.md)o risorse di Azure, ad esempio un'app per la [logica](import-logic-app-as-api.md)o un [app per le funzioni di Azure](import-function-app-as-api.md) attivato da http.

Gestione API supporta anche l'uso di altre risorse di Azure, ad esempio un [cluster Service Fabric](how-to-configure-service-fabric-backend.md) o un servizio personalizzato come back-end dell'API. L'uso di questi back-end personalizzati richiede una configurazione aggiuntiva, ad esempio, per autorizzare le credenziali delle richieste al servizio back-end e per definire le operazioni dell'API. Configurare e gestire questi backend nel portale di Azure o usando le API o gli strumenti di Azure.

Dopo aver creato un back-end, è possibile fare riferimento all'URL back-end nelle API. Usare il [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criterio per reindirizzare una richiesta API in ingresso al back-end personalizzato anziché al back-end predefinito per l'API.

## <a name="benefits-of-backends"></a>Vantaggi dei backend

Un back-end personalizzato presenta diversi vantaggi, tra cui:

* Astrae informazioni sul servizio back-end, innalzando di livello la riusabilità tra API e governance migliorata  
* Semplicità di utilizzo mediante la configurazione di criteri di trasformazione in un'API esistente
* Sfrutta le funzionalità di gestione API per gestire i segreti in Azure Key Vault se [i valori denominati](api-management-howto-properties.md) sono configurati per l'autenticazione dei parametri di query o di intestazione

## <a name="next-steps"></a>Passaggi successivi

* Configurare un [back-end Service Fabric](how-to-configure-service-fabric-backend.md) usando il portale di Azure.
* I backend possono anche essere configurati usando l' [API REST](/rest/api/apimanagement)di gestione api, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)o i [modelli di Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

