---
title: Accesso condizionale - Informazioni di sicurezza combinate - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per la registrazione delle informazioni di sicurezza
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ddd825321a3cb7947eb76c4f5f6cdbc6cb3414
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055706"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accesso condizionale: protezione della registrazione delle informazioni di sicurezza

Sicurezza quando e come gli utenti si registrano per Azure AD Multi-Factor Authentication e la reimpostazione della password self-service è possibile con le azioni dell'utente in un criterio di accesso condizionale. Questa funzionalità è disponibile per le organizzazioni che hanno abilitato la [registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità consente alle organizzazioni di gestire il processo di registrazione come qualsiasi applicazione in un criterio di accesso condizionale e di sfruttare tutta la potenza dell'accesso condizionale per garantire la sicurezza dell'esperienza. 

Alcune organizzazioni in passato potrebbero aver usato il percorso di rete attendibile o la conformità dei dispositivi come mezzo per proteggere l'esperienza di registrazione. Con l'aggiunta del [passaggio di accesso temporaneo](../authentication/howto-authentication-temporary-access-pass.md) in Azure ad, gli amministratori possono eseguire il provisioning di credenziali con limitazioni temporali per gli utenti che consentono loro di eseguire la registrazione da qualsiasi dispositivo o posizione. Le credenziali di accesso temporaneo soddisfano i requisiti di accesso condizionale per l'autenticazione a più fattori.

## <a name="create-a-policy-to-secure-registration"></a>Creare un criterio per proteggere la registrazione

I criteri seguenti si applicano agli utenti selezionati, che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata. Per i criteri è necessario che gli utenti eseguano l'autenticazione a più fattori o usino credenziali di accesso temporaneo.

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un nome per i criteri. Ad esempio, la **registrazione delle informazioni di sicurezza combinata con Tap**.
1. In **Assegnazioni** selezionare **Utenti e gruppi** e quindi gli utenti e i gruppi a cui si vogliono applicare i criteri.
   1. In **Includi** selezionare **Tutti gli utenti**.

      > [!WARNING]
      > Gli utenti devono essere abilitati per la [registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

   1. In **Escludi**.
      1. Selezionare **tutti gli utenti guest e External**.
      
         > [!NOTE]
         > Il passaggio di accesso temporaneo non funziona per gli utenti guest.

      1. Selezionare **utenti e gruppi** e scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass. 
1. In **Applicazioni cloud o azioni** selezionare **Azioni utente** e quindi **Registra le informazioni di sicurezza**.
1. In **Controlli di accesso** > **Concedi**:
   1. Selezionare **Concedi accesso**.
   1. Selezionare **Richiedi autenticazione a più fattori** .
   1. Fare clic su **Seleziona**.
1. Impostare **Abilita criterio** su **Sì**.
1. Quindi selezionare **Crea**

Gli amministratori dovranno ora emettere credenziali di accesso temporaneo ai nuovi utenti in modo che possano soddisfare i requisiti per la registrazione di autenticazione a più fattori. Per completare questa attività, vedere la sezione [creare un passaggio di accesso temporaneo nel portale di Azure ad](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass).

Le organizzazioni possono scegliere di richiedere altri controlli di concessione oltre al o al posto di **richiedere l'autenticazione** a più fattori al passaggio 6b. Quando si selezionano più controlli, assicurarsi di selezionare l'interruttore appropriato del pulsante di opzione per richiedere **tutti** o **uno** dei controlli selezionati quando si apportano queste modifiche.

### <a name="guest-user-registration"></a>Registrazione utente Guest

Per [gli utenti Guest](../external-identities/what-is-b2b.md) che devono registrarsi per l'autenticazione a più fattori nella directory, è possibile scegliere di bloccare la registrazione dall'esterno dei [percorsi di rete attendibili](concept-conditional-access-conditions.md#locations) usando la guida seguente.

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un nome per i criteri. Ad esempio, **Registrazione delle informazioni di sicurezza combinata su reti attendibili**.
1. In **Assegnazioni** selezionare **Utenti e gruppi** e quindi gli utenti e i gruppi a cui si vogliono applicare i criteri.
   1. In **Includi** selezionare **tutti gli utenti guest e External**.
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
