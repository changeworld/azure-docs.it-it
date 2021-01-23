---
title: Resilienza della compilazione usando la valutazione dell'accesso continuo in Azure Active Directory
description: Guida per architetti e amministratori IT sull'uso di CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a12cc3299fbb5916afba48d2608024f28999634
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724628"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Resilienza della compilazione usando la valutazione dell'accesso continuo

La [valutazione di accesso continuo](../conditional-access/concept-continuous-access-evaluation.md) (CAE) consente alle applicazioni Azure ad di sottoscrivere eventi critici che possono quindi essere valutati e applicati. Ciò include la valutazione degli eventi seguenti:

* Account utente eliminato o disabilitato

* La password per un utente è stata modificata

* Multi-factor authentication è abilitato per l'utente.

* L'amministratore revoca esplicitamente un token.

* È stato rilevato un rischio utente elevato.

Di conseguenza, le applicazioni possono rifiutare i token non scaduti in base agli eventi segnalati da Azure AD, come illustrato nel diagramma seguente.

![conceptualiagram di CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Qual è la Guida di CAE?

Questo meccanismo consente Azure AD di emettere token di lunga durata, consentendo alle applicazioni di revocare l'accesso e forzare la riautenticazione solo quando necessario. Il risultato netto di questo modello è un minor numero di chiamate per l'acquisizione di token, il che significa che il flusso end-to-end è più resiliente. 

Per usare CAE, il servizio e il client devono essere in grado di supportare il CAE. Microsoft 365 servizi quali Exchange Online, i team e SharePoint Online supportano CAE. Sul lato client, le esperienze basate su browser che usano i servizi di Office 365 (ad esempio Outlook Web App) e le versioni specifiche dei client nativi di Office 365 sono compatibili con CAE. Un maggior numero di servizi cloud Microsoft diventerà idoneo per CAE.

Microsoft collabora con il settore per creare [standard](https://openid.net/wg/sse/) che consentiranno alle applicazioni di terze parti di usare questa funzionalità. È anche possibile sviluppare applicazioni che supportano la funzionalità CAE. Per ulteriori informazioni, vedere come creare resilienza nell'applicazione.

## <a name="how-do-i-implement-cae"></a>Ricerca per categorie implementare CAE?

* [Abilitare CAE](../conditional-access/concept-continuous-access-evaluation.md) nella configurazione di sicurezza Azure ad.

* Assicurarsi che l'organizzazione usi [versioni compatibili](../conditional-access/concept-continuous-access-evaluation.md) di Microsoft Office applicazioni native.

* [Ottimizzare i prompt di riautenticazione](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

 
## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)