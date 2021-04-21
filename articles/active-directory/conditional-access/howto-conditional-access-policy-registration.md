---
title: Accesso condizionale - Informazioni di sicurezza combinate - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per la registrazione delle informazioni di sicurezza
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765648"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accesso condizionale: protezione della registrazione delle informazioni di sicurezza

È possibile proteggere quando e come gli utenti si registrano Azure AD Multi-Factor Authentication e la reimpostazione della password self-service con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità è disponibile per le organizzazioni che hanno abilitato la [registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità consente alle organizzazioni di trattare il processo di registrazione come qualsiasi applicazione in un criterio di accesso condizionale e di usare tutte le funzionalità dell'accesso condizionale per proteggere l'esperienza. 

Alcune organizzazioni in passato potrebbero aver usato un percorso di rete attendibile o la conformità del dispositivo come mezzo per proteggere l'esperienza di registrazione. Con l'aggiunta [di Pass di accesso temporaneo](../authentication/howto-authentication-temporary-access-pass.md) in Azure AD, gli amministratori possono effettuare il provisioning di credenziali con tempo limitato agli utenti che consentono loro di registrarsi da qualsiasi dispositivo o percorso. Pass di accesso temporaneo credenziali soddisfano i requisiti di accesso condizionale per l'autenticazione a più fattori.

## <a name="create-a-policy-to-secure-registration"></a>Creare un criterio per proteggere la registrazione

I criteri seguenti si applicano agli utenti selezionati che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata. I criteri richiedono agli utenti di eseguire l'autenticazione a più fattori o di usare Pass di accesso temporaneo credenziali.

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un nome per i criteri. Ad esempio, **Registrazione combinata delle informazioni di sicurezza con TAP.**
1. In **Assegnazioni** selezionare **Utenti e gruppi** e quindi gli utenti e i gruppi a cui si vogliono applicare i criteri.
   1. In **Includi** selezionare **Tutti gli utenti**.

      > [!WARNING]
      > Gli utenti devono essere abilitati per la [registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

   1. In **Escludi**.
      1. Selezionare **Tutti gli utenti guest ed esterni.**
      
         > [!NOTE]
         > Pass di accesso temporaneo non funziona per gli utenti guest.

      1. Selezionare **Utenti e gruppi e** scegliere gli account di accesso di emergenza o di emergenza dell'organizzazione. 
1. In **Applicazioni cloud o azioni** selezionare **Azioni utente** e quindi **Registra le informazioni di sicurezza**.
1. In **Controlli di accesso** > **Concedi**:
   1. Selezionare **Concedi accesso**.
   1. Selezionare **Richiedi autenticazione a più fattori** .
   1. Fare clic su **Seleziona**.
1. Impostare **Abilita criterio** su **Sì**.
1. Quindi selezionare **Crea**

Gli amministratori doranno ora rilasciare Pass di accesso temporaneo credenziali ai nuovi utenti in modo che possano soddisfare i requisiti per la registrazione dell'autenticazione a più fattori. I passaggi per eseguire questa attività sono disponibili nella sezione Creare un Pass di accesso temporaneo [nel portale Azure AD .](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)

Le organizzazioni possono scegliere di richiedere altri controlli di concessione oltre o al posto di **Richiedere l'autenticazione a più fattori** nel passaggio 6b. Quando si selezionano più controlli, assicurarsi di  selezionare  l'interruttore del pulsante di opzione appropriato per richiedere tutti o uno dei controlli selezionati quando si apporta questa modifica.

### <a name="guest-user-registration"></a>Registrazione utente guest

Per [gli utenti guest](../external-identities/what-is-b2b.md) che devono registrarsi per l'autenticazione a più fattori nella directory, è possibile scegliere di bloccare la registrazione dall'esterno dei percorsi di rete [attendibili](concept-conditional-access-conditions.md#locations) usando la guida seguente.

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un nome per i criteri. Ad esempio, **Registrazione delle informazioni di sicurezza combinata su reti attendibili**.
1. In **Assegnazioni** selezionare **Utenti e gruppi** e quindi gli utenti e i gruppi a cui si vogliono applicare i criteri.
   1. In **Includi** selezionare **Tutti gli utenti guest ed esterni.**
1. In **Applicazioni cloud o azioni** selezionare **Azioni utente** e quindi **Registra le informazioni di sicurezza**.
1. In **Condizioni** > **Percorsi**:
   1. Configurare **Sì**.
   1. Includere **Tutte le località**.
   1. Escludere **Tutte le posizioni attendibili**.
   1. Selezionare **Fine** nel pannello Percorsi.
   1. Selezionare **Fine** nel pannello Condizioni.
1. In **Controlli di accesso** > **Concedi**:
   1. Selezionare **Blocca accesso**.
   1. Quindi fare clic su **Seleziona**.
1. Impostare **Abilita criteri** su **Sì**.
1. Selezionare quindi **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Richiedere agli utenti di riconfermare le informazioni di autenticazione](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
