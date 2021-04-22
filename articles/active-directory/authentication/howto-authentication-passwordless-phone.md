---
title: Accesso senza password con l'app Microsoft Authenticator - Azure Active Directory
description: Abilitare l'accesso senza password per Azure AD'app Microsoft Authenticator password
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861788"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Abilitare l'accesso senza password con l Microsoft Authenticator app 

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Microsoft Authenticator usa l'autenticazione basata su chiave per abilitare una credenziale utente associata a un dispositivo, in cui il dispositivo usa un PIN o biometrico. [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) usa una tecnologia simile.

Questa tecnologia di autenticazione può essere usata in qualsiasi piattaforma di dispositivi, inclusi i dispositivi mobili. Questa tecnologia può essere usata anche con qualsiasi app o sito Web che si integra con Microsoft Authentication Libraries.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Esempio di accesso del browser che richiede all'utente di approvare l'accesso.":::

Gli utenti che hanno abilitato l'accesso tramite telefono dall'app Microsoft Authenticator visualizzano un messaggio che chiede di toccare un numero nell'app. Non viene richiesto alcun nome utente o password. Per completare il processo di accesso nell'app, un utente deve quindi eseguire le azioni seguenti:

1. Trovare la corrispondenza con il numero.
2. Scegliere **Approva**.
3. Specificare il PIN o la biometria.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'accesso tramite telefono senza password con l Microsoft Authenticator app, è necessario che siano soddisfatti i prerequisiti seguenti:

- Azure AD Multi-Factor Authentication, con le notifiche push consentite come metodo di verifica.
- Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.
- Il dispositivo in cui è Microsoft Authenticator'app deve essere registrato all'interno del tenant Azure AD a un singolo utente. 

> [!NOTE]
> Se è stato abilitato Microsoft Authenticator accesso senza password usando Azure AD PowerShell, è stato abilitato per l'intera directory. Se si abilita l'uso di questo nuovo metodo, il criterio di PowerShell viene sostituito. È consigliabile abilitare per tutti gli utenti nel tenant tramite il nuovo *menu* Metodi di autenticazione, in caso contrario gli utenti non presenti nei nuovi criteri non saranno più in grado di accedere senza una password.

## <a name="enable-passwordless-authentication-methods"></a>Abilitare i metodi di autenticazione senza password

Per usare l'autenticazione senza password in Azure AD, abilitare prima di tutto l'esperienza di registrazione combinata, quindi abilitare gli utenti per il metodo senza password.

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione senza password si basano sulla funzionalità di registrazione combinata. Per consentire agli utenti di completare la registrazione combinata, seguire la procedura per abilitare [la registrazione combinata delle informazioni di sicurezza.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Abilitare i metodi di autenticazione per l'accesso tramite telefono senza password

Azure AD consente di scegliere i metodi di autenticazione che possono essere usati durante il processo di accesso. Gli utenti si registrano quindi per i metodi che vogliono usare.

Per abilitare il metodo di autenticazione per l'accesso tramite telefono senza password, seguire questa procedura:

1. Accedere al sito [portale di Azure](https://portal.azure.com) con un account *amministratore* globale.
1. Cercare e selezionare *Azure Active Directory*, quindi passare a Metodi **di** autenticazione  >  **di sicurezza**  >  **Criteri**.
1. In **Microsoft Authenticator** scegliere le opzioni seguenti:
   1. **Abilita** - Sì o No
   1. **Destinazione** - Tutti gli utenti o Seleziona utenti
1. Ogni gruppo o utente aggiunto è abilitato per impostazione predefinita per l'Microsoft Authenticator in modalità di notifica push e senza password ("Qualsiasi"). Per modificare questa impostazione, per ogni riga:
   1. Passare a **...**  >  **Configurare**.
   1. Per **la modalità di** autenticazione- Qualsiasi, Senza password o Push
1. Per applicare il nuovo criterio, selezionare **Salva.**

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Registrazione e gestione degli Microsoft Authenticator

Gli utenti si registrano per il metodo di autenticazione senza password Azure AD seguendo questa procedura:

1. Passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Accedere, quindi aggiungere l'app Authenticator selezionando Aggiungi metodo **>'app Authenticator** e **quindi Aggiungi**.
1. Seguire le istruzioni per installare e configurare l'app Microsoft Authenticator nel dispositivo.
1. Selezionare **Fine per** completare la configurazione dell'autenticatore.
1. In **Microsoft Authenticator** scegliere **Abilita accesso** tramite telefono dal menu a discesa per l'account registrato.
1. Seguire le istruzioni nell'app per completare la registrazione dell'account per l'accesso tramite telefono senza password.

Un'organizzazione può indirizzare gli utenti all'accesso con i telefoni, senza usare una password. Per altre informazioni sulla configurazione dell'app Microsoft Authenticator e sull'abilitazione dell'accesso tramite telefono, vedere Accedere agli account con [l'app Microsoft Authenticator.](../user-help/user-help-auth-app-sign-in.md)

> [!NOTE]
> Gli utenti che non sono autorizzati dai criteri a usare l'accesso tramite telefono non sono più in grado di abilitarlo all'interno dell'app Microsoft Authenticator app.

## <a name="sign-in-with-passwordless-credential"></a>Accedere con credenziali senza password

Un utente può iniziare a usare l'accesso senza password al termine di tutte le azioni seguenti:

- Un amministratore ha abilitato il tenant dell'utente.
- L'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

La prima volta che un utente avvia il processo di accesso tramite telefono, l'utente esegue la procedura seguente:

1. Immette il nome nella pagina di accesso.
2. Selezionare **Avanti**.
3. Se necessario, seleziona **Altri modi per accedere.**
4. Selezionare **Approva una richiesta nell'app Microsoft Authenticator.**

All'utente viene quindi visualizzato un numero. L'app richiede all'utente di eseguire l'autenticazione selezionando il numero appropriato anziché immettendo una password.

Dopo che l'utente ha utilizzato l'accesso tramite telefono senza password, l'app continua a guidare l'utente attraverso questo metodo. Tuttavia, l'utente visualizza l'opzione per scegliere un altro metodo.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Esempio di accesso al browser con l'app Microsoft Authenticator app.":::

## <a name="known-issues"></a>Problemi noti

Esistono i problemi noti seguenti.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Opzione non disponibile per l'accesso tramite telefono senza password

In uno scenario, un utente può avere una verifica di accesso tramite telefono senza password senza risposta in sospeso. Tuttavia, l'utente potrebbe tentare di eseguire di nuovo l'accesso. In questo caso, l'utente potrebbe visualizzare solo l'opzione per immettere una password.

Per risolvere questo scenario, è possibile usare i passaggi seguenti:

1. Aprire l'app Microsoft Authenticator.
2. Rispondere a eventuali richieste di notifica.

L'utente può quindi continuare a usare l'accesso tramite telefono senza password.

### <a name="federated-accounts"></a>Account federati

Quando un utente ha abilitato qualsiasi credenziale senza password, il Azure AD di accesso viene arrestato utilizzando l'hint di \_ accesso. Di conseguenza, il processo non accelera più l'utente verso un percorso di accesso federato.

Questa logica impedisce in genere che un utente in un tenant ibrido venga indirizzato a Active Directory Federated Services (AD FS) per la verifica dell'accesso. Tuttavia, l'utente mantiene la possibilità di fare clic **su Usa la password.**

### <a name="azure-mfa-server"></a>Server di Azure MFA

Un utente finale può essere abilitato per l'autenticazione a più fattori (MFA) tramite un server Azure MFA locale. L'utente può comunque creare e usare una singola credenziale di accesso tramite telefono senza password.

Se l'utente tenta di aggiornare più installazioni (5+) dell'app Microsoft Authenticator con le credenziali di accesso tramite telefono senza password, questa modifica potrebbe causare un errore.

### <a name="device-registration"></a>Registrazione del dispositivo

Prima di poter creare queste nuove credenziali strong, sono necessari alcuni prerequisiti. Un prerequisito è che il dispositivo in cui è installata l'app Microsoft Authenticator deve essere registrato all'interno del tenant Azure AD a un singolo utente.

Attualmente, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

> [!NOTE]
> La registrazione del dispositivo non corrisponde alla gestione dei dispositivi o alla gestione dei dispositivi mobili (MDM). La registrazione del dispositivo associa solo un ID dispositivo e un ID utente nella directory Azure AD dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni Azure AD metodi di autenticazione e senza password, vedere gli articoli seguenti:

- [Informazioni sul funzionamento dell'autenticazione senza password](concept-authentication-passwordless.md)
- [Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)
- [Informazioni su Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
