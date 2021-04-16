---
title: Valutazione dell'accesso continuo in Azure AD
description: Rispondere più rapidamente alle modifiche dello stato utente con la valutazione dell'accesso continuo in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387905"
---
# <a name="continuous-access-evaluation"></a>Valutazione continua dell'accesso

La scadenza e l'aggiornamento dei token sono un meccanismo standard nel settore. Quando un'applicazione client come Outlook si connette a un servizio come Exchange Online, le richieste API vengono autorizzate usando i token di accesso OAuth 2.0. Per impostazione predefinita, i token di accesso sono validi per un'ora. Alla scadenza, il client viene reindirizzato a Azure AD per aggiornarli. Questo periodo di aggiornamento offre l'opportunità di rivalutare i criteri per l'accesso utente. Ad esempio, è possibile scegliere di non aggiornare il token a causa di un criterio di accesso condizionale o perché l'utente è stato disabilitato nella directory. 

I clienti hanno espresso preoccupazioni riguardo al ritardo tra il momento in cui cambiano le condizioni per l'utente, ad esempio il percorso di rete o il furto di credenziali, e il momento in cui è possibile applicare criteri correlati a tale modifica. È stato fatto un esperimento con l'approccio "oggetto blunt" della durata dei token ridotta, ma si è scoperto che possono ridurre le esperienze utente e l'affidabilità senza eliminare i rischi.

La risposta immediata a violazioni dei criteri o problemi di sicurezza richiede effettivamente una "conversazione" tra l'emittente del token, ad esempio Azure AD, e il relying party, ad esempio Exchange Online. Questa conversazione bidirestica offre due funzionalità importanti. Il relying party può notare quando sono state modificate le modifiche, ad esempio un client proveniente da una nuova posizione, e indicare all'autorità emittente del token. Fornisce inoltre all'autorità emittente del token un modo per indicare al relying party di interrompere il rispetto dei token per un determinato utente a causa di compromissione dell'account, disabilitazione o altri problemi. Il meccanismo per questa conversazione è la valutazione dell'accesso continuo (CAE). L'obiettivo è che la risposta sia quasi in tempo reale, ma in alcuni casi può essere osservata una latenza massima di 15 minuti a causa del tempo di propagazione degli eventi.

L'implementazione iniziale della valutazione dell'accesso continuo è incentrata su Exchange, Teams e SharePoint Online.

Per preparare le applicazioni all'uso della CAE, vedere Come usare Valutazione continua dell'accesso [API abilitate nelle applicazioni.](../develop/app-resilience-continuous-access-evaluation.md)

### <a name="key-benefits"></a>Vantaggi principali

- Terminazione utente o modifica/reimpostazione della password: la revoca della sessione utente verrà applicata quasi in tempo reale.
- Modifica del percorso di rete: i criteri del percorso di accesso condizionale verranno applicati quasi in tempo reale.
- L'esportazione di token in un computer esterno a una rete attendibile può essere impedita con i criteri del percorso di accesso condizionale.

## <a name="scenarios"></a>Scenari 

Esistono due scenari che costituiscono la valutazione dell'accesso continuo, la valutazione degli eventi critici e la valutazione dei criteri di accesso condizionale.

### <a name="critical-event-evaluation"></a>Valutazione di eventi critici

La valutazione dell'accesso continuo viene implementata consentendo ai servizi, ad esempio Exchange Online, SharePoint Online e Teams, di sottoscrivere eventi critici in Azure AD in modo che tali eventi possano essere valutati e applicati quasi in tempo reale. La valutazione degli eventi critici non si basa sui criteri di accesso condizionale, quindi è disponibile in qualsiasi tenant. Attualmente vengono valutati gli eventi seguenti:

- L'account utente è stato eliminato o disabilitato
- La password per un utente viene modificata o reimpostata
- L'autenticazione a più fattori è abilitata per l'utente
- L'amministratore revoca in modo esplicito tutti i token di aggiornamento per un utente
- Alto rischio utente rilevato da Azure AD Identity Protection

Questo processo consente lo scenario in cui gli utenti perdono l'accesso ai file, alla posta elettronica, al calendario o alle attività di SharePoint Online dell'organizzazione e a Teams dalle app client di Microsoft 365 in pochi minuti dopo uno di questi eventi critici. 

> [!NOTE] 
> Teams e SharePoint Online non supportano ancora gli eventi di rischio utente.

### <a name="conditional-access-policy-evaluation-preview"></a>Valutazione dei criteri di accesso condizionale (anteprima)

Exchange e SharePoint sono in grado di sincronizzare i criteri di accesso condizionale chiave in modo che possano essere valutati all'interno del servizio stesso.

Questo processo consente lo scenario in cui gli utenti perdono l'accesso a file aziendali, posta elettronica, calendario o attività Microsoft 365 da app client o SharePoint Online immediatamente dopo la modifica del percorso di rete.

> [!NOTE]
> Non tutte le combinazioni di app e provider di risorse sono supportate. Vedere la tabella seguente. Office fa riferimento a Word, Excel e PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Supportato | Supportato | Supportato | Supportato | Supportato |
| **Exchange Online** | Supportato | Supportato | Supportato | Supportato | Supportato |

| | App Web di Office | App Win32 di Office | Office per iOS | Office per Android | Office per Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Non supportato | Supportato | Supportato | Supportato | Supportato |
| **Exchange Online** | Non supportato | Supportato | Supportato | Supportato | Supportato |

| | OneDrive Web | OneDrive Win32 | OneDrive iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Supportato | Supportato | Supportato | Supportato | Supportato |

### <a name="client-side-claim-challenge"></a>Richiesta di attestazione lato client

Prima della valutazione dell'accesso continuo, i client provano sempre a riprodurre il token di accesso dalla cache, purché non sia scaduto. Con cae, viene introdotto un nuovo caso in cui un provider di risorse può rifiutare un token anche quando non è scaduto. Per informare i client di ignorare la cache anche se i token  memorizzati nella cache non sono scaduti, viene introdotto un meccanismo denominato richiesta di attestazione per indicare che il token è stato rifiutato e che un nuovo token di accesso deve essere emesso da Azure AD. CaE richiede un aggiornamento client per comprendere la richiesta di attestazione. La versione più recente delle applicazioni seguenti supporta la richiesta di attestazione:

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Supportato | Supportato | Supportato | Supportato | Supportato |
| **Teams** | Supportato | Supportato | Supportato | Supportato | Supportato |
| **Office** | Non supportato | Supportato | Supportato | Supportato | Supportato |
| **OneDrive** | Supportato | Supportato | Supportato | Supportato | Supportato |

### <a name="token-lifetime"></a>Durata dei token

Poiché i rischi e i criteri vengono valutati in tempo reale, i client che negoziano sessioni in grado di riconoscere la valutazione dell'accesso continuo si baseranno su CAE anziché su criteri di durata dei token di accesso statici esistenti, il che significa che i criteri di durata dei token configurabili non verranno più rispettati per i client con supporto cae che negoziano sessioni compatibili con CAE.

La durata dei token viene aumentata per durare a lungo, fino a 28 ore, nelle sessioni CAE. La revoca è basata su eventi critici e valutazione dei criteri, non solo su un periodo di tempo arbitrario. Questa modifica aumenta la stabilità delle applicazioni senza influire sulla sicurezza. 

Se non si usano client con capacità CAE, la durata del token di accesso predefinita rimarrà di 1 ora, a meno che non sia stata configurata la durata del token di accesso con la funzionalità di anteprima Durata token configurabile [(CTL).](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="example-flows"></a>Flussi di esempio

### <a name="user-revocation-event-flow"></a>Flusso di eventi di revoca utente:

![Flusso di eventi di revoca utente](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client con supporto cae presenta le credenziali o un token di aggiornamento per Azure AD un token di accesso per una risorsa.
1. Un token di accesso viene restituito insieme ad altri elementi al client.
1. Un amministratore revoca [in modo esplicito tutti i token di aggiornamento per l'utente](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Un evento di revoca verrà inviato al provider di risorse da Azure AD.
1. Un token di accesso viene presentato al provider di risorse. Il provider di risorse valuta la validità del token e verifica se è presente un evento di revoca per l'utente. Il provider di risorse usa queste informazioni per decidere se concedere o meno l'accesso alla risorsa.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di richiesta 401+ al client.
1. Il client con capacità CAE comprende la richiesta di attestazione 401+. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione Azure AD. Azure AD rivaluterà quindi tutte le condizioni e chiederà all'utente di eseguire di nuovo l'autenticazione in questo caso.

### <a name="user-condition-change-flow-preview"></a>Flusso di modifica della condizione utente (anteprima):

Nell'esempio seguente un amministratore dell'accesso condizionale ha configurato un criterio di accesso condizionale basato sulla posizione per consentire l'accesso solo da intervalli IP specifici:

![Flusso di eventi delle condizioni utente](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Un client che supporta CAE presenta le credenziali o un token di aggiornamento Azure AD richiesta di un token di accesso per una risorsa.
1. Azure AD valuta tutti i criteri di accesso condizionale per verificare se l'utente e il client soddisfano le condizioni.
1. Un token di accesso viene restituito insieme ad altri artefatti al client.
1. L'utente esce da un intervallo IP consentito
1. Il client presenta un token di accesso al provider di risorse dall'esterno di un intervallo IP consentito.
1. Il provider di risorse valuta la validità del token e controlla i criteri di posizione sincronizzati Azure AD.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di attestazione 401+ al client perché non proviene dall'intervallo IP consentito.
1. Il client che supporta CAE comprende la richiesta di attestazione 401+. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione Azure AD. Azure AD rivaluta tutte le condizioni e in questo caso negherà l'accesso.

## <a name="enable-or-disable-cae-preview"></a>Abilitare o disabilitare cae (anteprima)

1. Accedere **all'portale di Azure** come amministratore dell'accesso condizionale, amministratore della sicurezza o amministratore globale
1. Passare **a** Azure Active Directory security continuous access evaluation  >  **(Valutazione dell'accesso** continuo di  >  **sicurezza).**
1. Scegliere **Abilita anteprima.**

In questa pagina è possibile limitare facoltativamente gli utenti e i gruppi che saranno soggetti all'anteprima.

![Abilitazione dell'anteprima cae nel portale di Azure](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="supported-location-policies"></a>Criteri di posizione supportati

Per CAE, sono disponibili solo informazioni dettagliate sulle località denominate basate su IP. Non sono disponibili informazioni dettagliate su altre impostazioni relative alla posizione, ad esempio [indirizzi IP attendibili MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) o località basate su paese. Quando l'utente proviene da un INDIRIZZO IP attendibile MFA o da località attendibili che includono indirizzi IP attendibili mFA o località del paese, la CAE non verrà applicata dopo lo spostamento dell'utente in un'altra località. In questi casi, verrà emettere un token CAE di 1 ora senza controllo immediato dell'imposizione IP.

> [!IMPORTANT]
> Quando si configurano i percorsi per la valutazione dell'accesso continuo, usare solo la condizione del percorso di accesso condizionale basato su [IP](../conditional-access/location-condition.md) e configurare tutti gli indirizzi IP, inclusi **IPv4 e IPv6,** che possono essere visti dal provider di identità e dal provider di risorse. Non usare le condizioni di località del paese o la funzionalità ip attendibili disponibile nella Azure AD impostazioni del servizio di Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Configurazione dell'indirizzo IP

Il provider di identità e i provider di risorse possono visualizzare indirizzi IP diversi. Questa mancata corrispondenza può verificarsi a causa di implementazioni del proxy di rete nell'organizzazione o di configurazioni IPv4/IPv6 non corrette tra il provider di identità e il provider di risorse. Ad esempio:

- Il provider di identità visualizza un indirizzo IP dal client.
- Il provider di risorse visualizza un indirizzo IP diverso dal client dopo aver passato un proxy.
- L'indirizzo IP visualizzato dal provider di identità fa parte di un intervallo IP consentito nei criteri, ma l'indirizzo IP del provider di risorse non lo è.

Se questo scenario esiste nell'ambiente per evitare cicli infiniti, Azure AD un token CAE di un'ora e non imponi la modifica della posizione del client. Anche in questo caso, la sicurezza è migliorata rispetto ai token [](#critical-event-evaluation) tradizionali di un'ora poiché vengono ancora valutati gli altri eventi oltre agli eventi di modifica della posizione del client.

### <a name="office-and-web-account-manager-settings"></a>Impostazioni di Office e Gestione account Web

| Canale di aggiornamento di Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Canale Enterprise semestrale | Se impostato su enabled o 1, CAE non è supportato. | Se impostato su enabled o 1, CAE non è supportato. |
| Canale corrente <br> oppure <br> Canale Enterprise mensile | CaE è supportato indipendentemente dall'impostazione | CaE è supportato indipendentemente dall'impostazione |

Per una spiegazione dei canali di aggiornamento di Office, vedere [Panoramica dei canali di aggiornamento per Microsoft 365 app](/deployoffice/overview-update-channels). È consigliabile che le organizzazioni non Gestione account Web (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Ora di validità dell'appartenenza ai gruppi e dell'aggiornamento dei criteri

L'appartenenza ai gruppi e l'aggiornamento dei criteri apportati dagli amministratori potrebbero richiedere fino a un giorno per essere efficaci. È stata eseguita un'ottimizzazione per gli aggiornamenti dei criteri che riducono il ritardo a due ore. Tuttavia, non tratta ancora tutti gli scenari. 

Se si verifica un'emergenza ed è necessario che i criteri siano aggiornati o che l'appartenenza al gruppo cambi per essere applicata immediatamente a determinati utenti, è consigliabile usare questo comando di [PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) o "Revoca sessione" nella pagina del profilo utente per revocare la sessione degli utenti, in modo da assicurarsi che i criteri aggiornati verranno applicati immediatamente.

### <a name="coauthoring-in-office-apps"></a>Creazione condivisa nelle app di Office

Quando più utenti collaborano contemporaneamente allo stesso documento, l'accesso dell'utente al documento potrebbe non essere revocato immediatamente dalla CAE in base a eventi di revoca dell'utente o di modifica dei criteri. In questo caso, l'utente perde completamente l'accesso dopo la chiusura del documento, la chiusura di Word, Excel o PowerPoint o dopo un periodo di 10 ore.

Per ridurre questo tempo, un amministratore di SharePoint può facoltativamente ridurre la durata massima delle sessioni di creazione condivisa per i documenti archiviati in SharePoint Online e OneDrive for Business, configurando criteri di percorso di rete [in SharePoint Online.](/sharepoint/control-access-based-on-network-location) Dopo aver modificato questa configurazione, la durata massima delle sessioni di coautoriazione verrà ridotta a 15 minuti e potrà essere modificata ulteriormente usando il comando di PowerShell di SharePoint Online "Set-SPOTenant –IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Abilitare dopo che un utente è stato disabilitato

Se si abilita un utente subito dopo che è stato disabilitato. Ci sarà una certa latenza prima che l'account possa essere abilitato. SPO e Teams avranno un ritardo di 15 minuti. Il ritardo è di 35-40 minuti per EXO.

## <a name="faqs"></a>Domande frequenti

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Come funzionerà CAE con la frequenza di accesso?

La frequenza di accesso verrà rispettata con o senza CAE.

## <a name="next-steps"></a>Passaggi successivi

[Annuncio della valutazione dell'accesso continuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
