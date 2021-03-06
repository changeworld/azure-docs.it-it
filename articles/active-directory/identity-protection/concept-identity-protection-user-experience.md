---
title: Esperienze utente con Azure AD Identity Protection
description: Esperienza utente di Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835986"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Esperienze utente con Azure AD Identity Protection

Con Azure Active Directory Identity Protection è possibile:

* Richiedi agli utenti di registrarsi per Azure AD Multi-Factor Authentication (autenticazione a più fattori)
* Automatizzare la correzione di accessi a rischio e utenti compromessi

Tutti i criteri di Identity Protection hanno un effetto sull'esperienza di accesso per gli utenti. Consentire agli utenti di effettuare la registrazione e usare strumenti come Azure AD autenticazione a più fattori e la reimpostazione della password self-service possono ridurre l'effetto. Questi strumenti con le scelte appropriate per i criteri forniscono agli utenti un'opzione di correzione automatica quando necessario.

## <a name="multi-factor-authentication-registration"></a>Registrazione per l'autenticazione a più fattori

L'abilitazione dei criteri di Identity Protection che richiedono la registrazione di multi-factor authentication e la destinazione di tutti gli utenti garantisce la possibilità di usare Azure AD autenticazione a più fattori per la correzione automatica in futuro. La configurazione di questo criterio consente agli utenti di effettuare la registrazione per un periodo di 14 giorni. L'esperienza per gli utenti è illustrata di seguito. Altre informazioni sono disponibili nella documentazione dell'utente finale nell'articolo [Panoramica per la verifica a due fattori e l'account aziendale o dell'Istituto di istruzione](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Interrompi registrazione

1. Al momento dell'accesso a qualsiasi applicazione integrata Azure AD, l'utente riceve una notifica relativa alla necessità di configurare l'account per l'autenticazione a più fattori. Questo criterio viene anche attivato nell'esperienza predefinita di Windows 10 per i nuovi utenti con un nuovo dispositivo.
   
    ![Richiesta di altre informazioni](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Completare i passaggi guidati per registrare Azure AD Multi-Factor Authentication e completare l'accesso.

## <a name="risky-sign-in-remediation"></a>Monitoraggio e aggiornamento a rischio per l'accesso

Quando un amministratore ha configurato un criterio per i rischi di accesso, gli utenti interessati ricevono una notifica quando tentano di accedere e attivano il livello di rischio dei criteri. 

### <a name="risky-sign-in-self-remediation"></a>Correzione automatica dell'accesso a rischio

1. L’utente è informato del fatto che qualcosa di insolito è stato rilevato in merito al suo accesso, ad esempio l’accesso da una nuova posizione, dispositivo o app.
   
    ![Un messaggio insolito](./media/concept-identity-protection-user-experience/120.png)

1. Per dimostrare l'identità dell'utente, è necessario completare Azure AD autenticazione a più fattori con uno dei metodi precedentemente registrati. 

### <a name="risky-sign-in-administrator-unblock"></a>Sblocco dell'amministratore di accesso rischioso

Gli amministratori possono scegliere di bloccare gli utenti al momento dell'accesso, a seconda del livello di rischio. Per essere sbloccato, gli utenti finali devono contattare il personale IT oppure provare ad accedere da una posizione o un dispositivo familiare. La correzione automatica eseguendo l'autenticazione a più fattori non è un'opzione in questo caso.

![Bloccato dai criteri di rischio di accesso](./media/concept-identity-protection-user-experience/200.png)

Il personale IT può seguire le istruzioni nella sezione [sblocco degli utenti](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) per consentire agli utenti di accedere di nuovo.

## <a name="risky-user-remediation"></a>Correzione utente rischiosa

Quando è stato configurato un criterio di rischio utente, gli utenti che soddisfano la probabilità di compromissione del livello di rischio dell'utente devono passare attraverso il flusso di recupero di compromissione dell'utente prima di poter eseguire l'accesso. 

### <a name="risky-user-self-remediation"></a>Correzione automatica degli utenti a rischio

1. L'utente viene informato che la sicurezza dell'account è a rischio a causa di attività sospette o di credenziali perse.
   
    ![Soluzione](./media/concept-identity-protection-user-experience/101.png)

1. Per dimostrare l'identità dell'utente, è necessario completare Azure AD autenticazione a più fattori con uno dei metodi precedentemente registrati. 
1. Infine, l'utente deve modificare la password usando la reimpostazione della password self-service perché qualcun altro potrebbe avere accesso al proprio account.

## <a name="risky-sign-in-administrator-unblock"></a>Sblocco dell'amministratore di accesso rischioso

Gli amministratori possono scegliere di bloccare gli utenti al momento dell'accesso, a seconda del livello di rischio. Per essere sbloccato, gli utenti finali devono contattare il personale IT. In questo caso, la correzione automatica tramite l'autenticazione a più fattori e la reimpostazione della password self-service non è un'opzione.

![Bloccato dai criteri di rischio utente](./media/concept-identity-protection-user-experience/104.png)

Il personale IT può seguire le istruzioni nella sezione [sblocco degli utenti](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) per consentire agli utenti di accedere di nuovo.

## <a name="see-also"></a>Vedi anche

- [Correggere i rischi e sbloccare gli utenti](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)