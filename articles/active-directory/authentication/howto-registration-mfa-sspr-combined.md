---
title: Abilitare la registrazione combinata delle informazioni di sicurezza - Azure Active Directory
description: Informazioni su come semplificare l'esperienza dell'utente finale con la Azure AD multi-factor authentication e la registrazione della reimpostazione della password self-service.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829576"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Abilitare la registrazione delle informazioni di sicurezza combinate in Azure Active Directory

Prima della registrazione combinata, gli utenti hanno registrato i metodi di autenticazione Azure AD multi-factor authentication e la reimpostazione della password self-service separatamente. Gli utenti erano confusi che metodi simili venivano usati Azure AD Multi-Factor Authentication e SSPR, ma dovevano registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e ottenere i vantaggi di Azure AD Multi-Factor Authentication e SSPR.

> [!NOTE]
> A partire dal 15 agosto 2020, tutti i nuovi tenant Azure AD verranno abilitati automaticamente per la registrazione combinata. I tenant creati dopo questa data non potranno usare i flussi di lavoro di registrazione legacy.

Per assicurarsi di comprendere le funzionalità e gli effetti prima di abilitare la nuova esperienza, vedere i concetti di registrazione delle informazioni [di sicurezza combinati](concept-registration-mfa-sspr-combined.md).

![Esperienza avanzata di registrazione delle informazioni di sicurezza combinata](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Per abilitare la registrazione combinata, seguire questa procedura:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.
2. Andare a **Azure Active Directory** > **Impostazioni utente** > **Gestione delle impostazioni dell'anteprima delle funzionalità per gli utenti**.
3. In **Gli utenti possono usare l'esperienza di registrazione delle informazioni di sicurezza combinata** scegliere l'abilitazione per un gruppo **selezionato** di utenti o per **tutti** gli utenti.

   ![Abilitare l'esperienza combinata per le informazioni di sicurezza per gli utenti](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Dopo aver abilitato la registrazione combinata, gli utenti che registrano o confermano il numero di telefono o l'app per dispositivi mobili tramite la nuova esperienza possono usarli per Azure AD Multi-Factor Authentication e SSPR, se questi metodi sono abilitati nei criteri Azure AD Multi-Factor Authentication e SSPR.
>
> Se si disabilita questa esperienza, gli utenti che passano alla pagina di registrazione della richiesta di autenticazione a più fattori precedente in devono eseguire l'autenticazione a più fattori prima di poter `https://aka.ms/ssprsetup` accedere alla pagina.

Se è stato configurato *l'elenco di assegnazioni* da sito a zona in Internet Explorer, i siti seguenti devono essere nella stessa area:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinata

Per proteggere quando e come gli utenti si registrano Azure AD Multi-Factor Authentication e la reimpostazione della password self-service, è possibile usare le azioni utente nei criteri di accesso condizionale. Questa funzionalità può essere abilitata nelle organizzazioni che vogliono che gli utenti si registrino per Azure AD Multi-Factor Authentication e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane.

> [!NOTE]
> Questo criterio si applica solo quando un utente accede a una pagina di registrazione combinata. Questo criterio non applica la registrazione MFA quando un utente accede ad altre applicazioni.
>
> È possibile creare criteri di registrazione MFA usando [Azure Identity Protection - Configurare i criteri MFA.](../identity-protection/howto-identity-protection-configure-mfa-policy.md)

Per altre informazioni sulla creazione di percorsi attendibili nell'accesso condizionale, vedere Qual è la condizione del percorso [nell'Azure Active Directory condizionale?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare criteri per richiedere la registrazione da un percorso attendibile

Completare la procedura seguente per creare un criterio che si applica a tutti gli utenti selezionati che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e blocca l'accesso a meno che non si connettono da una posizione contrassegnata come rete attendibile:

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**
1. Selezionare **+ Nuovi criteri**.
1. Immettere un nome per i criteri, ad esempio *Registrazione delle informazioni di sicurezza combinata su reti attendibili*.
1. In **Assegnazioni** selezionare **Utenti e gruppi**. Scegliere gli utenti e i gruppi a cui si vuole applicare questi criteri, quindi selezionare **Fatto**.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la registrazione combinata.

1. In **App Cloud o azioni** selezionare **Azioni utente**. Controllare **Registra le informazioni di sicurezza**, quindi selezionare **Fatto**.

    ![Creare criteri di accesso condizionale per controllare la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. In **Condizioni** > **Posizioni** configurare le opzioni seguenti:
   1. Configurare **Sì**.
   1. Includere **Tutte le località**.
   1. Escludere **tutti i percorsi attendibili**.
1. Selezionare **Fatto** nella finestra *Posizioni*, quindi selezionare **Fatto** nella finestra *Condizioni*.
1. In **Controlli di accesso** > **Concedi** selezionare **Blocca accesso**, quindi **Seleziona**.
1. Impostare **Abilita criteri** su **On**.
1. Per finalizzare i criteri, selezionare **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Per assistenza, vedere Risolvere i problemi relativi alla registrazione [combinata delle informazioni di](howto-registration-mfa-sspr-combined-troubleshoot.md) sicurezza o informazioni su Qual è la condizione della posizione [nell'Azure AD condizionale?](../conditional-access/location-condition.md)

Dopo aver abilitato gli utenti per la registrazione combinata, è possibile abilitare la reimpostazione [della password self-service](tutorial-enable-sspr.md) e abilitare Azure AD [Multi-Factor Authentication.](tutorial-enable-azure-mfa.md)

Se necessario, informazioni su come [forzare gli utenti a registrare nuovamente i metodi di autenticazione.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)
