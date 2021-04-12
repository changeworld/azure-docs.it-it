---
title: Esperienze di consenso per l'app Azure AD
titleSuffix: Microsoft identity platform
description: Altre informazioni sulle esperienze di consenso di Azure AD per sapere come sfruttarne le potenzialità per la gestione e lo sviluppo di applicazioni in Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105414"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Informazioni sulle esperienze di consenso per le applicazioni di Azure AD

Approfondendo il concetto di esperienza di consenso utente per le applicazioni di Azure Active Directory (Azure AD), è possibile gestire in modo intelligente le applicazioni dell'organizzazione e/o sviluppare applicazioni con un'esperienza di consenso più semplice.

## <a name="consent-and-permissions"></a>Consenso e autorizzazioni

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. A un amministratore o un utente può essere chiesto il consenso per permettere l'accesso ai dati individuali o a quelli dell'organizzazione.

L'esperienza utente effettiva per la concessione del consenso può variare a seconda dei criteri impostati per il tenant dell'utente, dell'ambito di autorità (o ruolo) dell'utente e del tipo di [autorizzazioni](v2-permissions-and-consent.md) richieste dall'applicazione client. In altre parole, l'esperienza di consenso ricade in parte sotto il controllo degli sviluppatori di applicazioni e degli amministratori del tenant. Gli amministratori hanno la possibilità di impostare e disabilitare i criteri per un tenant o un'app per controllare l'esperienza di consenso nel tenant. Gli sviluppatori di applicazioni possono stabilire quali tipi di autorizzazioni sono richieste e se vogliono fornire agli utenti informazioni dettagliate attraverso il flusso di consenso utente o il flusso di consenso amministratore.

- Il **flusso di consenso utente** è quello per cui uno sviluppatore di applicazioni indirizza gli utenti all'endpoint di autorizzazione allo scopo di registrare il consenso per il solo utente corrente.
- Il **flusso di consenso amministratore** è quello per cui uno sviluppatore di applicazioni indirizza gli utenti all'endpoint di consenso amministratore allo scopo di registrare il consenso per l'intero tenant. Per garantire il corretto funzionamento del flusso di consenso amministratore, gli sviluppatori di applicazioni devono inserire l'elenco di tutte le autorizzazioni nella proprietà `RequiredResourceAccess` del manifesto dell'applicazione. Per altre informazioni, vedere [Manifesto dell'applicazione](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Blocchi predefiniti della richiesta di consenso

La richiesta di consenso è stata pensata per garantire che gli utenti abbiano a disposizione informazioni sufficienti per determinare l'affidabilità dell'applicazione client che accede a risorse protette per loro conto. La comprensione dei blocchi predefiniti aiuterà gli utenti che concedono il consenso a prendere decisioni più informate e gli sviluppatori a creare esperienze utente più efficaci.

La figura e la tabella seguenti includono informazioni sui blocchi predefiniti della richiesta di consenso.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Blocchi predefiniti della richiesta di consenso":::

| # | Componente | Scopo |
| ----- | ----- | ----- |
| 1 | ID utente | Questo ID rappresenta l'utente per conto del quale l'applicazione client sta richiedendo di accedere a risorse protette. |
| 2 | Titolo | Il titolo varia a seconda che gli utenti siano inseriti nel flusso di consenso utente o nel flusso di consenso amministratore. Nel flusso di consenso utente il titolo sarà "Autorizzazioni richieste", mentre nel flusso di consenso amministratore conterrà la riga aggiuntiva "Accetta per l'organizzazione". |
| 3 | Logo dell'app | Questa immagine deve consentire agli utenti di avere un'indicazione visiva se l'app in questione è quella a cui intendevano accedere. L'immagine viene fornita dagli sviluppatori dell'applicazione e la relativa proprietà non è convalidata. |
| 4 | Nome app | Questo valore deve comunicare agli utenti il nome dell'applicazione che sta richiedendo l'accesso ai dati. Si noti che il nome dell'app viene fornito dagli sviluppatori e la relativa proprietà non è convalidata. |
| 5 | Dominio dell'entità di pubblicazione | Questo valore deve fornire agli utenti un dominio di cui possano essere in grado di valutare il livello di affidabilità. Il dominio dell'entità di pubblicazione viene fornito dagli sviluppatori e la relativa proprietà è convalidata. |
| 6 | Server di pubblicazione verificato | Il badge "verificato" blu indica che l'autore dell'app ha verificato la propria identità usando un account di Microsoft Partner Network e ha completato il processo di verifica.|
| 7 | Informazioni sull'editore  | Indica se l'applicazione è pubblicata da Microsoft o dall'organizzazione. |
| 8 | Autorizzazioni | Questo elenco contiene le autorizzazioni richieste dall'applicazione client. Gli utenti devono sempre valutare i tipi di autorizzazioni richieste per comprendere, in caso di accettazione, a quali dati l'applicazione client sarà autorizzata ad accedere per loro conto. Si consiglia agli sviluppatori di applicazioni di richiedere l'accesso alle autorizzazioni con privilegi minimi. |
| 9 | Descrizione dell'autorizzazione | Questo valore viene fornito dal servizio che espone le autorizzazioni. Per visualizzare le descrizioni delle autorizzazioni, è necessario attivare o disattivare la freccia di espansione accanto all'autorizzazione. |
| 10| Condizioni dell'app | Queste condizioni includono collegamenti alle condizioni d'uso e all'informativa sulla privacy dell'applicazione. L'entità di pubblicazione è responsabile della definizione delle regole nelle condizioni d'uso. Inoltre, deve dichiarare nell'informativa sulla privacy in che modo intende usare e condividere i dati utente. Se l'entità di pubblicazione non fornisce i collegamenti a questi valori per le applicazioni multi-tenant, verrà visualizzato un avviso in grassetto nella richiesta di consenso. |
| 11 | https://myapps.microsoft.com | Questo collegamento consente agli utenti di controllare e rimuovere le applicazioni non Microsoft che attualmente hanno accesso ai dati. |
| 12 | Segnala qui | Questo collegamento viene usato per segnalare un'app sospetta se non si considera attendibile l'app, se si ritiene che l'app rappresenti un'altra app, se si ritiene che l'App utilizzerà i dati in modo non corretto o per qualche altro motivo. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>L'app richiede un'autorizzazione nell'ambito dell'autorità dell'utente

Uno scenario di consenso comune è che l'utente accede a un'app che richiede un set di autorizzazioni che rientra nell'ambito dell'autorità dell'utente. L'utente viene indirizzato al flusso di consenso dell'utente.

Nella richiesta di consenso tradizionale gli amministratori visualizzeranno un controllo aggiuntivo che permetterà loro di acconsentire per conto dell'intero tenant. Per impostazione predefinita, il controllo sarà disattivato. Solo quando gli amministratori selezionano la casella verrà concesso il consenso per conto dell'intero tenant. Ad oggi questa casella di controllo viene visualizzata solo per il ruolo di amministratore globale, non per i ruoli di amministratore del cloud e amministratore dell'app.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Richiesta di consenso per lo scenario 1a":::

Gli utenti visualizzeranno la richiesta di consenso tradizionale.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Screenshot che mostra la richiesta di consenso tradizionale.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>L'app richiede un'autorizzazione al di fuori dell'ambito dell'autorità dell'utente

Un altro scenario di consenso comune è che l'utente accede a un'app che richiede almeno un'autorizzazione che esula dall'ambito dell'autorità dell'utente.

Nella richiesta di consenso tradizionale gli amministratori visualizzeranno un controllo aggiuntivo che permetterà loro di acconsentire per conto dell'intero tenant.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Richiesta di consenso per lo scenario 1a":::

Agli utenti non amministratori verrà impedito di concedere il consenso all'applicazione e verrà chiesto di chiedere all'amministratore di accedere all'app.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Screenshot della richiesta di consenso che informa l'utente di chiedere a un amministratore di accedere all'app.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>L'utente viene indirizzato al flusso di consenso dell'amministratore

Un altro scenario comune è quello in cui l'utente accede o viene indirizzato al flusso di consenso dell'amministratore.

Gli utenti amministratore visualizzeranno la richiesta di consenso amministratore. In questa richiesta il titolo e le descrizioni delle autorizzazioni risultano modificati. Le modifiche evidenziano il fatto che l'accettazione di questa richiesta concederà all'app l'accesso ai dati richiesti per conto dell'intero tenant.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Richiesta di consenso per lo scenario 3A":::

Agli utenti non amministratori verrà impedito di concedere il consenso all'applicazione e verrà chiesto di chiedere all'amministratore di accedere all'app.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Screenshot della richiesta di consenso che informa l'utente di chiedere a un amministratore di accedere all'app.":::

## <a name="next-steps"></a>Passaggi successivi

- Panoramica dettagliata di [come il framework di consenso di Azure AD implementa il consenso](./quickstart-register-app.md).
- Per informazioni più approfondite, scoprire [come un'applicazione multi-tenant può usare il framework di consenso](./howto-convert-app-to-be-multi-tenant.md) per implementare il consenso "user" e "admin", che supporta più modelli di applicazione avanzati a più livelli.
- Informazioni [su come configurare il dominio del server di pubblicazione dell'applicazione](howto-configure-publisher-domain.md).