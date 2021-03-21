---
title: 'Avvio rapido: Creare un tenant di Azure Active Directory'
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come creare un tenant di Azure Active Directory da usare nello sviluppo di applicazioni che usano Microsoft Identity Platform per l'autenticazione e l'autorizzazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535162"
---
# <a name="quickstart-set-up-a-tenant"></a>Avvio rapido: Configurare un tenant

Per compilare app che usano la piattaforma Microsoft Identity per la gestione delle identità e dell'accesso, è necessario accedere a un *tenant* di Azure Active Directory (Azure ad). Si trova nel tenant Azure AD che si registrano e gestiscono le app, si configura l'accesso ai dati in Microsoft 365 e altre API Web e si abilitano funzionalità come l'accesso condizionale.

Un tenant rappresenta un'organizzazione. Si tratta di un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve all'inizio di una relazione con Microsoft. Questa relazione può iniziare con l'iscrizione ad Azure, Microsoft Intune o Microsoft 365, ad esempio.

Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Ha una propria rappresentazione delle identità aziendali e dell'Istituto di istruzione, delle identità degli utenti (se si tratta di un tenant Azure AD B2C) e delle registrazioni dell'app. Una registrazione dell'app all'interno del tenant può consentire l'autenticazione solo da account all'interno del tenant o di tutti i tenant.

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Determinazione del tipo di ambiente

È possibile creare due tipi di ambienti. L'ambiente dipende esclusivamente dai tipi di utenti che l'app eseguirà l'autenticazione. 

Questa Guida introduttiva affronta due scenari per il tipo di app che si vuole compilare:

* Account aziendali e dell'Istituto di istruzione (Azure AD) o account Microsoft (ad esempio Outlook.com e Live.com)
* Account social e locali (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Account aziendali e dell'istituto di istruzione o account Microsoft personali

Per creare un ambiente per account aziendali o dell'Istituto di istruzione o account Microsoft personali, è possibile usare un tenant di Azure AD esistente o crearne uno nuovo.
### <a name="use-an-existing-azure-ad-tenant"></a>Usare un tenant Azure AD esistente

Molti sviluppatori hanno già tenant attraverso servizi o sottoscrizioni collegati a Azure AD tenant, ad esempio Microsoft 365 o sottoscrizioni di Azure.

Per controllare il tenant:

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>. Usare l'account che verrà usato per gestire l'applicazione.
1. Controllare l'angolo superiore destro. Se si dispone di un tenant, l'accesso verrà eseguito automaticamente. Il nome del tenant viene visualizzato direttamente sotto il nome dell'account.
   * Passare il puntatore del mouse sul nome dell'account per visualizzare il nome, l'indirizzo di posta elettronica, la directory o l'ID tenant (GUID) e il dominio.
   * Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID.

> [!TIP]
> Per trovare l'ID tenant, è possibile:
> * Passare il puntatore del mouse sul nome dell'account per ottenere la directory o l'ID tenant.
> * Cercare e selezionare **Azure Active Directory**  >  **proprietà**  >  **ID tenant** nell'portale di Azure.

Se non si dispone di un tenant associato all'account, verrà visualizzato un GUID sotto il nome dell'account. Non sarà possibile eseguire azioni come la registrazione di app fino a quando non si crea un tenant di Azure AD.

### <a name="create-a-new-azure-ad-tenant"></a>Creare un nuovo tenant Azure AD

Se non si ha già un tenant di Azure AD o se si vuole crearne uno nuovo per lo sviluppo, vedere [creare un nuovo tenant in Azure ad](../fundamentals/active-directory-access-create-new-tenant.md). In alternativa, usare l' [esperienza di creazione della directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) nella portale di Azure. 

Per creare il nuovo tenant, verranno fornite le informazioni seguenti:

- **Nome organizzazione**
- **Dominio iniziale** : questo dominio fa parte di *. onmicrosoft.com. È possibile personalizzare il dominio in un secondo momento.
- **Paese o area geografica**

> [!NOTE]
> Quando si assegna un nome al tenant, usare caratteri alfanumerici. I caratteri speciali non sono consentiti. Il nome non deve superare 256 caratteri.

## <a name="social-and-local-accounts"></a>Account di social e locali

Per iniziare a creare app che consentono di accedere ad account social e locali, creare un tenant di Azure AD B2C. Per iniziare, vedere [creare un tenant di Azure ad B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrare un'app](quickstart-register-app.md) da integrare con Microsoft Identity Platform.
