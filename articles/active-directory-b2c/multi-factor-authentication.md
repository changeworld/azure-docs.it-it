---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft Docs
description: Come abilitare la Multi-Factor Authentication in applicazioni rivolte agli utenti finali protette da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5d656a27017f3c7ec97362efc6207917ffcd1a56
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111248"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Abilitare l'autenticazione a più fattori in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) si integra direttamente con [Azure AD multi-factor authentication](../active-directory/authentication/concept-mfa-howitworks.md) in modo che sia possibile aggiungere un secondo livello di sicurezza per le esperienze di iscrizione e accesso nelle applicazioni. È possibile abilitare l'autenticazione a più fattori senza scrivere una singola riga di codice. Se i flussi utente di iscrizione e accesso sono già stati creati, è ancora possibile abilitare l'autenticazione a più fattori.

Questa funzionalità consente alle applicazioni di gestire scenari come:

- L'autenticazione a più fattori non viene richiesta per accedere a una sola applicazione, ma viene richiesta per accedere a un'ulteriore applicazione. Ad esempio, l'utente può accedere a un'applicazione di assicurazione auto con un account locale o di social networking, ma deve verificare il numero di telefono prima di accedere all'applicazione di assicurazione casa registrata nella stessa directory.
- L'autenticazione a più fattori non viene richiesta per accedere a un'applicazione in generale, ma viene richiesta per l'accesso alle aree sensibili. Ad esempio, l'utente può accedere a un'applicazione bancaria con un account locale o di social networking e controllare il saldo del conto, ma deve verificare il numero di telefono prima di effettuare un bonifico.

## <a name="set-multi-factor-authentication"></a>Impostare l'autenticazione a più fattori

::: zone pivot="b2c-user-flow"

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera abilitare l'autenticazione a più fattori. Ad esempio, *B2C_1_signinsignup*.
1. Selezionare **Proprietà**.
1. Nella sezione **autenticazione** a più fattori selezionare il **Metodo** di autenticazione a più fattori desiderato, quindi in **applicazione** dell'autenticazione a più fattori selezionare **Always on** oppure **[condizionale](conditional-access-user-flow.md) (scelta consigliata)**. Per condizionale, creare un criterio di [criteri di accesso condizionale](conditional-access-identity-protection-setup.md) e specificare le app a cui si vuole applicare il criterio. 
1. Selezionare Salva. L'autenticazione a più fattori è ora abilitata per questo flusso utente.

È possibile usare **Esegui ora** per verificare l'esperienza. Confermare lo scenario seguente:

Un account utente viene creato nel tenant prima che venga eseguito il passaggio di autenticazione a più fattori. Durante il passaggio, all'utente viene richiesto di fornire un numero di telefono e di verificarlo. Se la verifica ha esito positivo, il numero di telefono viene associato all'account per un utilizzo successivo. Anche se l'utente annulla o esce, al successivo accesso può venire richiesto all'utente di verificare nuovamente un numero di telefono (con l'autenticazione a più fattori abilitata).

::: zone-end

::: zone pivot="b2c-custom-policy"

Per abilitare Multi-Factor Authentication ottenere gli Starter Pack per i criteri personalizzati da GitHub, aggiornare i file XML nello Starter Pack **SocialAndLocalAccountsWithMFA** con il nome del tenant di Azure ad B2C. **SocialAndLocalAccountsWithMFA** Abilita le opzioni di autenticazione sociale, locale e a più fattori. Per ulteriori informazioni, vedere [Introduzione ai criteri personalizzati in Active Directory B2C](custom-policy-get-started.md). 

::: zone-end