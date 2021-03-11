---
title: Raccomandazioni sulla sicurezza del Centro sicurezza di Azure per l'autenticazione a più fattori
description: Informazioni su come applicare l'autenticazione a più fattori per le sottoscrizioni di Azure tramite il Centro sicurezza di Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631898"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Gestire l'applicazione dell'autenticazione a più fattori (multi-factor authentication) nelle sottoscrizioni

Se si usano solo le password per autenticare gli utenti, si lascia aperto un vettore di attacco. Spesso gli utenti usano password vulnerabili o riutilizzarle per più servizi. Se si abilita l'[autenticazione a più fattori](https://www.microsoft.com/security/business/identity/mfa), gli account sono più sicuri e gli utenti possono ancora eseguire l'autenticazione a quasi tutte le applicazioni con Single Sign-On (SSO).

Esistono diversi modi per abilitare l'autenticazione a più fattori per gli utenti di Azure Active Directory (AD) in base alle licenze di proprietà dell'organizzazione. Questa pagina fornisce i dettagli per ognuno nel contesto del Centro sicurezza di Azure.


## <a name="mfa-and-security-center"></a>Multi-factor authentication e Centro sicurezza 

Il Centro sicurezza pone un valore elevato in multi-factor authentication. Il controllo di sicurezza che contribuisce maggiormente al Punteggio sicuro è **abilitato** per l'autenticazione a più fattori. 

Le raccomandazioni del controllo Abilita autenticazione a più fattori garantiscono la conformità alle procedure consigliate per gli utenti delle sottoscrizioni:

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di scrittura per la sottoscrizione

Esistono tre modi per abilitare l'autenticazione a più fattori ed essere conformi ai due consigli del Centro sicurezza: impostazioni predefinite sicurezza, assegnazione per utente, criteri di accesso condizionale (CA). Ognuna di queste opzioni è illustrata di seguito.

### <a name="free-option---security-defaults"></a>Opzione gratuita-impostazioni predefinite di sicurezza
Se si usa l'edizione gratuita di Azure AD, usare le [impostazioni predefinite di sicurezza](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) per abilitare multi-factor authentication nel tenant.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>Multi-factor authentication per clienti Microsoft 365 Business, E3 o E5
I clienti con Microsoft 365 possono usare l' **assegnazione per singolo utente**. In questo scenario, Azure AD autenticazione a più fattori è abilitata o disabilitata per tutti gli utenti, per tutti gli eventi di accesso. Non è possibile abilitare l'autenticazione a più fattori per un subset di utenti o in determinati scenari e la gestione avviene tramite il portale di Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>Multi-factor authentication per clienti Azure AD Premium
Per un'esperienza utente migliorata, eseguire l'aggiornamento a Azure AD Premium P1 o P2 per le opzioni dei **criteri di accesso condizionale (CA)** . Per configurare un criterio dell'autorità di certificazione, è necessario disporre [delle autorizzazioni del tenant di Azure Active Directory (ad)](../active-directory/roles/permissions-reference.md).

I criteri della CA devono:
- applicare l'autenticazione a più fattori
- includere l'ID app di gestione Microsoft Azure (797f4846-BA00-4FD7-BA43-dac1f8f63013) o tutte le app
- non escludere l'ID dell'app di gestione Microsoft Azure

I clienti di **Azure ad Premium P1** possono usare Azure ad CA per chiedere agli utenti di eseguire l'autenticazione a più fattori durante determinati scenari o eventi in base alle esigenze aziendali. Altre licenze che includono questa funzionalità: Enterprise Mobility + Security E3, Microsoft 365 F1 e Microsoft 365 E3.

**Azure ad Premium P2** fornisce le funzionalità di sicurezza più sicure e un'esperienza utente migliorata. Questa licenza aggiunge l' [accesso condizionale basato sul rischio](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) alle funzionalità di Azure ad Premium P1. La CA basata sui rischi si adatta ai modelli degli utenti e riduce al minimo le richieste di autenticazione a più fattori. Altre licenze che includono questa funzionalità: Enterprise Mobility + Security E5 o Microsoft 365 E5.

Per altre informazioni, vedere la [documentazione sull'accesso condizionale di Azure](../active-directory/conditional-access/overview.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificare gli account senza multi-factor authentication (autenticazione a più fattori abilitata)

È possibile visualizzare l'elenco di account utente senza autenticazione a più fattori abilitato dalla pagina dei dettagli delle raccomandazioni del Centro sicurezza o tramite Azure Resource Graph.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Visualizzare gli account senza autenticazione a più fattori abilitata nella portale di Azure
Nella pagina Dettagli raccomandazione selezionare una sottoscrizione dall'elenco **delle risorse non integre** oppure selezionare Esegui **azione** per visualizzare l'elenco.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Visualizzare gli account senza autenticazione a più fattori abilitata usando il grafico delle risorse di Azure
Per visualizzare gli account in cui non è abilitata l'autenticazione a più fattori, usare la query di Azure Resource Graph seguente. La query restituisce tutte le risorse non integre-account-della raccomandazione "l'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni proprietario per la sottoscrizione". 

1. Aprire **Esplora grafico risorse di Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio della pagina consigli di Azure Resource Graph Explorer * *" :::

1. Immettere la query seguente e selezionare **Esegui query**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. La `additionalData` Proprietà rivela l'elenco degli ID oggetto account per gli account che non hanno l'autenticazione a più fattori applicata. 

    > [!NOTE]
    > Gli account vengono visualizzati come ID oggetto anziché come nomi di account per proteggere la privacy dei titolari dell'account.

> [!TIP]
> In alternativa, è possibile usare le valutazioni dei metodi dell'API REST del Centro sicurezza [: Get](/rest/api/securitycenter/assessments/get).


## <a name="faq---mfa-in-security-center"></a>Domande frequenti-autenticazione a più fattori nel centro sicurezza

- [Per applicare l'autenticazione a più fattori è già in uso il criterio della CA. Perché si ottengono ancora le raccomandazioni del Centro sicurezza?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Stiamo usando uno strumento di autenticazione a più fattori di terze parti per applicare l'autenticazione a più fattori. Perché si ottengono ancora le raccomandazioni del Centro sicurezza?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Perché il Centro sicurezza Mostra gli account utente senza autorizzazioni sulla sottoscrizione come "richiesta di autenticazione a più fattori"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [È in corso l'applicazione dell'autenticazione a più fattori con PIM. Perché gli account PIM sono visualizzati come non conformi?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [È possibile esentare o ignorare alcuni degli account?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Esistono limitazioni alle protezioni di identità e accesso del Centro sicurezza?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Per applicare l'autenticazione a più fattori è già in uso il criterio della CA. Perché si ottengono ancora le raccomandazioni del Centro sicurezza?
Per esaminare il motivo per cui è ancora in corso la generazione delle raccomandazioni, verificare le opzioni di configurazione seguenti nei criteri della CA multi-factor authentication:

- Sono stati inclusi gli account nella sezione **utenti** dei criteri della CA multi-factor authentication (o uno dei gruppi nella sezione **Groups** )
- L'ID app di gestione di Azure (797f4846-BA00-4FD7-BA43-dac1f8f63013) o tutte le app sono incluse nella sezione **Apps** dei criteri della CA multi-factor authentication
- L'ID app di gestione di Azure non è escluso nella sezione **app** del criterio della CA multi-factor authentication

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Stiamo usando uno strumento di autenticazione a più fattori di terze parti per applicare l'autenticazione a più fattori. Perché si ottengono ancora le raccomandazioni del Centro sicurezza?
Le raccomandazioni dell'autenticazione a più fattori del Centro sicurezza non supportano gli strumenti di autenticazione a più fattori di terze parti, ad esempio DUO.

Se le raccomandazioni sono irrilevanti per l'organizzazione, è consigliabile contrassegnarle come "mitigate" come descritto in [esenzione di risorse e consigli dal punteggio sicuro](exempt-resource.md). È anche possibile [disabilitare una raccomandazione](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Perché il Centro sicurezza Mostra gli account utente senza autorizzazioni sulla sottoscrizione come "richiesta di autenticazione a più fattori"?
Le raccomandazioni relative all'autenticazione [a più fattori del centro](../role-based-access-control/classic-administrators.md) sicurezza fanno riferimento ai ruoli di controllo degli accessi in base al ruolo di [Azure](../role-based-access-control/role-definitions-list.md) Verificare che nessuno degli account disponga di tali ruoli.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>È in corso l'applicazione dell'autenticazione a più fattori con PIM. Perché gli account PIM sono visualizzati come non conformi?
Le raccomandazioni per l'autenticazione a più fattori del Centro sicurezza non supportano attualmente gli account PIM. È possibile aggiungere questi account a un criterio di accesso condizionale nella sezione Utenti/Gruppo.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>È possibile esentare o ignorare alcuni degli account?
La possibilità di esentare alcuni account che non usano l'autenticazione a più fattori non è attualmente supportata.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Esistono limitazioni alle protezioni di identità e accesso del Centro sicurezza?
Esistono alcune limitazioni per le protezioni di identità e accesso del Centro sicurezza:

- Le raccomandazioni relative all'identità non sono disponibili per le sottoscrizioni con più di 600 account. In questi casi, queste raccomandazioni verranno elencate in "valutazioni non disponibili".
- Le raccomandazioni di identità non sono disponibili per gli agenti di amministrazione del partner Cloud Solution Provider (CSP).
- Le raccomandazioni di identità non identificano gli account gestiti con un sistema di gestione delle identità con privilegi (PIM). Se si usa uno strumento PIM, è possibile che vengano visualizzati risultati non accurati nel controllo **Gestisci accesso e autorizzazioni** .


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere l'articolo seguente:

- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)