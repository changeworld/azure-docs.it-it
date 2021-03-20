---
title: Accesso condizionale per gli utenti di collaborazione B2B-Azure AD
description: Informazioni su come applicare i criteri di autenticazione a più fattori per Azure Active Directory utenti B2B.
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
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646283"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Accesso condizionale per gli utenti di collaborazione B2B

Questo articolo descrive in che modo le organizzazioni possono definire l'ambito dei criteri di accesso condizionale per gli utenti Guest B2B per accedere alle risorse.
>[!NOTE]
>Questo flusso di autenticazione o autorizzazione è leggermente diverso per gli utenti Guest rispetto agli utenti esistenti del provider di identità (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Flusso di autenticazione per gli utenti Guest B2B da una directory esterna

Il diagramma seguente illustra il flusso: ![ immagine che mostra il flusso di autenticazione per gli utenti Guest B2B da una directory esterna](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Passaggio | Descrizione |
|--------------|-----------------------|
| 1. | L'utente Guest B2B richiede l'accesso a una risorsa. La risorsa reindirizza l'utente al tenant delle risorse, un IdP attendibile.|
| 2. | Il tenant della risorsa identifica l'utente come esterno e reindirizza l'utente all'IdP dell'utente Guest B2B. L'utente esegue l'autenticazione primaria nel provider di identità.
| 3. | L'IdP dell'utente Guest B2B rilascia un token per l'utente. L'utente viene reindirizzato di nuovo al tenant della risorsa con il token. Il tenant di risorse convalida il token e quindi valuta l'utente in base ai criteri della CA. Ad esempio, il tenant della risorsa potrebbe richiedere all'utente di eseguire Azure Active Directory (AD) Multi-Factor Authentication.
| 4. | Una volta soddisfatti tutti i criteri della CA del tenant di risorse, il tenant di risorse emette il proprio token e reindirizza l'utente alla relativa risorsa.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Flusso di autenticazione per gli utenti Guest B2B con una sola volta il codice

Il diagramma seguente illustra il flusso: ![ immagine che mostra il flusso di autenticazione per gli utenti Guest B2B con una sola volta il codice](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Passaggio | Descrizione |
|--------------|-----------------------|
| 1. |L'utente richiede l'accesso a una risorsa in un altro tenant. La risorsa reindirizza l'utente al tenant delle risorse, un IdP attendibile.|
| 2. | Il tenant della risorsa identifica l'utente come utente del codice di accesso monouso [(OTP) di posta elettronica esterno](./one-time-passcode.md) e invia all'utente un messaggio di posta elettronica con l'OTP.|
| 3. | L'utente recupera l'OTP e invia il codice. Il tenant della risorsa valuta l'utente in base ai criteri della CA.
| 4. | Una volta soddisfatti tutti i criteri della CA, il tenant di risorse rilascia un token e reindirizza l'utente alla relativa risorsa. |

>[!NOTE]
>Se l'utente si trova in un tenant di risorse esterne, non è possibile valutare anche i criteri della CA IdP dell'utente Guest B2B. Attualmente, solo i criteri di autorità di certificazione del tenant delle risorse si applicano ai relativi Guest.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication Azure AD per gli utenti B2B

Le organizzazioni possono applicare più criteri di Multi-Factor Authentication Azure AD per gli utenti Guest B2B. Questi criteri possono essere applicati a livello di tenant, app o singoli utenti nello stesso modo in cui sono abilitati per i dipendenti a tempo pieno e i membri dell'organizzazione.
Il tenant di risorse è sempre responsabile della Azure AD Multi-Factor Authentication per gli utenti, anche se l'organizzazione dell'utente Guest dispone di funzionalità di Multi-Factor Authentication. Ecco un esempio:

1. Un amministratore o un Information Worker in una società denominata Fabrikam invita l'utente di un'altra società denominata Contoso a utilizzare la Woodgrove dell'applicazione.

2. L'App Woodgrove in Fabrikam è configurata in modo da richiedere Azure AD Multi-Factor Authentication all'accesso.

3. Quando l'utente Guest B2B di Contoso tenta di accedere a Woodgrove nel tenant Fabrikam, viene richiesto di completare la Azure AD Multi-Factor Authentication richiesta di verifica.

4. L'utente Guest può quindi impostare la Azure AD Multi-Factor Authentication con Fabrikam e selezionare le opzioni.

5. Questo scenario funziona per qualsiasi identità, Azure AD o account Microsoft personale (MSA). Ad esempio, se l'utente in Contoso esegue l'autenticazione usando l'ID social.

6. Fabrikam deve disporre di licenze Azure AD Premium sufficienti che supportano Azure AD Multi-Factor Authentication. L'utente di Contoso usa quindi questa licenza da Fabrikam. Per informazioni sulle licenze B2B, vedere [modello di fatturazione per Azure ad identità esterne](./external-identities-pricing.md) .

>[!NOTE]
>Azure AD Multi-Factor Authentication viene eseguita a livello di tenant delle risorse per garantire la prevedibilità.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Configurare Azure AD Multi-Factor Authentication per gli utenti B2B

Per configurare Azure AD Multi-Factor Authentication per gli utenti di collaborazione B2B, guardare questo video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Utenti B2B Azure AD Multi-Factor Authentication per il riscatto dell'offerta

Per ulteriori informazioni sul Azure AD Multi-Factor Authentication l'esperienza di riscatto, guardare questo video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Reimpostazione Azure AD Multi-Factor Authentication per gli utenti B2B

A questo punto, i cmdlet di PowerShell seguenti sono disponibili per la verifica degli utenti Guest B2B:

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

3. Reimpostare il metodo di Multi-Factor Authentication Azure AD per un utente specifico in modo da richiedere all'utente di collaborazione B2B di impostare di nuovo i metodi di prova. 
   Ecco un esempio:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Accesso condizionale per gli utenti B2B

Esistono diversi fattori che influenzano i criteri della CA per gli utenti Guest B2B.

### <a name="device-based-conditional-access"></a>Accesso condizionale basato sul dispositivo

Nell'autorità di certificazione è disponibile un'opzione per richiedere che un [dispositivo dell'utente sia conforme o Azure ad ibrido Unito in join](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Gli utenti Guest B2B possono soddisfare solo la conformità se il tenant di risorse è in grado di gestire il dispositivo. I dispositivi non possono essere gestiti da più di un'organizzazione alla volta. Gli utenti Guest B2B non possono soddisfare il Azure AD ibrido join perché non hanno un account AD locale. Solo se il dispositivo dell'utente Guest non è gestito, può registrare o registrare il dispositivo nel tenant delle risorse e quindi rendere il dispositivo conforme. L'utente può quindi soddisfare il controllo di concessione.

>[!Note]
>Non è consigliabile richiedere un dispositivo gestito per gli utenti esterni.

### <a name="mobile-application-management-policies"></a>Criteri di gestione delle applicazioni mobili

I controlli di concessione CA, ad esempio **Richiedi app client approvate** e **richiedono criteri di protezione delle app** , devono essere registrati nel tenant. Questi controlli possono essere applicati solo a [dispositivi iOS e Android](../conditional-access/concept-conditional-access-conditions.md#device-platforms). Tuttavia, nessuno di questi controlli può essere applicato agli utenti Guest B2B se il dispositivo dell'utente è già gestito da un'altra organizzazione. Un dispositivo mobile non può essere registrato in più di un tenant alla volta. Se il dispositivo mobile è gestito da un'altra organizzazione, l'utente verrà bloccato. Solo se il dispositivo dell'utente Guest è non gestito, è possibile registrare il dispositivo nel tenant delle risorse. L'utente può quindi soddisfare il controllo di concessione.  

>[!NOTE]
>Non è consigliabile richiedere un criterio di protezione delle app per gli utenti esterni.

### <a name="location-based-conditional-access"></a>Accesso condizionale basato sulla posizione

I [criteri basati sulla posizione](../conditional-access/concept-conditional-access-conditions.md#locations) basati sugli intervalli IP possono essere applicati se l'organizzazione che invia l'invito può creare un intervallo di indirizzi IP attendibili che definisce le organizzazioni partner.

I criteri possono essere applicati anche in base a **posizioni geografiche**.

### <a name="risk-based-conditional-access"></a>Accesso condizionale basato sul rischio

Il [criterio di rischio di accesso](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) viene applicato se l'utente Guest B2B soddisfa il controllo di concessione. Ad esempio, un'organizzazione potrebbe richiedere Azure AD Multi-Factor Authentication per il rischio di accesso medio o elevato. Tuttavia, se un utente non è stato registrato in precedenza per Azure AD Multi-Factor Authentication nel tenant di risorse, l'utente verrà bloccato. Questa operazione viene eseguita per impedire agli utenti malintenzionati di registrare i propri Azure AD Multi-Factor Authentication le credenziali nel caso in cui compromettono la password di un utente legittimo.

I [criteri di rischio utente, tuttavia,](../conditional-access/concept-conditional-access-conditions.md#user-risk) non possono essere risolti nel tenant della risorsa. Ad esempio, se è necessaria una modifica della password per gli utenti guest ad alto rischio, verranno bloccati a causa dell'impossibilità di reimpostare le password nella directory delle risorse.

### <a name="conditional-access-client-apps-condition"></a>Condizione per le app client con accesso condizionale

Le [condizioni delle app client](../conditional-access/concept-conditional-access-conditions.md#client-apps) si comportano per gli utenti Guest B2B come per qualsiasi altro tipo di utente. Ad esempio, è possibile impedire agli utenti guest di usare protocolli di autenticazione legacy.

### <a name="conditional-access-session-controls"></a>Controlli della sessione di accesso condizionale

I [controlli della sessione](../conditional-access/concept-conditional-access-session.md) si comportano per gli utenti Guest B2B come per qualsiasi altro tipo di utente.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti su Azure AD collaborazione B2B:

- [Che cos'è Azure AD B2B Collaboration?](./what-is-b2b.md)
- [Identity Protection e utenti B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Prezzi delle identità esterne](https://azure.microsoft.com/pricing/details/active-directory/)
- [Domande frequenti (FAQ)](./faq.md)