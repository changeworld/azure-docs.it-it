---
title: Limitare Azure AD app a un set di utenti | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come limitare l'accesso alle app registrate in Azure AD a un set di utenti.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f6ac11fc5c7bbe7a8f81e6ea89e2c582ebcf264
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178739"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Procedura: limitare l'app Azure AD a un set di utenti in un tenant Azure AD

Per impostazione predefinita, le applicazioni registrate in un tenant di Azure Active Directory (Azure AD) sono disponibili per tutti gli utenti del tenant che eseguono correttamente l'autenticazione.

Analogamente, nel caso di un app [multi-tenant](howto-convert-app-to-be-multi-tenant.md), tutti gli utenti del tenant di Azure AD in cui è stato eseguito il provisioning dell'app potranno accedere all'applicazione dopo aver eseguito l'autenticazione nel rispettivo tenant.

Gli sviluppatori e gli amministratori di tenant devono spesso soddisfare l'esigenza di limitare un'app a un determinato set di utenti. Gli sviluppatori possono ottenere lo stesso risultato usando modelli di autorizzazione comuni come il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), ma questo approccio richiede una notevole quantità di lavoro da parte dello sviluppatore.

Gli amministratori e gli sviluppatori tenant possono limitare un'app a un set specifico di utenti o gruppi di sicurezza nel tenant usando anche questa funzionalità incorporata di Azure AD.

## <a name="supported-app-configurations"></a>Configurazioni di app supportate

La possibilità di limitare un'app a un set specifico di utenti o gruppi di sicurezza in un tenant può essere adottata con i tipi di applicazione seguenti:

- Applicazioni configurate per Single Sign-On federative con autenticazione basata su SAML.
- Applicazioni proxy di applicazione che usano Azure AD pre-autenticazione.
- Applicazioni create direttamente sulla piattaforma applicativa Azure AD che usano l'autenticazione OAuth 2.0/OpenID Connect dopo che un utente o un amministratore ha fornito il consenso per tale applicazione.

     > [!NOTE]
     > Questa funzionalità è disponibile solo per app Web/API Web e applicazioni aziendali. Le app registrate come [native](./quickstart-register-app.md) non possono essere limitate a un set di utenti o gruppi di sicurezza all'interno del tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>Aggiornare l'app per abilitare l'assegnazione degli utenti

Esistono due modi per creare un'applicazione con l'assegnazione utente abilitata. Uno richiede il ruolo di **amministratore globale** , il secondo no.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Applicazioni aziendali (richiede il ruolo di amministratore globale)

1. Accedere al <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> portale di Azure</a> come **amministratore globale**.
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **applicazioni aziendali**  >  **tutte le applicazioni**.
1. Selezionare dall'elenco l'applicazione a cui si desidera assegnare un utente o un gruppo di sicurezza. 
    Usare i filtri nella parte superiore della finestra per cercare un'applicazione specifica.
1. Nella pagina **Panoramica** dell'applicazione, in **Gestisci**, selezionare **proprietà**.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti del tenant devono prima essere assegnati a questa applicazione o non saranno in grado di accedere a questa applicazione.
1. Selezionare **Salva**.

### <a name="app-registration"></a>Registrazione delle app

1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app**.
1. Creare o selezionare l'app che si vuole gestire. È necessario essere il **proprietario** di questa applicazione.
1. Nella pagina Panoramica dell'applicazione **selezionare il collegamento** **applicazione gestita in directory locale** nella sezione **informazioni** di base.
1. In **Gestisci** selezionare **Proprietà**.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti del tenant devono prima essere assegnati a questa applicazione o non saranno in grado di accedere a questa applicazione.
1. Selezionare **Salva**.

## <a name="assign-users-and-groups-to-the-app"></a>Assegnare utenti e gruppi all'app

Dopo aver configurato l'app per abilitare l'assegnazione degli utenti, è possibile ora assegnare all'app gli utenti e i gruppi desiderati.

1. In **Gestisci** selezionare **utenti e gruppi**  >  **Aggiungi utente/gruppo** .
1. Selezionare il selettore **utenti** . 

     Verrà visualizzato un elenco di utenti e gruppi di sicurezza, oltre a una casella di testo per cercare e trovare un determinato utente o gruppo. Questa schermata consente di selezionare contemporaneamente più utenti e gruppi.

1. Al termine della selezione di utenti e gruppi, selezionare **Seleziona**.
1. Opzionale Se nell'applicazione sono stati definiti i ruoli dell'app, è possibile usare l'opzione **Seleziona ruolo** per assegnare gli utenti e i gruppi selezionati a uno dei ruoli dell'applicazione. 
1. Selezionare **assegna** per completare le assegnazioni di utenti e gruppi all'app. 
1. Verificare che gli utenti e i gruppi aggiunti siano presenti nell'elenco **Utenti e gruppi** aggiornato.

## <a name="more-information"></a>Ulteriori informazioni

- [Procedura: aggiungere i ruoli dell'app nell'applicazione](./howto-add-app-roles-in-azure-ad-apps.md)
- [Aggiungere l'autorizzazione a un'app Web ASP.NET Core usando i ruoli dell'app e le attestazioni dei ruoli](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM) (Utilizzo dei gruppi di sicurezza e dei ruoli applicazione nelle app - Video)
- [Azure Active Directory, now with Group Claims and Application Roles](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862) (Azure Active Directory, ora con attestazioni di gruppo e ruoli applicazione)
- [Manifesto dell'app Azure Active Directory](./reference-app-manifest.md)
