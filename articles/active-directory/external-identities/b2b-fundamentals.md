---
title: Procedure consigliate e consigli per Azure Active Directory B2B
description: Informazioni sulle procedure consigliate e sulle raccomandazioni per l'accesso degli utenti Guest Business-to-business (B2B) in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365634"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Procedure consigliate per Azure Active Directory B2B
Questo articolo contiene indicazioni e procedure consigliate per la collaborazione business-to-business (B2B) in Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **A partire dall'ottobre 2021**, Microsoft non supporterà più il riscatto degli inviti mediante la creazione di account Azure ad e tenant non gestiti (JIT) per gli scenari di collaborazione B2B. In quel momento, la funzionalità di accesso monouso per il codice di posta elettronica verrà attivata per tutti i tenant esistenti e abilitata per impostazione predefinita per i nuovi tenant. È in corso l'abilitazione della funzionalità di accesso monouso per la posta elettronica perché fornisce un metodo di autenticazione di fallback semplice per gli utenti guest. Tuttavia, è possibile disabilitare questa funzionalità se si sceglie di non usarla. Per informazioni dettagliate, vedere la pagina relativa all' [autenticazione del codice di posta elettronica una volta](one-time-passcode.md)


## <a name="b2b-recommendations"></a>Raccomandazioni B2B
| Recommendation | Commenti |
| --- | --- |
| Per un'esperienza di accesso ottimale, Federazione con i provider di identità | Quando possibile, eseguire la Federazione direttamente con i provider di identità per consentire agli utenti invitati di accedere alle app e alle risorse condivise senza dover creare account Microsoft (MSAs) o account Azure AD. È possibile usare la [funzionalità di Federazione di Google](google-federation.md) per consentire agli utenti Guest B2B di accedere con i propri account Google. In alternativa, è possibile usare la [funzionalità Direct Federation (Preview)](direct-federation.md) per configurare la Federazione diretta con qualsiasi organizzazione il cui provider di identità (IDP) supporta il protocollo SAML 2,0 o WS-Fed. |
| Usare la funzionalità di accesso monouso per i clienti B2B che non possono eseguire l'autenticazione in altri modi | La funzionalità di accesso monouso per il [codice di posta elettronica](one-time-passcode.md) consente di autenticare gli utenti Guest B2B quando non è possibile eseguire l'autenticazione con altri strumenti, ad esempio Azure ad, un account Microsoft (MSA) o una Federazione Google. Quando l'utente guest riscatta un invito o accede a una risorsa condivisa, può richiedere un codice temporaneo, che viene inviato all'indirizzo di posta elettronica. Quindi immette tale codice per continuare ad accedere. |
| Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso | È possibile personalizzare la pagina di accesso in modo che sia più intuitiva per gli utenti Guest B2B. Vedere come aggiungere informazioni personalizzate distintive dell' [azienda per l'accesso e le pagine del pannello di accesso](../fundamentals/customize-branding.md). |
| Aggiungere la propria informativa sulla privacy all'esperienza di riscatto utente Guest B2B | È possibile aggiungere l'URL dell'informativa sulla privacy dell'organizzazione al primo processo di riscatto dell'invito, in modo che un utente invitato debba acconsentire alle condizioni di privacy per continuare. Vedere [procedura: aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Utilizzare la funzionalità di invito bulk (anteprima) per invitare più utenti Guest B2B nello stesso momento | Invitare più utenti guest nell'organizzazione allo stesso tempo usando la funzionalità di anteprima di invito bulk nel portale di Azure. Questa funzionalità consente di caricare un file CSV per creare utenti Guest B2B e inviare inviti in blocco. Vedere [esercitazione per l'invito bulk degli utenti B2B](tutorial-bulk-invite.md). |
| Applicare i criteri di accesso condizionale per Multi-Factor Authentication (autenticazione a più fattori) | È consigliabile applicare i criteri di autenticazione a più fattori nelle app che si desidera condividere con gli utenti B2B partner. In questo modo, l'autenticazione a più fattori verrà applicata in modo coerente alle app nel tenant, indipendentemente dal fatto che l'organizzazione partner stia usando l'autenticazione a più fattori. Vedere [accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md). |
| Se si applicano criteri di accesso condizionale basato su dispositivo, usare gli elenchi di esclusione per consentire l'accesso agli utenti B2B | Se nell'organizzazione sono abilitati i criteri di accesso condizionale basati su dispositivo, i dispositivi utente Guest B2B verranno bloccati perché non sono gestiti dall'organizzazione. È possibile creare elenchi di esclusione contenenti utenti partner specifici per escluderli dal criterio di accesso condizionale basato su dispositivo. Vedere [accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md). |
| Usare un URL specifico del tenant quando si forniscono collegamenti diretti agli utenti Guest B2B | In alternativa al messaggio di posta elettronica di invito, è possibile assegnare a un utente guest un collegamento diretto all'app o al portale. Questo collegamento diretto deve essere specifico del tenant, ovvero deve includere un ID tenant o un dominio verificato, in modo che il Guest possa essere autenticato nel tenant, in cui si trova l'app condivisa. Vedere [l'esperienza di riscatto per l'utente Guest](redemption-experience.md). |
| Quando si sviluppa un'app, usare UserType per determinare l'esperienza utente Guest  | Se si sta sviluppando un'applicazione e si desidera fornire esperienze diverse per gli utenti tenant e gli utenti guest, utilizzare la proprietà UserType. L'attestazione UserType non è attualmente inclusa nel token. Le applicazioni devono usare l'API Microsoft Graph per eseguire una query sulla directory in modo che l'utente ottenga il proprio UserType. |
| Modificare la proprietà UserType *solo* se la relazione dell'utente con l'organizzazione viene modificata | Sebbene sia possibile utilizzare PowerShell per convertire la proprietà UserType per un utente da membro a Guest (e viceversa), è necessario modificare questa proprietà solo se la relazione dell'utente con l'organizzazione cambia. Vedere [proprietà di un utente Guest B2B](user-properties.md).|

## <a name="next-steps"></a>Passaggi successivi

[Gestire la condivisione B2B](delegate-invitations.md)