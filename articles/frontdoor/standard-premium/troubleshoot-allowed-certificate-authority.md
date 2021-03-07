---
title: Autorità di certificazione consentite per Azure front door standard/Premium (anteprima)
description: Questo articolo elenca tutte le autorità di certificazione consentite quando si crea un certificato personalizzato.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434867"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autorità di certificazione consentite per Azure front door standard/Premium (anteprima)

Quando si Abilita la funzionalità HTTPS usando il proprio certificato per un dominio personalizzato di Azure front door standard/Premium. Per creare il certificato TLS/SSL è necessaria un'autorità di certificazione (CA) consentita. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
