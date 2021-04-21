---
title: API Management back-end di Azure | Microsoft Docs
description: Informazioni sui back-end personalizzati in API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813021"
---
# <a name="backends-in-api-management"></a>Back-end in API Management

Un *back-end* (o *back-end api)* in API Management è un servizio HTTP che implementa l'API front-end e le relative operazioni.

Quando si importano determinate API, API Management il back-end dell'API viene configurato automaticamente. Ad esempio, API Management il back-end quando si importa una specifica [OpenAPI,](import-api-from-oas.md) [un'API SOAP](import-soap-api.md)o risorse di Azure, ad esempio un'app per le funzioni [di Azure](import-function-app-as-api.md) attivata tramite HTTP o un'app per la [logica.](import-logic-app-as-api.md)

API Management supporta anche l'uso di altre risorse di Azure, ad esempio un [cluster Service Fabric o](how-to-configure-service-fabric-backend.md) un servizio personalizzato come back-end api. L'uso di questi back-end personalizzati richiede una configurazione aggiuntiva, ad esempio per autorizzare le credenziali delle richieste al servizio back-end e per definire le operazioni API. È possibile configurare e gestire questi back-end nel portale di Azure o usando le API o gli strumenti di Azure.

Dopo aver creato un back-end, è possibile fare riferimento all'URL back-end nelle API. Usare i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criteri per reindirizzare una richiesta API in ingresso al back-end personalizzato anziché al back-end predefinito per tale API.

## <a name="benefits-of-backends"></a>Vantaggi dei back-end

Un back-end personalizzato offre diversi vantaggi, tra cui:

* Astrae informazioni sul servizio back-end, promuovendo la riutilizzabilità tra le API e una governance migliorata  
* Facile da usare configurando criteri di trasformazione in un'API esistente
* Sfrutta la funzionalità API Management per mantenere i segreti nelle Azure Key Vault [se](api-management-howto-properties.md) i valori denominati sono configurati per l'autenticazione dei parametri di intestazione o query

## <a name="next-steps"></a>Passaggi successivi

* Configurare un [Service Fabric back-end usando](how-to-configure-service-fabric-backend.md) il portale di Azure.
* I back-end possono essere configurati anche usando [l'API REST](/rest/api/apimanagement)API Management, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)o [Azure Resource Manager modelli](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

