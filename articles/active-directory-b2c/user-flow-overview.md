---
title: Flussi utente e criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Altre informazioni sui flussi utente predefiniti e sul Framework di criteri estendibile per i criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226009"
---
# <a name="user-flows-and-custom-policies-overview"></a>Cenni preliminari sui flussi utente e sui criteri personalizzati

In Azure AD B2C, è possibile definire la logica di business seguita dagli utenti per accedere all'applicazione. Ad esempio, è possibile determinare la sequenza di passaggi seguiti dagli utenti all'accesso, all'iscrizione, alla modifica di un profilo o alla reimpostazione di una password. Dopo aver completato la sequenza, l'utente acquisisce un token e ottiene l'accesso all'applicazione. 

In Azure AD B2C, esistono due modi per fornire esperienze utente di identità:

* I **flussi utente** sono criteri predefiniti configurabili che permettono di creare esperienze di iscrizione, accesso e modifica dei criteri in pochi minuti.

* I **criteri personalizzati** consentono di creare percorsi utente personalizzati per scenari di esperienza di identità complessi.

Lo screenshot seguente mostra l'interfaccia utente delle impostazioni del flusso utente, rispetto ai file di configurazione dei criteri personalizzati.

![Screenshot mostra l'interfaccia utente delle impostazioni del flusso utente, rispetto ai file di configurazione dei criteri personalizzati.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Questo articolo fornisce una breve panoramica dei flussi utente e dei criteri personalizzati e consente di decidere quale metodo funzionerà meglio per le esigenze aziendali.

## <a name="user-flows"></a>Flussi degli utenti

Per configurare le attività di identità più comuni, il portale di Azure include diversi criteri predefiniti e configurabili detti *flussi utente*.

È possibile configurare impostazioni di flusso utente come queste per controllare i comportamenti dell'esperienza di identità nelle applicazioni:

* Tipi di account usati per l'accesso, ad esempio account di social network, come Facebook, o account locali che usano un indirizzo di posta elettronica e una password per accedere
* Attributi da raccogliere dall'utente, ad esempio nome, codice postale o paese/area geografica di residenza
* Azure AD Multi-Factor Authentication (MFA)
* Personalizzazione dell'interfaccia utente
* Set di attestazioni in un token che l'applicazione riceve dopo che l'utente ha completato il flusso utente
* Gestione delle sessioni
* ... e altro ancora

La maggior parte degli scenari di identità comuni per le app può essere definita e implementata in modo efficace con i flussi utente. Si consiglia di usare i flussi utente predefiniti, a meno che non si disponga di scenari di percorso utente complessi che richiedono la flessibilità completa dei criteri personalizzati.

## <a name="custom-policies"></a>Criteri personalizzati

I criteri personalizzati sono file di configurazione che definiscono il comportamento dell'esperienza utente di Azure AD B2C tenant. Sebbene i flussi utente siano predefiniti nel portale Azure AD B2C per le attività di identità più comuni, i criteri personalizzati possono essere modificati completamente da uno sviluppatore di identità per completare diverse attività.

Un criterio personalizzato è completamente configurabile e basato sui criteri. Orchestra la relazione di trust tra le entità nei protocolli standard. Ad esempio, OpenID Connect, OAuth, SAML e alcuni di quelli non standard, ad esempio scambi di attestazioni da sistema a sistema basati su API REST. Il Framework crea esperienze intuitive e con etichetta bianca.

I criteri personalizzati offrono la possibilità di creare percorsi utente con qualsiasi combinazione di passaggi. Ad esempio:

* Federazione con altri provider di identità
* Richieste di autenticazione a più fattori proprietarie e di terze parti
* Raccolta dell'input utente
* Integrazione con sistemi esterni tramite la comunicazione con le API REST

Ogni percorso utente è definito da un criterio. È possibile compilare tutti i criteri necessari per consentire la migliore esperienza utente per l'organizzazione.

![Diagramma che mostra un esempio di percorso utente complesso abilitato da Identity Experience Framework](media/user-flow-overview/custom-policy-diagram.png)

Un criterio personalizzato è definito da diversi file XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono lo schema delle attestazioni, le trasformazioni delle attestazioni, le definizioni di contenuto, i provider di attestazioni, i profili tecnici, i passaggi di orchestrazione dei percorsi utente e altri aspetti dell'esperienza di identità.

La potente flessibilità dei criteri personalizzati è più appropriata quando è necessario creare scenari di identità complessi. Gli sviluppatori che configurano criteri personalizzati devono definire dettagliatamente le relazioni attendibili in modo da includere gli endpoint dei metadati e le definizioni di scambio per le attestazioni esatte e configurare i segreti, le chiavi e i certificati necessari per ogni provider di identità.

Per altre informazioni sui criteri personalizzati, vedere [Criteri personalizzati in Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Confronto tra i criteri personalizzati e i flussi utente

La tabella seguente offre un confronto dettagliato degli scenari che è possibile eseguire con Azure AD B2C flussi utente e criteri personalizzati.

| Context | Flussi degli utenti | Criteri personalizzati |
|-|-------------------|-----------------|
| Utenti di destinazione | Tutti gli sviluppatori di applicazioni con o senza competenze di identità. | Professionisti di identità, integratori di sistemi, consulenti e team interni per la gestione di identità. Sono confortevoli con i flussi di OpenID Connect e comprendono i provider di identità e l'autenticazione basata sulle attestazioni. |
| Metodo di configurazione | Portale di Azure con un'interfaccia utente (UI) intuitiva. | Modifica diretta dei file XML e caricamento nel portale di Azure. |
| Personalizzazione dell'interfaccia utente | [Personalizzazione completa dell'interfaccia utente](customize-ui-with-html.md) , ad esempio HTML, CSS e [JavaScript](javascript-and-page-layout.md).<br><br>[Supporto multilingue](language-customization.md) con stringhe personalizzate. | Uguale |
| Personalizzazione degli attributi | Attributi standard e personalizzati. | Uguale |
| Gestione delle sessioni e dei token | [Personalizzare](configure-tokens.md) il comportamento delle [sessioni](session-behavior.md)e dei token. | Uguale |
| Provider di identità | Provider locale o [sociale](add-identity-provider.md) [predefinito](identity-provider-local.md) , ad esempio federazione con Azure Active Directory tenant. | OIDC, SAML e OAuth basati su standard.  L'autenticazione è inoltre possibile tramite l'integrazione con le API REST. |
| Attività relative all'identità | [Iscriversi o accedere](add-sign-up-and-sign-in-policy.md) con account di social networking locali o diversi.<br><br>[Reimpostazione della password self-service](add-password-reset-policy.md).<br><br>[Modifica del profilo](add-profile-editing-policy.md).<br><br>Autenticazione a più fattori.<br><br>Flussi di accesso ai token. | Completare le stesse attività dei flussi utente usando provider di identità personalizzati ambiti personalizzati.<br><br>Effettuare il provisioning di un account utente in un altro sistema al momento della registrazione.<br><br>Inviare un messaggio di posta elettronica di benvenuto con il proprio provider di servizi di posta elettronica.<br><br>Usare un archivio utente esterno Azure AD B2C.<br><br>Convalidare le informazioni date dall'utente con un sistema attendibile tramite un'API. |

## <a name="application-integration"></a>Integrazione di applicazioni

È possibile creare molti flussi utente o criteri personalizzati di tipi diversi nel tenant e usarli nelle applicazioni in base alle esigenze. Sia i flussi utente che i criteri personalizzati possono essere riutilizzati tra le applicazioni. Questa flessibilità consente di definire e modificare le esperienze per le identità con modifiche minime al codice o anche senza alcuna modifica al codice. 

Quando un utente vuole accedere all'applicazione, l'applicazione avvia una richiesta di autorizzazione a un flusso utente o a un endpoint fornito dal criterio personalizzato. Il flusso utente o il criterio personalizzato definisce e controlla l'esperienza dell'utente. Al termine di un flusso utente, Azure AD B2C genera un token, quindi reindirizza l'utente all'applicazione.

![App per dispositivi mobili con frecce che mostrano il flusso tra Azure AD B2C e la pagina di accesso](media/user-flow-overview/app-integration.png)

Più applicazioni possono usare lo stesso flusso utente o lo stesso criterio personalizzato. Una singola applicazione può usare più flussi utente o criteri personalizzati.

Ad esempio, per accedere a un'applicazione, l'applicazione usa il flusso utente di *iscrizione o accesso*. Dopo che l'utente ha eseguito l'accesso, potrebbe voler modificare il profilo. Per modificare il profilo, l'applicazione avvia un'altra richiesta di autorizzazione, questa volta usando il flusso utente di *modifica del profilo* .

L'applicazione attiva un flusso utente usando una richiesta di autenticazione HTTP standard che include il flusso utente o il nome del criterio personalizzato. Come risposta viene ricevuto un oggetto [token](tokens-overview.md) personalizzato.


## <a name="next-steps"></a>Passaggi successivi

- Per creare i flussi utente consigliati, seguire le istruzioni riportate in [esercitazione: creare un flusso utente](tutorial-create-user-flows.md).
- Informazioni sulle [versioni dei flussi utente in Azure ad B2C](user-flow-versions.md).
- Altre informazioni sui [criteri di Azure ad B2C personalizzati](custom-policy-overview.md).
