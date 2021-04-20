---
title: Eseguire la migrazione al nuovo portale per sviluppatori dal portale per sviluppatori legacy
titleSuffix: Azure API Management
description: Informazioni su come eseguire la migrazione dal portale per sviluppatori legacy al nuovo portale per sviluppatori in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738883"
---
# <a name="migrate-to-the-new-developer-portal"></a>Eseguire la migrazione al nuovo portale per sviluppatori

Questo articolo descrive i passaggi da eseguire per eseguire la migrazione dal portale legacy deprecato al nuovo portale per sviluppatori in API Management.

> [!IMPORTANT]
> Il portale per sviluppatori legacy è ora deprecato e riceverà solo gli aggiornamenti della sicurezza. È possibile continuare a usarlo come al solito fino al ritiro nell'ottobre 2023, quando verrà rimosso da tutti i servizi di Gestione API.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Miglioramenti nel nuovo portale per sviluppatori

Il nuovo portale per sviluppatori risolve molte limitazioni del portale deprecato. Include un [editor visivo di trascinamento della selezione per](api-management-howto-developer-portal-customize.md) la modifica del contenuto e un pannello dedicato per i progettisti per lo stile del sito Web. Le pagine, le personalizzazioni e la configurazione vengono salvate come Azure Resource Manager risorse nel servizio API Management, che consente di [automatizzare le distribuzioni del portale.](automate-portal-deployments.md) Infine, la codebase del portale è open source, quindi [è possibile estenderla con la funzionalità personalizzata](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Come eseguire la migrazione al nuovo portale per sviluppatori

Il nuovo portale per sviluppatori non è compatibile con il portale deprecato e la migrazione automatizzata non è possibile. È necessario ricreare manualmente il contenuto (pagine, testo, file multimediali) e personalizzare l'aspetto del nuovo portale. I passaggi precisi variano a seconda delle personalizzazioni e della complessità del portale. Per istruzioni, [vedere l'esercitazione sul portale](api-management-howto-developer-portal-customize.md) per sviluppatori. La configurazione rimanente, ad esempio l'elenco di API, prodotti, utenti e provider di identità, viene condivisa automaticamente tra entrambi i portali.

> [!IMPORTANT]
> Se il nuovo portale per sviluppatori è stato avviato in precedenza, ma non sono state apportate modifiche, reimpostare il contenuto predefinito per aggiornarlo alla versione più recente.

Quando si esegue la migrazione dal portale deprecato, tenere presenti le modifiche seguenti:

- Se si espone il portale per sviluppatori tramite un dominio personalizzato, [assegnare un dominio](configure-custom-domain.md) al nuovo portale per sviluppatori. Usare **l'opzione Portale** per sviluppatori nell'elenco a discesa portale di Azure.
- [Applicare un criterio CORS](developer-portal-faq.md#cors) alle API per abilitare la console di test interattiva.
- Se si inserisce css personalizzato per applicare uno stile al portale, è necessario replicare lo stile usando [il pannello di progettazione predefinito.](api-management-howto-developer-portal-customize.md) L'inserimento CSS non è consentito nel nuovo portale.
- È possibile inserire codice JavaScript personalizzato solo nella [versione self-hosted del nuovo portale.](api-management-howto-developer-portal.md#managed-vs-self-hosted)
- Se il API Management si trova in una rete virtuale ed è esposto a Internet tramite il gateway [applicazione,](api-management-howto-integrate-internal-vnet-appgateway.md) fare riferimento a questo articolo della documentazione per passaggi di configurazione precisi. È necessario:

    - Abilitare la connettività all API Management endpoint di gestione dell'utente.
    - Abilitare la connettività al nuovo endpoint del portale.
    - Disabilitare le regole di Web Application Firewall selezionate.

- Se sono stati modificati i modelli di notifica di posta elettronica predefiniti per includere un URL del portale deprecato definito in modo esplicito, modificarli in modo da usare il parametro URL del portale o puntare al nuovo URL del portale. Se invece i modelli usano il parametro PREDEFINITO DELL'URL del portale, non sono necessarie modifiche.
- *Problemi* e *applicazioni* non sono supportati nel nuovo portale per sviluppatori.
- L'integrazione diretta con Facebook, Microsoft, Twitter e Google come provider di identità non è supportata nel nuovo portale per sviluppatori. È possibile eseguire l'integrazione con tali provider tramite Azure AD B2C.
- Se si usa la delega, modificare l'URL restituito nelle applicazioni e usare [l'endpoint dell'API *Get Shared Access Token*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) invece dell'endpoint Genera URL *SSO.*
- Se si usa Azure AD come provider di identità:

    - Modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori.
    - Modificare il suffisso dell'URL restituito nell'applicazione da `/signin-aad` a `/signin` .

- Se si usa Azure AD B2C come provider di identità:

    - Modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori.
    - Modificare il suffisso dell'URL restituito nell'applicazione da `/signin-aad` a `/signin` .
    - Includere *il nome specificato,* *il cognome* e *l'ID oggetto dell'utente* nelle attestazioni dell'applicazione.

- Se si usa OAuth 2.0 nella console di test interattiva, modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori e modificare il suffisso:

    - Da `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` a per il flusso di `/signin-oauth/code/callback/[serverName]` concessione del codice di autorizzazione.
    - Da `/docs/services/[serverName]/console/oauth2/implicit/callback` a per il flusso di `/signin-oauth/implicit/callback` concessione implicita.
- Se si usa OpenID Connect nella console di test interattiva, modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori e modificare il suffisso:

    - Da `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` a per il flusso di `/signin-oauth/code/callback/[serverName]` concessione del codice di autorizzazione.
    - Da `/docs/services/[serverName]/console/openidconnect/implicit/callback` a per il flusso di `/signin-oauth/implicit/callback` concessione implicita.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
- [Accedere al portale per sviluppatori e personalizzarlo](api-management-howto-developer-portal-customize.md)