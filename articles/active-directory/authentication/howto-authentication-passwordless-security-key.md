---
title: Accesso alla chiave di sicurezza senza password-Azure Active Directory
description: Abilitare l'accesso alla chiave di sicurezza senza password per Azure AD usando le chiavi di sicurezza FIDO2
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
ms.openlocfilehash: 71a29a2a7e379e253b52813eb7a76f669abbf668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653831"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Abilitare l'accesso con chiave di sicurezza senza password 

Per le aziende che usano oggi le password e hanno un ambiente PC condiviso, le chiavi di sicurezza forniscono agli utenti un modo semplice per eseguire l'autenticazione senza immettere un nome utente o una password. Le chiavi di sicurezza forniscono una maggiore produttività per i ruoli di lavoro e offrono una maggiore sicurezza.

Questo documento è incentrato sull'abilitazione dell'autenticazione senza password basata sulla chiave di sicurezza. Alla fine di questo articolo, sarà possibile accedere alle applicazioni basate sul Web con l'account Azure AD usando una chiave di sicurezza FIDO2.

## <a name="requirements"></a>Requisiti

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Abilitare la [registrazione delle informazioni di sicurezza combinate](concept-registration-mfa-sspr-combined.md)
- [Chiavi di sicurezza FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibili
- Webauthn richiede Windows 10 versione 1903 o successiva * *

Per usare le chiavi di sicurezza per l'accesso a servizi e app Web, è necessario disporre di un browser che supporti il protocollo webauthn. Sono inclusi Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices"></a>Preparare i dispositivi

Per i dispositivi Azure AD aggiunti, la migliore esperienza è in Windows 10 versione 1903 o successiva.

Azure AD ibrido dispositivi aggiunti devono eseguire Windows 10 versione 2004 o successiva.

## <a name="enable-passwordless-authentication-method"></a>Abilita metodo di autenticazione con password

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sulla funzionalità di registrazione combinata. Per abilitare la registrazione combinata, seguire la procedura descritta nell'articolo [abilitare la registrazione delle informazioni di sicurezza combinata](howto-registration-mfa-sspr-combined.md).

### <a name="enable-fido2-security-key-method"></a>Abilitare il metodo della chiave di sicurezza FIDO2

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Individuare i   >    >  criteri del metodo di autenticazione Azure Active Directory **metodi di autenticazione** di sicurezza  >  .
1. Sotto il metodo **FIDO2 Security Key** scegliere le opzioni seguenti:
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. **Salvare** la configurazione.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrazione e gestione degli utenti delle chiavi di sicurezza FIDO2

1. Passare a [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Accedere se non è già stato fatto.
1. Fare clic su **info di sicurezza**.
   1. Se l'utente dispone già di almeno un Azure AD Multi-Factor Authentication metodo registrato, può registrare immediatamente una chiave di sicurezza FIDO2.
   1. Se non hanno almeno un Azure AD Multi-Factor Authentication metodo registrato, è necessario aggiungerne uno.
1. Aggiungere una chiave di sicurezza FIDO2 facendo clic su **Aggiungi metodo** e scegliendo **chiave di sicurezza**.
1. Scegliere **dispositivo USB** o **dispositivo NFC**.
1. Preparare la chiave e scegliere **Avanti**.
1. Verrà visualizzata una casella per chiedere all'utente di creare/immettere un PIN per la chiave di sicurezza, quindi eseguire il gesto necessario per la chiave, ovvero biometrica o touch.
1. L'utente verrà restituito all'esperienza di registrazione combinata e chiederà di fornire un nome significativo per la chiave in modo che l'utente possa identificare quello che ha più. Fare clic su **Avanti**.
1. Fare clic su **fine** per completare il processo.

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Nell'esempio seguente un utente ha già eseguito il provisioning della chiave di sicurezza FIDO2. L'utente può scegliere di accedere al Web con la chiave di sicurezza FIDO2 all'interno di un browser supportato in Windows 10 versione 1903 o successiva.

![Accesso alla chiave di sicurezza Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Risoluzione dei problemi e feedback

Per condividere commenti e suggerimenti o riscontrare problemi con questa funzionalità, condividere tramite l'app hub di feedback di Windows seguendo questa procedura:

1. Avviare **feedback Hub** e assicurarsi di aver eseguito l'accesso.
1. Inviare commenti e suggerimenti nella categorizzazione seguente:
   - Categoria: sicurezza e privacy
   - Sottocategoria: FIDO
1. Per acquisire i log, utilizzare l'opzione per **ricreare il problema**.

## <a name="known-issues"></a>Problemi noti

### <a name="security-key-provisioning"></a>Provisioning delle chiavi di sicurezza

Il provisioning e il deprovisioning dell'amministratore delle chiavi di sicurezza non sono disponibili.

### <a name="upn-changes"></a>Modifiche dei nomi UPN

Si sta lavorando per supportare una funzionalità che consente la modifica dell'UPN nei dispositivi ibridi Azure AD aggiunti e Azure AD. Se l'UPN di un utente viene modificato, non è più possibile modificare le chiavi di sicurezza di FIDO2 per tenere conto della modifica. La risoluzione consiste nel reimpostare il dispositivo e l'utente deve eseguire di nuovo la registrazione.

## <a name="next-steps"></a>Passaggi successivi

[Chiave di sicurezza FIDO2 accesso a Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Abilitare l'autenticazione FIDO2 per le risorse locali](howto-authentication-passwordless-security-key-on-premises.md)

[Altre informazioni sulla registrazione del dispositivo](../devices/overview.md)

[Altre informazioni su Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
