---
title: CA consentita per l'abilitazione di HTTPS personalizzato
titleSuffix: Azure Content Delivery Network
description: Se si usa il proprio certificato per abilitare HTTPS in un dominio personalizzato, è necessario usare un'autorità di certificazione (CA) consentita per crearla.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573399"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Autorità di certificazione autorizzate per l'abilitazione di HTTPS personalizzato

Quando si [Abilita la funzionalità HTTPS usando il proprio certificato](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) per un dominio personalizzato della rete CDN di Azure (rete per la distribuzione di contenuti), sono necessari requisiti specifici per i certificati. 

* Il profilo della rete **CDN standard di Azure di Microsoft** richiede un certificato di una delle autorità di certificazione (CA) approvate nell'elenco seguente. Se viene usato un certificato da una CA non approvata o se viene usato un certificato autofirmato, la richiesta viene rifiutata. 

* I profili della rete **CDN standard di Azure di Verizon** e **della rete CDN Premium di Azure da Verizon** accettano tutti i certificati validi da qualsiasi CA valida. I profili Verizon non supportano i certificati autofirmati.

> [!NOTE]
> La possibilità di usare il proprio certificato per abilitare la funzionalità HTTPS del dominio personalizzato *non* è disponibile per la rete **CDN standard di Azure dai profili Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

