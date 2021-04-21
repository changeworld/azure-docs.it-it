---
title: Accesso con chiave di sicurezza senza password - Azure Active Directory
description: Abilitare l'accesso con chiave di sicurezza senza password Azure AD le chiavi di sicurezza FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a88bb7904143f69a0eea84ea291c65e3244c70a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765864"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Abilitare l'accesso con chiave di sicurezza senza password 

Per le aziende che usano attualmente le password e hanno un ambiente PC condiviso, le chiavi di sicurezza consentono ai ruoli di lavoro di eseguire l'autenticazione senza immettere un nome utente o una password. Le chiavi di sicurezza offrono una produttività migliorata per i ruoli di lavoro e offrono una maggiore sicurezza.

Questo documento è in particolare sull'abilitazione dell'autenticazione senza password basata su chiave di sicurezza. Alla fine di questo articolo sarà possibile accedere alle applicazioni basate sul Web con l'account Azure AD usando una chiave di sicurezza FIDO2.

## <a name="requirements"></a>Requisiti

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Abilitare la [registrazione combinata delle informazioni di sicurezza](concept-registration-mfa-sspr-combined.md)
- Chiavi di [sicurezza FIDO2 compatibili](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN richiede Windows 10 versione 1903 o successiva**

Per usare le chiavi di sicurezza per l'accesso ad app e servizi Web, è necessario disporre di un browser che supporti il protocollo WebAuthN. Questi includono Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices"></a>Preparare i dispositivi

Per Azure AD dispositivi aggiunti, l'esperienza migliore è Windows 10 versione 1903 o successiva.

Azure AD ibrido dispositivi aggiunti devono essere eseguiti Windows 10 versione 2004 o successiva.

## <a name="enable-passwordless-authentication-method"></a>Abilitare il metodo di autenticazione senza password

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione senza password si basano sulla funzionalità di registrazione combinata. Seguire la procedura descritta nell'articolo [Abilitare la registrazione combinata delle informazioni di sicurezza](howto-registration-mfa-sspr-combined.md)per abilitare la registrazione combinata.

### <a name="enable-fido2-security-key-method"></a>Abilitare il metodo della chiave di sicurezza FIDO2

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a Metodi **Azure Active Directory**  >  **autenticazione**  >  **di sicurezza Criteri** del metodo di  >  **autenticazione.**
1. Nel metodo **Chiave di sicurezza FIDO2** scegliere le opzioni seguenti:
   1. **Abilita** - Sì o No
   1. **Destinazione-** Tutti gli utenti o Seleziona utenti
1. **Salvare** la configurazione.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrazione e gestione degli utenti delle chiavi di sicurezza FIDO2

1. Passare a [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Se non è già stato fatto, accedere.
1. Fare clic **su Informazioni di sicurezza**.
   1. Se l'utente ha già almeno un Azure AD multi-factor authentication registrato, può registrare immediatamente una chiave di sicurezza FIDO2.
   1. Se non hanno almeno un metodo Azure AD Multi-Factor Authentication registrato, è necessario aggiungerne uno.
1. Aggiungere una chiave di sicurezza FIDO2 facendo clic **su Aggiungi metodo** e scegliendo Chiave di **sicurezza**.
1. Scegliere **Dispositivo USB** o Dispositivo **NFC.**
1. Avere la chiave pronta e scegliere **Avanti.**
1. Verrà visualizzata una casella che chiede all'utente di creare/immettere un PIN per la chiave di sicurezza, quindi eseguire il movimento necessario per la chiave, biometrica o tocco.
1. L'utente verrà restituito all'esperienza di registrazione combinata e verrà chiesto di specificare un nome significativo per la chiave in modo che l'utente possa identificare quale se ne ha più. Fare clic su **Avanti**.
1. Fare **clic su** Fine per completare il processo.

## <a name="sign-in-with-passwordless-credential"></a>Accedere con credenziali senza password

Nell'esempio seguente un utente ha già effettuato il provisioning della chiave di sicurezza FIDO2. L'utente può scegliere di accedere sul Web con la chiave di sicurezza FIDO2 all'interno di un browser supportato Windows 10 versione 1903 o successiva.

![Accesso con chiave di sicurezza Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e feedback

Se si desidera condividere commenti e suggerimenti o riscontrare problemi con questa funzionalità, condividere tramite l'app Hub di Windows Feedback seguendo questa procedura:

1. Avviare **Hub di Feedback** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti con la categorizzazione seguente:
   - Categoria: Sicurezza e privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, usare l'opzione **Per ricreare il problema.**

## <a name="known-issues"></a>Problemi noti

### <a name="security-key-provisioning"></a>Provisioning delle chiavi di sicurezza

Il provisioning e il depro provisioning delle chiavi di sicurezza da parte dell'amministratore non sono disponibili.

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>Accesso memorizzato nella cache nei Azure AD ibrido aggiunti

L'accesso memorizzato nella cache con chiavi FIDO2 ha esito negativo nei dispositivi Azure AD aggiunti Windows 10, versione 20H2. Di conseguenza, gli utenti non saranno in grado di accedere quando la linea di vista al controller di dominio locale non è disponibile. Questa operazione è attualmente in fase di analisi.

### <a name="upn-changes"></a>Modifiche dei nomi UPN

Microsoft sta lavorando per supportare una funzionalità che consente la modifica dell'UPN nei dispositivi Azure AD aggiunti e Azure AD aggiunti. Se l'UPN di un utente cambia, non è più possibile modificare le chiavi di sicurezza FIDO2 per prendere in considerazione la modifica. La soluzione è reimpostare il dispositivo e l'utente deve eseguire di nuovo la registrazione.

## <a name="next-steps"></a>Passaggi successivi

[Chiave di sicurezza FIDO2 Windows 10'accesso](howto-authentication-passwordless-security-key-windows.md)

[Abilitare l'autenticazione FIDO2 per le risorse locali](howto-authentication-passwordless-security-key-on-premises.md)

[Altre informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Altre informazioni su Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
