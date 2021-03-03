---
title: Provider di identità per Identità esterne - Azure AD
description: Informazioni su come usare Azure AD come provider di identità predefinito per la condivisione con utenti esterni.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687745"
---
# <a name="identity-providers-for-external-identities"></a>Provider di identità per Identità esterne

> [!NOTE]
> Alcune delle funzionalità descritte in questo articolo sono le funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un *provider di identità* crea, mantiene e gestisce le informazioni sulle identità fornendo al contempo servizi di autenticazione alle applicazioni. Quando si condividono app e risorse con utenti esterni, Azure AD è il provider di identità predefinito per la condivisione. Ciò significa che quando si invitano utenti esterni che hanno già un account Azure AD o Microsoft, questi possono accedere automaticamente senza ulteriori operazioni di configurazione da parte dell'utente.

Oltre agli account Azure AD, le identità esterne offrono un'ampia gamma di provider di identità.

- **Account Microsoft** (anteprima): gli utenti guest possono usare il proprio account Microsoft personale (MSA) per riscattare gli inviti di collaborazione B2B. Quando si configura un flusso utente di iscrizione self-service, è possibile aggiungere un [account Microsoft (anteprima)](microsoft-account.md) come uno dei provider di identità consentiti. Non è necessaria alcuna configurazione aggiuntiva per rendere disponibile questo provider di identità per i flussi utente.

- **Invia tramite posta elettronica un** codice di accesso monouso (anteprima): quando si riscatta un invito o si accede a una risorsa condivisa, un utente Guest può richiedere un codice temporaneo, che viene inviato al proprio indirizzo di posta elettronica. Quindi immette tale codice per continuare ad accedere. La funzionalità di accesso monouso per la posta elettronica consente di autenticare gli utenti Guest B2B quando non è possibile eseguire l'autenticazione in altri modi. Quando si configura un flusso utente di iscrizione self-service, è possibile aggiungere la **posta elettronica One-Time il codice di accesso (anteprima)** come uno dei provider di identità consentiti. Alcune configurazioni sono obbligatorie. vedere [l'autenticazione con il codice di posta elettronica](one-time-passcode.md)monouso.

- **Google**: Federazione Google consente agli utenti esterni di riscattare gli inviti dall'utente accedendo alle app con gli account Gmail personali. Federazione Google può essere usato anche nei flussi utente di iscrizione self-service. Vedere come [aggiungere Google come provider di identità](google-federation.md).
   > [!IMPORTANT]
   > **A partire dal 4 gennaio 2021**, il [supporto dell'accesso WebView verrà deprecato](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) da Google. Se si usa la federazione Google o l'iscrizione self-service con Gmail, è consigliabile [testare la compatibilità delle applicazioni line-of-business native](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: Quando si crea un'app, è possibile configurare l'iscrizione self-service e abilitare la Federazione con Facebook in modo che gli utenti possano iscriversi all'app usando i propri account Facebook. Facebook può essere usato solo per i flussi utente di iscrizione self-service e non è disponibile come opzione di accesso quando gli utenti riscattano gli inviti. Vedere come [aggiungere Facebook come provider di identità](facebook-federation.md).

- **Federazione diretta**: È anche possibile impostare la federazione diretta con qualsiasi provider di identità esterno che supporti i protocolli SAML o WS-Fed. Federazione diretta consente agli utenti esterni di riscattare gli inviti dall'utente accedendo alle app con gli account social o aziendali esistenti. Vedere come [configurare la Federazione diretta](direct-federation.md).
   > [!NOTE]
   > I provider di identità di federazione diretta non possono essere usati nei flussi utente di iscrizione self-service.

## <a name="adding-social-identity-providers"></a>Aggiunta di provider di identità basati su social network

Azure AD è abilitato per impostazione predefinita per l'iscrizione self-service, quindi gli utenti hanno sempre la possibilità di iscriversi usando un account di Azure AD. Tuttavia, è possibile abilitare altri provider di identità, inclusi i provider di identità basati su social network come Google o Facebook. Per configurare i provider di identità di social networking nel tenant di Azure AD, è necessario creare un'applicazione nel provider di identità e configurare le credenziali. Si otterrà un ID client o un ID app e un segreto client o un segreto app, che sarà quindi possibile aggiungere al tenant di Azure AD.

Dopo l'aggiunta di un provider di identità al tenant di Azure AD:

- Quando si invita un utente esterno in app o risorse all'interno dell'organizzazione, l'utente esterno può accedere usando il proprio account con tale provider di identità.
- Quando si abilita l'[iscrizione self-service](self-service-sign-up-overview.md) per le app, gli utenti esterni possono iscriversi alle app usando il proprio account con i provider di identità aggiunti. Saranno in grado di effettuare una selezione tra le opzioni dei provider di identità di social networking rese disponibili nella pagina di iscrizione:

   ![Screenshot che illustra la schermata di accesso con le opzioni Google e Facebook](media/identity-providers/sign-in-with-social-identity.png)

Per un'esperienza di accesso ottimale, creare la federazione con provider di identità ogni volta che sia possibile, in modo da offrire agli utenti guest invitati un'esperienza di accesso trasparente al momento dell'accesso alle app.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiungere provider di identità per l'accesso alle applicazioni, fare riferimento agli articoli seguenti:

- [Aggiungere l'autenticazione con password monouso per la posta elettronica](one-time-passcode.md)
- [Aggiungi Google](google-federation.md) come provider di identità di social networking consentito
- [Aggiungere Facebook](facebook-federation.md) come provider di identità di social networking consentito
- [Configurare la federazione diretta](direct-federation.md) con qualsiasi organizzazione il cui provider di identità supporti il protocollo SAML 2.0 o WS-Fed. Si noti che la federazione diretta non è un'opzione per flussi utente di iscrizione self-service.
