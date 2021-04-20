---
title: Accesso condizionale per gli utenti di Collaborazione B2B - Azure AD
description: Informazioni su come applicare criteri di autenticazione a più fattori per Azure Active Directory utenti B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739549"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Accesso condizionale per gli utenti di Collaborazione B2B

Questo articolo descrive come le organizzazioni possono ambito dei criteri di accesso condizionale (CA) per gli utenti guest B2B per accedere alle risorse.
>[!NOTE]
>Questo flusso di autenticazione o autorizzazione è leggermente diverso per gli utenti guest rispetto agli utenti esistenti di tale provider di identità(IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Flusso di autenticazione per gli utenti guest B2B da una directory esterna

Il diagramma seguente illustra il flusso: l'immagine mostra il flusso di autenticazione per ![ gli utenti guest B2B da una directory esterna](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Passaggio | Descrizione |
|--------------|-----------------------|
| 1. | L'utente guest B2B richiede l'accesso a una risorsa. La risorsa reindirizza l'utente al tenant delle risorse, un Provider di identità attendibile.|
| 2. | Il tenant della risorsa identifica l'utente come esterno e lo reindirizza al provider di identità dell'utente guest B2B. L'utente esegue l'autenticazione primaria nel provider di identità.
| 3. | Il provider di identità dell'utente guest B2B elava un token per l'utente. L'utente viene reindirizzato al tenant della risorsa con il token . Il tenant della risorsa convalida il token e quindi valuta l'utente in base ai criteri della CA. Ad esempio, il tenant delle risorse potrebbe richiedere all'utente di Azure Active Directory (AD) Multi-Factor Authentication.
| 4. | Una volta soddisfatti tutti i criteri della CA del tenant delle risorse, il tenant delle risorse emissione del proprio token e reindirizza l'utente alla relativa risorsa.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Flusso di autenticazione per gli utenti guest B2B con passcode una sola volta

Il diagramma seguente illustra il flusso: l'immagine mostra il flusso di autenticazione per gli utenti ![ guest B2B con passcode una sola volta](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Passaggio | Descrizione |
|--------------|-----------------------|
| 1. |L'utente richiede l'accesso a una risorsa in un altro tenant. La risorsa reindirizza l'utente al tenant delle risorse, un Provider di identità attendibile.|
| 2. | Il tenant della risorsa identifica l'utente come utente [OTP (One-Time Passcode)](./one-time-passcode.md) di posta elettronica esterno e invia un messaggio di posta elettronica con OTP all'utente.|
| 3. | L'utente recupera OTP e invia il codice. Il tenant della risorsa valuta l'utente in base ai criteri della CA.
| 4. | Dopo aver soddisfatto tutti i criteri della CA, il tenant delle risorse emissione di un token e reindirizza l'utente alla relativa risorsa. |

>[!NOTE]
>Se l'utente viene da un tenant di risorse esterne, non è possibile che anche i criteri della CA IdP dell'utente guest B2B siano valutati. Al giorno d'oggi, solo i criteri della CA del tenant delle risorse si applicano ai propri guest.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Azure AD Multi-Factor Authentication per gli utenti B2B

Le organizzazioni possono applicare più criteri Azure AD Multi-Factor Authentication per gli utenti guest B2B. Questi criteri possono essere applicati a livello di tenant, app o singolo utente nello stesso modo in cui vengono abilitati per i dipendenti a tempo pieno e i membri dell'organizzazione.
Il tenant delle risorse è sempre responsabile Azure AD Multi-Factor Authentication per gli utenti, anche se l'organizzazione dell'utente guest ha funzionalità di Multi-Factor Authentication. Ecco un esempio:

1. Un amministratore o information worker di una società denominata Fabrikam invita l'utente di un'altra società denominata Contoso a usare l'applicazione Woodgrove.

2. L'app Woodgrove in Fabrikam è configurata per richiedere Azure AD Multi-Factor Authentication all'accesso.

3. Quando l'utente guest B2B di Contoso tenta di accedere a Woodgrove nel tenant di Fabrikam, viene richiesto di completare la richiesta Azure AD multi-factor authentication.

4. L'utente guest può quindi configurare il Azure AD Multi-Factor Authentication con Fabrikam e selezionare le opzioni.

5. Questo scenario funziona per qualsiasi identità: Azure AD o account Microsoft personale (MSA). Ad esempio, se l'utente in Contoso esegue l'autenticazione usando l'ID di social network.

6. Fabrikam deve avere licenze premium Azure AD sufficienti per supportare Azure AD Multi-Factor Authentication. L'utente di Contoso usa quindi questa licenza da Fabrikam. Per informazioni sulle licenze [B2B, Azure AD](./external-identities-pricing.md) modello di fatturazione per le identità esterne.

>[!NOTE]
>Azure AD Multi-Factor Authentication viene eseguita in tenancy delle risorse per garantire la prevedibilità. Quando l'utente guest esegue l'accesso, verrà visualizzata la pagina di accesso del tenant della risorsa visualizzata in background e la pagina di accesso del tenant principale e il logo dell'azienda in primo piano.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Configurare Azure AD Multi-Factor Authentication per gli utenti B2B

Per configurare Azure AD Multi-Factor Authentication per gli utenti di collaborazione B2B, guardare questo video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Gli utenti B2B Azure AD Multi-Factor Authentication per il riscatto dell'offerta

Per altre informazioni sull'esperienza Azure AD di riscatto di Multi-Factor Authentication, guardare questo video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Azure AD reimpostazione di Multi-Factor Authentication per gli utenti B2B

A questo punto, sono disponibili i cmdlet di PowerShell seguenti per la verifica degli utenti guest B2B:

1. Connessione ad Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Ottenere tutti gli utenti con metodi di identificazione

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Ecco un esempio:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Reimpostare il Azure AD multi-factor authentication per un utente specifico in modo che richieda all'utente di collaborazione B2B di impostare nuovamente i metodi di correzione. 
   Ecco un esempio:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Accesso condizionale per gli utenti B2B

Esistono diversi fattori che influenzano i criteri di CA per gli utenti guest B2B.

### <a name="device-based-conditional-access"></a>Accesso condizionale basato sul dispositivo

In CA è disponibile un'opzione per richiedere che il dispositivo di un utente sia conforme o Azure AD ibrido [aggiunto.](../conditional-access/concept-conditional-access-conditions.md#device-state-preview) Gli utenti guest B2B possono soddisfare la conformità solo se il tenant delle risorse può gestire il dispositivo. I dispositivi non possono essere gestiti da più organizzazioni alla volta. Gli utenti guest B2B non possono soddisfare l'Azure AD ibrido'aggiunta perché non hanno un account AD locale. Solo se il dispositivo dell'utente guest non è gestito, può registrare o registrare il dispositivo nel tenant delle risorse e quindi rendere il dispositivo conforme. L'utente può quindi soddisfare il controllo di concessione.

>[!Note]
>Non è consigliabile richiedere un dispositivo gestito per gli utenti esterni.

### <a name="mobile-application-management-policies"></a>Criteri di gestione delle applicazioni mobili

I controlli di concessione ca, ad esempio **Richiedi app client** approvate e Richiedi criteri di protezione delle **app,** richiedono che il dispositivo sia registrato nel tenant. Questi controlli possono essere applicati solo ai [dispositivi iOS e Android.](../conditional-access/concept-conditional-access-conditions.md#device-platforms) Tuttavia, nessuno di questi controlli può essere applicato agli utenti guest B2B se il dispositivo dell'utente è già gestito da un'altra organizzazione. Un dispositivo mobile non può essere registrato in più di un tenant alla volta. Se il dispositivo mobile è gestito da un'altra organizzazione, l'utente verrà bloccato. Solo se il dispositivo dell'utente guest non è gestito, può registrare il dispositivo nel tenant delle risorse. L'utente può quindi soddisfare il controllo di concessione.  

>[!NOTE]
>Non è consigliabile richiedere criteri di protezione delle app per gli utenti esterni.

### <a name="location-based-conditional-access"></a>Accesso condizionale basato sulla posizione

I [criteri basati sulla posizione basati](../conditional-access/concept-conditional-access-conditions.md#locations) sugli intervalli IP possono essere applicati se l'organizzazione che emittente dell'invito può creare un intervallo di indirizzi IP attendibili che definisce le organizzazioni partner.

I criteri possono essere applicati anche in base alle **posizioni geografiche.**

### <a name="risk-based-conditional-access"></a>Accesso condizionale basato sul rischio

I [criteri di rischio di accesso](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) vengono applicati se l'utente guest B2B soddisfa il controllo di concessione. Ad esempio, un'organizzazione potrebbe richiedere Azure AD Multi-Factor Authentication per il rischio di accesso medio o alto. Tuttavia, se un utente non è stato registrato in precedenza Azure AD Multi-Factor Authentication nel tenant delle risorse, l'utente verrà bloccato. Questa operazione viene eseguita per impedire a utenti malintenzionati di registrare le proprie credenziali Azure AD Multi-Factor Authentication in caso di compromissione della password di un utente legittimo.

I [criteri di rischio utente,](../conditional-access/concept-conditional-access-conditions.md#user-risk) tuttavia, non possono essere risolti nel tenant delle risorse. Ad esempio, se è necessaria una modifica della password per gli utenti guest ad alto rischio, questi verranno bloccati a causa dell'impossibilità di reimpostare le password nella directory delle risorse.

### <a name="conditional-access-client-apps-condition"></a>Condizione delle app client per l'accesso condizionale

[Le condizioni delle app](../conditional-access/concept-conditional-access-conditions.md#client-apps) client si comportano come per gli utenti guest B2B come per qualsiasi altro tipo di utente. Ad esempio, è possibile impedire agli utenti guest di usare protocolli di autenticazione legacy.

### <a name="conditional-access-session-controls"></a>Controlli della sessione di accesso condizionale

[I controlli sessione](../conditional-access/concept-conditional-access-session.md) si comportano allo stesso modo per gli utenti guest B2B come per qualsiasi altro tipo di utente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti sulla collaborazione Azure AD B2B:

- [Che cos'è Azure AD B2B Collaboration?](./what-is-b2b.md)
- [Identity Protection e utenti B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Prezzi delle identità esterne](https://azure.microsoft.com/pricing/details/active-directory/)
- [Domande frequenti (FAQ)](./faq.md)
