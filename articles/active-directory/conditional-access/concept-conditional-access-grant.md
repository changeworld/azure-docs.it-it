---
title: Concedere i controlli nei criteri di accesso condizionale - Azure Active Directory
description: Che cosa sono i controlli di concessione in un Azure AD di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530195"
---
# <a name="conditional-access-grant"></a>Accesso condizionale: Concedi

All'interno di un criterio di accesso condizionale, un amministratore può usare i controlli di accesso per concedere o bloccare l'accesso alle risorse.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattori](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blocca accesso

Blocca tiene conto di eventuali assegnazioni e impedisce l'accesso in base alla configurazione dei criteri di accesso condizionale.

Block è un controllo potente che deve essere utilizzato con le conoscenze appropriate. I criteri con istruzioni di blocco possono avere effetti collaterali imprevisti. I test e la convalida adeguati sono fondamentali prima dell'abilitazione su larga scala. Gli amministratori devono usare strumenti come la modalità [solo report](concept-conditional-access-report-only.md) accesso condizionale e lo strumento What If in [Accesso condizionale](what-if-tool.md) quando si apportano modifiche.

## <a name="grant-access"></a>Concedere l'accesso

Gli amministratori possono scegliere di applicare uno o più controlli quando concedono l'accesso. Questi controlli includono le opzioni seguenti: 

- [Richiedi multi-factor authentication (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Richiedere che il dispositivo sia contrassegnato come conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Richiedere un dispositivo aggiunto ad Azure AD ibrido](../devices/concept-azure-ad-join-hybrid.md)
- [Richiedi app client approvata](app-based-conditional-access.md)
- [Richiedere criteri di protezione dell'app](app-protection-based-conditional-access.md)
- [Richiedere la modifica della password](#require-password-change)

Quando gli amministratori scelgono di combinare queste opzioni, possono scegliere i metodi seguenti:

- Richiedi tutti i controlli selezionati (controllo **AND)**
- Richiedere uno dei controlli selezionati (controllo **OR)**

Per impostazione predefinita, l'accesso condizionale richiede tutti i controlli selezionati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Se si seleziona questa casella di controllo, gli utenti Azure AD Multi-Factor Authentication. Altre informazioni sulla distribuzione Azure AD Multi-Factor Authentication sono disponibili nell'articolo [Planning a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md).

[Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview) soddisfa il requisito per l'autenticazione a più fattori nei criteri di accesso condizionale. 

### <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai dispositivi per identificare i dispositivi che soddisfano requisiti di conformità specifici. Queste informazioni sulla conformità dei criteri vengono inoltrate da Intune Azure AD in cui l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Per altre informazioni sui criteri di conformità, vedere l'articolo Impostare le regole nei dispositivi per consentire l'accesso alle risorse [nell'organizzazione usando Intune.](/intune/protect/device-compliance-get-started)

Un dispositivo può essere contrassegnato come conforme da Intune (per qualsiasi sistema operativo del dispositivo) o da un sistema MDM di terze parti per Windows 10 dispositivi. Jamf pro è l'unico sistema MDM di terze parti supportato. Altre informazioni sull'integrazione sono disponibili nell'articolo Integrare [Jamf Pro con Intune per la conformità.](/intune/protect/conditional-access-integrate-jamf)

I dispositivi devono essere registrati Azure AD prima che possano essere contrassegnati come conformi. Altre informazioni sulla registrazione del dispositivo sono disponibili nell'articolo Informazioni [sull'identità del dispositivo.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Richiedere un dispositivo aggiunto ad Azure AD ibrido

Le organizzazioni possono scegliere di usare l'identità del dispositivo come parte dei criteri di accesso condizionale. Le organizzazioni possono richiedere che i dispositivi siano Azure AD aggiunti usando questa casella di controllo. Per altre informazioni sulle identità dei dispositivi, vedere l'articolo [Che cos'è un'identità del dispositivo?](../devices/overview.md).

Quando si usa [il flusso OAuth del codice](../develop/v2-oauth2-device-code.md)del dispositivo, il controllo richiedi concessione del dispositivo gestito o una condizione di stato del dispositivo non sono supportati. Ciò è dovuto al fatto che il dispositivo che esegue l'autenticazione non può fornire lo stato del dispositivo al dispositivo fornendo un codice e lo stato del dispositivo nel token è bloccato al dispositivo che esegue l'autenticazione. Usare invece il controllo richiedi concessione di autenticazione a più fattori.

### <a name="require-approved-client-app"></a>Richiedere app client approvata

Le organizzazioni possono richiedere che sia necessario eseguire un tentativo di accesso alle app cloud selezionate da un'app client approvata. Queste app client approvate [supportano i criteri di protezione](/intune/app-protection-policy) delle app di Intune indipendentemente da qualsiasi soluzione di gestione di dispositivi mobili (MDM).

Per sfruttare questo controllo delle concessioni, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app broker. L'app broker può essere la Microsoft Authenticator per iOS o l'Microsoft Authenticator o il portale aziendale Microsoft per dispositivi Android. Se un'app broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'App Store appropriato per installare l'app broker necessaria.

Questa impostazione si applica alle app iOS e Android seguenti:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Amministrazione di Microsoft 365

**Osservazioni:**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.
- Il requisito **Richiedi app client approvata**:
   - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
   - Per registrare il dispositivo è necessaria un'app broker. L'app broker può essere la Microsoft Authenticator per iOS o l'Microsoft Authenticator o il portale aziendale Microsoft per dispositivi Android.
- L'accesso condizionale non Microsoft Edge in modalità InPrivate come app client approvata.
- L'Azure AD Application Proxy per consentire all'app per dispositivi mobili Power BI di connettersi a Server di report di Power BI locale non è supportata con criteri di accesso condizionale che richiedono l'app Microsoft Power BI come app client approvata.

Per esempi di configurazione, vedere l'articolo Procedura: Richiedere app client approvate per l'accesso [alle app cloud con l'accesso](app-based-conditional-access.md) condizionale.

### <a name="require-app-protection-policy"></a>Richiedere criteri di protezione dell'app

Nei criteri di accesso condizionale è possibile richiedere che nell'app client siano presenti criteri di protezione [delle app](/intune/app-protection-policy) di Intune prima che l'accesso sia disponibile per le app cloud selezionate. 

Per sfruttare questo controllo delle concessioni, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app broker. L'app broker può essere Microsoft Authenticator per iOS o il Portale aziendale Microsoft per i dispositivi Android. Se un'app broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'App Store per installare l'app broker.

Le applicazioni devono disporre di **Intune SDK con** Policy **Assurance** implementato e soddisfare determinati altri requisiti per supportare questa impostazione. Gli sviluppatori che implementano applicazioni con Intune SDK possono trovare altre informazioni nella documentazione dell'SDK su questi requisiti.

Le app client seguenti sono state confermate per supportare questa impostazione:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Elenchi Microsoft (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine per Intune
- Nine Mail - Email & Calendar

> [!NOTE]
> Microsoft Teams, Microsoft Kaizala, Microsoft Skype for Business e Microsoft Visio non supportano la concessione dei criteri **di protezione delle app.** Se è necessario che queste app funzionino, usare la **concessione Richiedi** app approvate esclusivamente. L'uso della clausola o tra le due concessioni non funzionerà per queste tre applicazioni.

**Osservazioni:**

- Le app per i criteri di protezione delle app supportano la funzionalità di gestione delle applicazioni mobili di Intune con la protezione dei criteri.
- Requisiti **dei criteri di protezione delle app:**
    - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
    - Per registrare il dispositivo è necessaria un'app broker. In iOS l'app broker è Microsoft Authenticator e in Android è Portale aziendale Intune app.

Per esempi di configurazione, vedere l'articolo [Procedura:](app-protection-based-conditional-access.md) Richiedere criteri di protezione delle app e un'app client approvata per l'accesso alle app cloud con l'accesso condizionale.

### <a name="require-password-change"></a>Richiedere la modifica della password 

Quando viene rilevato un rischio utente, usando le condizioni dei criteri di rischio utente, gli amministratori possono scegliere di fare in modo che l'utente cambi in modo sicuro la password usando Azure AD reimpostazione della password self-service. Se viene rilevato un rischio utente, gli utenti possono eseguire una reimpostazione della password self-service per la correzione autonoma. L'evento di rischio utente verrà chiuso per evitare inutili problemi per gli amministratori. 

Quando a un utente viene richiesto di modificare la password, verrà prima richiesto di completare l'autenticazione a più fattori. È necessario assicurarsi che tutti gli utenti si siano registrati per l'autenticazione a più fattori, in modo che siano preparati nel caso in cui il rischio sia stato rilevato per il proprio account.  

> [!WARNING]
> Prima di attivare i criteri di rischio utente, è necessario che gli utenti abbiano prima effettuato la registrazione per la reimpostazione della password self-service. 

Esistono un paio di restrizioni quando si configura un criterio usando il controllo di modifica della password.  

1. I criteri devono essere assegnati a "tutte le app cloud". Questo impedisce a un utente malintenzionato di usare un'app diversa per modificare la password dell'utente e reimpostare il rischio dell'account, semplicemente accedendo a un'app diversa. 
1. Richiedere la modifica della password non può essere usata con altri controlli, ad esempio richiedendo un dispositivo conforme.  
1. Il controllo della modifica della password può essere usato solo con la condizione di assegnazione utente e gruppo, la condizione di assegnazione dell'app cloud (che deve essere impostata su tutti) e le condizioni di rischio dell'utente. 

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se l'organizzazione ha creato condizioni per l'utilizzo, le opzioni aggiuntive possono essere visibili sotto i controlli di concessione. Queste opzioni consentono agli amministratori di richiedere il riconoscimento delle condizioni per l'utilizzo come condizione di accesso alle risorse protette dai criteri. Altre informazioni sulle condizioni per l'utilizzo sono disponibili nell'articolo, [Azure Active Directory condizioni per l'utilizzo.](terms-of-use.md)

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: controlli sessione](concept-conditional-access-session.md)

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
