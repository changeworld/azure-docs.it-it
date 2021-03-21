---
title: Limiti e restrizioni del servizio Azure AD B2C
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui limiti del servizio e sulle restrizioni per il servizio Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979913"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Limiti e restrizioni del servizio Azure Active Directory B2C

Questo articolo contiene i vincoli di utilizzo e altri limiti di servizio per il servizio Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Limiti relativi all'utente finale o al consumo

I limiti dei servizi correlati agli utenti finali seguenti si applicano a tutti i protocolli di autenticazione e autorizzazione supportati da Azure AD B2C, tra cui SAML, Open ID Connect, OAuth2 e ROPC.

|Category |Limite    |
|---------|---------|
|Numero di richieste per ogni indirizzo IP per ogni tenant di Azure AD B2C       |6000/5 minuti          |
|Numero totale di richieste per ogni tenant di Azure AD B2C     |12000/min          |

Il numero di richieste può variare a seconda del numero di letture e scritture della directory che si verificano durante il Azure AD B2C percorso utente. Ad esempio, un semplice percorso di accesso che legge dalla directory è costituito da 1 richiesta. Se il percorso di accesso deve aggiornare anche la directory, questa operazione viene conteggiata come richiesta aggiuntiva.

## <a name="azure-ad-b2c-configuration-limits"></a>Limiti di configurazione Azure AD B2C

Nella tabella seguente sono elencati i limiti di configurazione amministrativa nel servizio Azure AD B2C.

|Category  |Limite  |
|---------|---------|
|Numero di ambiti per applicazione        |1000          |
|Numero di [attributi personalizzati](user-profile-attributes.md#extension-attributes)   per utente <sup>1</sup>       |100         |
|Numero di URL di reindirizzamento per applicazione       |100         |
|Numero di URL di disconnessione per applicazione        |1          |
|Limite stringa per attributo      |250 caratteri          |
|Numero di tenant B2C per sottoscrizione      |20         |
|Livelli di [ereditarietà](custom-policy-overview.md#inheritance-model) nei criteri personalizzati     |10         |
|Numero di criteri per ogni tenant di Azure AD B2C      |200          |
|Dimensioni massime del file di criteri      |400 KB          |

<sup>1</sup> vedere anche [Azure ad limiti e restrizioni del servizio](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [linee guida per la limitazione di Microsoft Graph](/graph/throttling) 
- Informazioni sulle [differenze di convalida per le applicazioni Azure ad B2C](../active-directory/develop/supported-accounts-validation.md)













