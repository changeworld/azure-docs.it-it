---
title: Modello applicativo | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con la piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795660"
---
# <a name="application-model"></a>Modello applicativo

Le applicazioni possono accedere agli utenti stessi o delegare l'accesso a un provider di identità. Questo articolo illustra i passaggi necessari per registrare un'applicazione con la piattaforma di identità Microsoft.

## <a name="register-an-application"></a>Registrare un'applicazione

Affinché un provider di identità sappia che un utente ha accesso a una determinata app, sia l'utente che l'applicazione devono essere registrati con il provider di identità. Quando si registra l'applicazione con Azure Active Directory (Azure AD), si sta fornendo una configurazione dell'identità per l'applicazione che consente l'integrazione con la piattaforma di identità Microsoft. La registrazione dell'app consente anche di:

* Personalizzare la personalizzazione dell'applicazione nella finestra di dialogo di accesso. Questa personalizzazione è importante perché l'accesso è la prima esperienza che un utente avrà con l'app.
* Decidere se si desidera consentire agli utenti di accedere solo se appartengono all'organizzazione. Questa architettura è nota come applicazione a tenant singolo. In alternativa, è possibile consentire agli utenti di accedere usando un account aziendale o dell'Istituto di istruzione, noto come applicazione multi-tenant. È anche possibile consentire account Microsoft personali o un account di social networking da LinkedIn, Google e così via.
* Autorizzazioni dell'ambito della richiesta. È ad esempio possibile richiedere l'ambito "User. Read", che concede l'autorizzazione per leggere il profilo dell'utente che ha eseguito l'accesso.
* Definire gli ambiti che definiscono l'accesso all'API Web. In genere, quando un'app vuole accedere all'API, sarà necessario richiedere le autorizzazioni agli ambiti definiti.
* Condividere un segreto con la piattaforma di identità Microsoft che dimostra l'identità dell'app. L'uso di un segreto è rilevante nel caso in cui l'app sia un'applicazione client riservata. Un'applicazione client riservata è un'applicazione in grado di conservare le credenziali in modo sicuro. Per archiviare le credenziali è necessario un server back-end attendibile.

Dopo che l'app è stata registrata, viene assegnato un identificatore univoco che condivide con la piattaforma di identità Microsoft quando richiede i token. Se l'app è un' [applicazione client riservata](developer-glossary.md#client-application), condividerà anche il segreto o la chiave pubblica, a seconda che siano stati usati i certificati o i segreti.

La piattaforma Microsoft Identity rappresenta le applicazioni utilizzando un modello che soddisfi due funzioni principali:

* Identificare l'app in base ai protocolli di autenticazione supportati.
* Specificare tutti gli identificatori, gli URL, i segreti e le informazioni correlate necessarie per l'autenticazione.

Piattaforma di identità Microsoft:

* Include tutti i dati necessari per supportare l'autenticazione in fase di esecuzione.
* Include tutti i dati per la decisione delle risorse a cui un'app potrebbe dover accedere e in quali circostanze deve essere soddisfatta una determinata richiesta.
* Fornisce l'infrastruttura per implementare il provisioning delle app nel tenant dello sviluppatore di app e in qualsiasi altro tenant Azure AD.
* Gestisce il consenso dell'utente durante il tempo di richiesta del token e facilita il provisioning dinamico delle app tra i tenant.

Il *consenso* è il processo di un proprietario di risorse che concede l'autorizzazione a un'applicazione client per accedere alle risorse protette, in autorizzazioni specifiche, per conto del proprietario della risorsa. La piattaforma Microsoft Identity consente:

* Utenti e amministratori per concedere o negare in modo dinamico l'accesso alle risorse per conto dell'app.
* Gli amministratori devono infine decidere quali app sono autorizzate a eseguire e quali utenti possono usare app specifiche e come accedere alle risorse di directory.

## <a name="multi-tenant-apps"></a>App multi-tenant

Nella piattaforma di identità Microsoft, un [oggetto applicazione](developer-glossary.md#application-object) descrive un'applicazione. In fase di distribuzione, la piattaforma Microsoft Identity usa l'oggetto applicazione come progetto per creare un' [entità servizio](developer-glossary.md#service-principal-object), che rappresenta un'istanza concreta di un'applicazione all'interno di una directory o di un tenant. L'entità servizio definisce le operazioni che l'app può effettivamente eseguire in una directory di destinazione specifica, chi può usarla, a quali risorse ha accesso e così via. La piattaforma Microsoft Identity crea un'entità servizio da un oggetto applicazione tramite il [consenso](developer-glossary.md#consent).

Il diagramma seguente illustra un flusso di provisioning di Microsoft Identity Platform semplificato basato su consenso. Mostra due tenant: *a* e *B*.

* Il *tenant A* è proprietario dell'applicazione.
* Il *tenant B* sta creando un'istanza dell'applicazione tramite un'entità servizio.

![Diagramma che mostra un flusso di provisioning semplificato guidato dal consenso.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In questo flusso di provisioning:

1. Un utente del tenant B tenta di accedere con l'app. L'endpoint di autorizzazione richiede un token per l'applicazione.
1. Le credenziali utente vengono acquisite e verificate per l'autenticazione.
1. All'utente viene richiesto di fornire il consenso affinché l'app ottenga l'accesso al tenant B.
1. La piattaforma Microsoft Identity usa l'oggetto applicazione nel tenant A come progetto per la creazione di un'entità servizio nel tenant B.
1. L'utente riceve il token richiesto.

È possibile ripetere questo processo per altri tenant. Il tenant A mantiene il progetto per l'app (oggetto applicazione). Gli utenti e gli amministratori di tutti gli altri tenant in cui l'app dispone del consenso continuano a controllare ciò che l'applicazione può eseguire tramite l'oggetto entità servizio corrispondente in ogni tenant. Per altre informazioni, vedere [oggetti applicazione e oggetti entità servizio nella piattaforma di identità Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'autenticazione e l'autorizzazione nella piattaforma di identità Microsoft, vedere gli articoli seguenti:

* Per informazioni sui concetti di base di autenticazione e autorizzazione, vedere [autenticazione e autorizzazione](authentication-vs-authorization.md).
* Per informazioni sull'uso di token di accesso, token di aggiornamento e token ID nell'autenticazione e nell'autorizzazione, vedere [token di sicurezza](security-tokens.md).
* Per informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili, vedere [flusso di accesso all'app](app-sign-in-flow.md).

Per ulteriori informazioni sul modello di applicazione, vedere gli articoli seguenti:

* Per ulteriori informazioni sugli oggetti applicazione e sulle entità servizio nella piattaforma di identità Microsoft, vedere [come e perché le applicazioni vengono aggiunte a Azure ad](active-directory-how-applications-are-added.md).
* Per altre informazioni sulle app a tenant singolo e sulle app multi-tenant, vedere tenant [in Azure Active Directory](single-and-multi-tenant-apps.md).
* Per altre informazioni sul modo in cui Azure AD fornisce anche Azure Active Directory B2C in modo che le organizzazioni possano accedere agli utenti, in genere clienti, usando identità social come un account Google, vedere la [documentazione di Azure Active Directory B2C](../../active-directory-b2c/index.yml).