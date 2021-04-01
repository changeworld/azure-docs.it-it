---
title: Criteri di gestione API di esempio-aggiungere funzionalità al servizio back-end
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure che illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 64db4e5451425002eeaac11695ac011a96047d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076182"
---
# <a name="add-capabilities-to-a-backend-service"></a>Aggiungere funzionalità a un servizio back-end

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere funzionalità a un servizio back-end, ad esempio per accettare il nome di un luogo anziché latitudine e longitudine in un'API di previsioni meteo. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-reference.md)