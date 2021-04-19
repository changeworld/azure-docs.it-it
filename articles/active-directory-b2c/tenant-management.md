---
title: Gestire i Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Informazioni su come gestire il tenant Azure Active Directory B2C di lavoro. Informazioni sulle Azure AD supportate in Azure AD B2C, su come usare i ruoli di amministratore per gestire le risorse e su come aggiungere account aziendali e utenti guest al tenant Azure AD B2C lavoro.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715456"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Gestire il tenant Azure Active Directory B2C di lavoro

In Azure Active Directory B2C (Azure AD B2C), un tenant rappresenta la directory degli utenti consumer. Ogni Azure AD B2C tenant è distinto e separato da qualsiasi altro tenant Azure AD B2C tenant. Un tenant di Azure AD B2C è diverso da un tenant di Azure Active Directory, che potrebbe essere già disponibile. In questo articolo si apprenderà come gestire il tenant Azure AD B2C tenant.

## <a name="supported-azure-ad-features"></a>Funzionalità Azure AD supportate

Azure AD B2C si basa sulla Azure AD di rete. Le funzionalità Azure AD seguenti possono essere usate nel tenant Azure AD B2C tenant.

|Funzionalità  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Gruppi](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | I gruppi possono essere usati per gestire gli account amministrativi e utente.| I gruppi possono essere usati per gestire gli account amministrativi. [Gli account consumer](user-overview.md#consumer-user) non supportano i gruppi. |
| [Invito di guest con identità esterne](../active-directory//external-identities/add-users-administrator.md)| È possibile invitare utenti guest e configurare le funzionalità di identità esterne, ad esempio la federazione e l'accesso con account Facebook e Google. | È possibile invitare solo un account Microsoft o un Azure AD come guest al tenant Azure AD per l'accesso alle applicazioni o la gestione dei tenant. Per [gli account consumer,](user-overview.md#consumer-user)si usano Azure AD B2C flussi utente e criteri personalizzati per gestire gli utenti e l'iscrizione o l'accesso con provider di identità esterni, ad esempio Google o Facebook. |
| [Ruoli e amministratori](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Completamente supportato per gli account amministrativi e utente. | I ruoli non sono supportati con [gli account consumer](user-overview.md#consumer-user). Gli account consumer non hanno accesso ad alcuna risorsa di Azure.|
| [Nomi di dominio personalizzati](../active-directory/roles/permissions-reference.md#) |  È possibile usare Azure AD domini personalizzati solo per gli account amministrativi. | [Gli account consumer](user-overview.md#consumer-user) possono accedere con un nome utente, un numero di telefono o qualsiasi indirizzo di posta elettronica. È possibile usare [domini personalizzati](custom-domain.md) negli URL di reindirizzamento.|
| [Accesso condizionale](../active-directory/roles/permissions-reference.md#) | Completamente supportato per gli account amministrativi e utente. | Un subset di Azure AD funzionalità di accesso condizionale è supportato con gli account [consumer](user-overview.md#consumer-user) Lean come configurare Azure AD B2C [dominio personalizzato.](conditional-access-user-flow.md)|

## <a name="other-azure-resources-in-your-tenant"></a>Altre risorse di Azure nel tenant

In un tenant Azure AD B2C non è possibile effettuare il provisioning di altre risorse di Azure, ad esempio macchine virtuali, app Web di Azure o funzioni di Azure. È necessario creare queste risorse nel tenant Azure AD.

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C degli account

In un tenant di Azure AD B2C è possibile creare i tipi di account seguenti:

In un tenant Azure AD B2C, è possibile creare diversi tipi di account, come descritto nell'articolo Panoramica degli account utente [Azure Active Directory B2C.](user-overview.md)

- **Account aziendale**: un account aziendale può accedere alle risorse in un tenant; se dispone di un ruolo di amministratore, può gestire i tenant.
- **Account guest**: un account guest può essere solo un account Microsoft o un utente di Azure AD che può essere usato per accedere alle applicazioni o gestire i tenant.
- **Account consumer:** un account consumer viene usato da un utente delle applicazioni registrate con Azure AD B2C.

Per informazioni dettagliate su questi tipi di account, vedere [Panoramica degli account utente in Azure Active Directory B2C](user-overview.md). Qualsiasi utente a cui verrà assegnato di gestire il tenant Azure AD B2C deve avere un account utente Azure AD per poter accedere ai servizi correlati ad Azure. È possibile aggiungere tale utente creando un [account](#add-an-administrator-work-account) aziendale nel tenant di [](#invite-an-administrator-guest-account) Azure AD B2C o invitandolo al tenant di Azure AD B2C come utente guest.

## <a name="use-roles-to-control-resource-access"></a>Usare i ruoli per controllare l'accesso alle risorse

Quando si pianifica la strategia di controllo di accesso, è meglio assegnare agli utenti il ruolo con privilegi minimi necessario per accedere alle risorse. La tabella seguente descrive le risorse primarie nel tenant Azure AD B2C e i ruoli amministrativi più adatti per gli utenti che le gestiscono.

|Risorsa  |Descrizione  |Ruolo  |
|---------|---------|---------|
|[Registrazioni delle applicazioni](tutorial-register-applications.md) | Creare e gestire tutti gli aspetti delle registrazioni delle applicazioni Web, per dispositivi mobili e native all'interno Azure AD B2C.|[Amministratore applicazione](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Provider di identità](add-identity-provider.md)| Configurare il [provider di identità locale e](identity-provider-local.md) i provider di identità aziendali o di social network esterni. | [Amministratore dei provider di identità esterni](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Connettori API](add-api-connector.md)| Integrare i flussi utente con le API Web per personalizzare l'esperienza utente e integrarli con sistemi esterni.|[ID esterno - Amministratore degli attributi dei flussi utente](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Informazioni personalizzate distintive dell'azienda](customize-ui.md#configure-company-branding)| Personalizzare le pagine del flusso utente.| [Amministratore globale](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Attributi utente](user-flow-custom-attributes.md)| Aggiungere o eliminare attributi personalizzati disponibili per tutti i flussi utente.| [ID esterno - Amministratore degli attributi dei flussi utente](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Gestire gli utenti| Gestire [gli account consumer](manage-users-portal.md) e gli account amministrativi come descritto in questo articolo.| [Amministratore utenti](../active-directory/roles/permissions-reference.md#user-administrator)|
|Ruoli e amministratori| Gestire le assegnazioni di ruolo Azure AD B2C directory. Creare e gestire gruppi che possono essere assegnati a Azure AD B2C ruoli. |[Amministratore globale](../active-directory/roles/permissions-reference.md#global-administrator), [Amministratore ruolo con privilegi](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Flussi utente](user-flow-overview.md)|Per una configurazione rapida e l'abilitazione di attività comuni di gestione delle identità, ad esempio l'iscrizione, l'accesso e la modifica del profilo.| [ID esterno - Amministratore degli attributi dei flussi utente](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Criteri personalizzati](user-flow-overview.md)| Creare, leggere, aggiornare ed eliminare tutti i criteri personalizzati in Azure AD B2C.| [Amministratore dei criteri IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Chiavi dei criteri](policy-keys-overview.md)|Aggiungere e gestire le chiavi di crittografia per la firma e la convalida di token, segreti client, certificati e password usati nei criteri personalizzati.|[Amministratore dei set di chiavi IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Aggiungere un amministratore (account aziendale)

Per creare un nuovo account amministrativo, seguire questa procedura:

1. Accedere al portale di Azure [con](https://portal.azure.com/) autorizzazioni di amministratore globale o amministratore del ruolo con privilegi.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare **Nuovo utente**.
1. Nella pagina **Utente** immettere le informazioni per l'utente:

   - **Nome**. Obbligatorio. Nome e cognome del nuovo utente. Ad esempio, *Mary Parker.*
   - **Nome utente**. Obbligatorio. Nome utente del nuovo utente. Ad esempio: `mary@contoso.com`.
     La parte del dominio del nome utente deve usare il nome di dominio predefinito iniziale, *\<yourdomainname> .onmicrosoft.com*.
   - **Raggruppa**. Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento. 
   - **Ruolo della directory**. Se sono necessari autorizzazioni amministrative Azure AD per l'utente, è possibile aggiungerle a un ruolo di Azure AD. È possibile assegnare l'utente come amministratore globale o uno o più ruoli di amministratore limitati in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere Usare i ruoli [per controllare l'accesso alle risorse.](#use-roles-to-control-resource-access)
   - **Informazioni sul processo:** è possibile aggiungere altre informazioni sull'utente qui o eseguire questa operazione in un secondo momento. 

1. Copiare la password rigenerata automaticamente specificata nella **casella** Password . È necessario assegnare questa password all'utente per accedere per la prima volta.
1. Selezionare **Crea**.

L'utente viene creato e aggiunto al tenant Azure AD B2C. È preferibile avere almeno un account aziendale nativo per il tenant Azure AD B2C assegnato al ruolo di amministratore globale. Questo account può essere considerato un account break-glass.

## <a name="invite-an-administrator-guest-account"></a>Invitare un amministratore (account guest)

È anche possibile invitare un nuovo utente guest a gestire il tenant. L'account guest è l'opzione preferita quando l'organizzazione ha anche Azure AD perché il ciclo di vita di questa identità può essere gestito esternamente. 

Per invitare un utente, seguire questa procedura:

1. Accedere all'applicazione [portale di Azure](https://portal.azure.com/) autorizzazioni di amministratore globale o amministratore dei ruoli con privilegi.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e **selezionare Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare **Nuovo account guest.**
1. Nella pagina **Utente** immettere le informazioni per questo utente:

   - **Nome**. Obbligatorio. Nome e cognome del nuovo utente. Ad esempio, *Mary Mary .*
   - **Indirizzo di posta elettronica**. Obbligatorio. Indirizzo di posta elettronica dell'utente da invitare. Ad esempio: `mary@contoso.com`.   
   - **Messaggio personale:** si aggiunge un messaggio personale che verrà incluso nel messaggio di posta elettronica di invito.
   - **Gruppi**. Facoltativamente, è possibile aggiungere l'utente a uno o più gruppi esistenti. È anche possibile aggiungere l'utente a gruppi in un secondo momento.
   - **Ruolo della directory**. Se sono necessari autorizzazioni amministrative Azure AD per l'utente, è possibile aggiungerle a un ruolo di Azure AD. È possibile assegnare l'utente come amministratore globale o uno o più ruoli di amministratore limitati in Azure AD. Per altre informazioni sull'assegnazione di ruoli, vedere [Usare i ruoli per controllare l'accesso alle risorse.](#use-roles-to-control-resource-access)
   - **Informazioni sul processo:** è possibile aggiungere altre informazioni sull'utente qui o in un secondo momento.

1. Selezionare **Crea**.

All'utente viene inviato un messaggio di posta elettronica di invito. L'utente deve accettare l'invito per poter accedere.

### <a name="resend-the-invitation-email"></a>Inviare di nuovo il messaggio di posta elettronica di invito

Se il guest non ha ricevuto il messaggio di posta elettronica di invito o l'invito è scaduto, è possibile inviare nuovamente l'invito. In alternativa al messaggio di posta elettronica di invito, è possibile fornire a un guest un collegamento diretto per accettare l'invito. Per inviare nuovamente l'invito e ottenere il collegamento diretto:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Cercare e selezionare l'utente a cui inviare di nuovo l'invito.
1. Nella finestra **di | Nella pagina** Profilo, in **Identità** selezionare **(Gestisci)**.
    
    ![Screenshot che mostra come inviare di nuovo l'indirizzo di posta elettronica di invito dell'account guest.](./media/tenant-management/guest-account-resend-invite.png)

1. Per **Invia di nuovo l'invito?** selezionare **Sì.** Quando **si è certi di voler inviare di nuovo un invito?** viene visualizzato, selezionare **Sì.**
1. Azure AD B2C invia l'invito. È anche possibile copiare l'URL di invito e fornirlo direttamente all'ospite.
    
    ![Screenshot che mostra come ottenere l'URL di invito.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

È possibile assegnare un ruolo quando si [crea un utente o](#add-an-administrator-work-account) si invita un utente [guest.](#invite-an-administrator-guest-account) È possibile aggiungere, modificare o rimuovere un ruolo per un utente:

1. Accedere all'applicazione [portale di Azure](https://portal.azure.com/) con autorizzazioni di amministratore globale o amministratore dei ruoli con privilegi.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e **selezionare Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare l'utente per cui si vogliono modificare i ruoli. Selezionare quindi **Ruoli assegnati.**
1. Selezionare **Aggiungi assegnazioni,** selezionare il ruolo da assegnare ( ad esempio, Amministratore *applicazione*) e quindi scegliere **Aggiungi**.

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Se è necessario rimuovere un'assegnazione di ruolo da un utente, seguire questa procedura:

1. Selezionare **Azure AD B2C**, **utenti** e quindi cercare e selezionare l'utente.
1. Selezionare **Ruoli assegnati.** Selezionare il ruolo da rimuovere, ad esempio *Amministratore applicazione*, quindi selezionare **Rimuovi assegnazione**.

## <a name="review-administrator-account-role-assignments"></a>Esaminare le assegnazioni di ruolo dell'account amministratore

Come parte di un processo di controllo, in genere si esaminano gli utenti assegnati a ruoli specifici nella directory Azure AD B2C. Usare la procedura seguente per controllare a quali utenti sono attualmente assegnati ruoli con privilegi.

1. Accedere all'applicazione [portale di Azure](https://portal.azure.com/) autorizzazioni di amministratore globale o amministratore dei ruoli con privilegi.
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e **selezionare Azure AD B2C**.
1. In **Gestisci** selezionare **Ruoli e amministratori.**
1. Selezionare un ruolo, ad esempio **amministratore globale**. Ruolo **| Nella pagina Assegnazioni** sono elencati gli utenti con tale ruolo.

## <a name="delete-an-administrator-account"></a>Eliminare un account amministratore

Per eliminare un utente esistente, è necessario disporre di *un'amministratore globale* di ruolo. Gli amministratori globali possono eliminare qualsiasi utente, inclusi altri amministratori. *Gli amministratori utenti* possono eliminare qualsiasi utente non amministratore.

1. Nella directory Azure AD B2C selezionare **Utenti** e quindi selezionare l'utente da eliminare.
1. Selezionare **Elimina** e quindi **Sì per** confermare l'eliminazione.

L'utente viene eliminato e non è più visualizzato nella pagina **Utenti - Tutti gli utenti**. L'utente può essere visualizzato nella pagina **Utenti eliminati** per i 30 giorni successivi e durante tale periodo può essere ripristinato. Per altre informazioni sul ripristino di un utente, vedere [Ripristinare o rimuovere](../active-directory/fundamentals/active-directory-users-restore.md)un utente eliminato di recente usando Azure Active Directory .

## <a name="protect-administrative-accounts"></a>Proteggere gli account amministrativi

È consigliabile proteggere tutti gli account amministratore con Multi-Factor Authentication (MFA) per una maggiore sicurezza. L'autenticazione a più fattori è un processo di verifica dell'identità durante l'accesso che richiede all'utente un'identificazione più specifica, ad esempio un codice di verifica nel dispositivo mobile o una richiesta nell'app Microsoft Authenticator app.

![Metodi di autenticazione in uso nello screenshot di accesso](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

È possibile abilitare le [Azure AD di sicurezza predefinite](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) per forzare l'uso dell'autenticazione a più fattori in tutti gli account amministrativi.



## <a name="next-steps"></a>Passaggi successivi

- [Creare un tenant di Azure Active Directory B2C nel portale di Azure](tutorial-create-tenant.md)

