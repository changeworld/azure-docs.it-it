---
title: Durate dei token configurabili
titleSuffix: Microsoft identity platform
description: Informazioni su come impostare la durata per i token di accesso, SAML e ID rilasciati da Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363972"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Durate dei token configurabili in Microsoft Identity Platform (anteprima)

È possibile specificare la durata di un token di accesso, ID o SAML rilasciato da Microsoft Identity Platform. La durata dei token può essere impostata per tutte le app di un'organizzazione, per un'applicazione multi-tenant (più organizzazioni) o per un'entità servizio specifica in un'organizzazione. Tuttavia, attualmente non è possibile configurare la durata dei token per le entità servizio [dell'identità gestita.](../managed-identities-azure-resources/overview.md)

In Azure AD, un oggetto criteri rappresenta un set di regole applicate a singole applicazioni o a tutte le applicazioni di un'organizzazione. Ogni tipo di criteri ha una struttura univoca con un set di proprietà che vengono applicate agli oggetti a cui sono assegnate.

È possibile designare un oggetto criteri come predefinito per l'organizzazione. I criteri predefiniti vengono applicati a tutte le applicazioni all'interno dell'organizzazione, fino a quando non vengono sostituiti da criteri con priorità più alta. È anche possibile assegnare criteri ad applicazioni specifiche. L'ordine di priorità varia in base al tipo di criteri.

Per esempi, vedere [esempi di come configurare la durata dei token.](configure-token-lifetimes.md)

> [!NOTE]
> I criteri di durata dei token configurabili si applicano solo ai client desktop e per dispositivi mobili che accedono a SharePoint Online e alle risorse OneDrive for Business e non si applicano alle sessioni del Web browser.
> Per gestire la durata delle sessioni del Web browser per SharePoint Online e OneDrive for Business, usare la funzionalità [Durata sessione di accesso](../conditional-access/howto-conditional-access-session-lifetime.md) condizionale. Fare riferimento ai [blog di SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) per altre informazioni sulla configurazione dei timeout di sessione inattiva.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare questa funzionalità è necessario disporre di una licenza di Azure AD Premium P1. Per trovare la licenza più valida per i propri requisiti, vedere Confronto tra le funzionalità disponibili a livello [generale delle edizioni Gratuito e Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Anche i clienti con [licenze di Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) possono accedere alle funzionalità di accesso condizionale.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Criteri di durata dei token per i token di accesso, SAML e ID

È possibile impostare criteri di durata dei token per i token di accesso, i token SAML e i token ID.

### <a name="access-tokens"></a>Token di accesso

I client usano i token di accesso per accedere a una risorsa protetta. I token di accesso possono essere usati solo per una combinazione specifica di utente, client e risorsa. Non è possibile revocare i token di accesso, che rimangono validi fino alla scadenza. Un attore malintenzionato può usare un eventuale token di accesso ottenuto per la sua intera durata. Regolare la durata dei token di accesso significa trovare un compromesso tra il miglioramento delle prestazioni di sistema e l'aumento dell'intervallo di tempo durante il quale il client conserva l'accesso dopo che l'account dell'utente è stato disabilitato. Il miglioramento delle prestazioni di sistema si ottiene riducendo il numero delle volte in cui un client deve acquisire un token di accesso aggiornato.  Il valore predefinito varia a seconda dell'applicazione client che richiede il token. Ad esempio, i client che supportano la valutazione dell'accesso continuo (CAE) che negoziano sessioni compatibili con CAE avranno una durata dei token di lunga durata (fino a 28 ore). Dopo la scadenza del token, il client deve usare il token di aggiornamento per acquisire (in genere automaticamente) un nuovo token di aggiornamento e un nuovo token di accesso.

### <a name="saml-tokens"></a>Token SAML

I token SAML vengono usati da molte applicazioni SaaS basate sul Web e vengono ottenuti usando Azure Active Directory endpoint del protocollo SAML2 del servizio. Vengono utilizzate anche dalle applicazioni che usano WS-Federation. La durata predefinita del token è 1 ora. Dal punto di vista di un'applicazione, il periodo di validità del token viene specificato dal valore NotOnOrAfter `<conditions …>` dell'elemento nel token. Al termine del periodo di validità del token, il client deve avviare una nuova richiesta di autenticazione, che verrà spesso soddisfatta senza accesso interattivo come risultato del token di sessione Single Sign-On (SSO).

Il valore di NotOnOrAfter può essere modificato usando il `AccessTokenLifetime` parametro in un oggetto `TokenLifetimePolicy` . Verrà impostata sulla durata configurata nei criteri, se presente, più un fattore di avallamento dell'orologio di cinque minuti.

La conferma dell'oggetto NotOnOrAfter specificata `<SubjectConfirmationData>` nell'elemento non è interessata dalla configurazione della durata del token. 

### <a name="id-tokens"></a>Token ID

I token ID vengono passati a siti Web e client nativi e contengono informazioni relative al profilo di un utente. Ogni token ID è associato a una combinazione specifica di utente e client ed è considerato valido fino alla relativa scadenza. La durata della sessione di un utente in un'applicazione Web corrisponde in genere alla durata del token ID rilasciato per l'utente. È possibile modificare la durata di un token ID per controllare la frequenza con cui l'applicazione Web scade la sessione dell'applicazione e la frequenza con cui richiede che l'utente sia nuovamente autenticato con Microsoft Identity Platform (in modo invisibile all'utente o in modo interattivo).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Criteri di durata dei token per i token di aggiornamento e i token di sessione

Non è possibile impostare criteri di durata dei token per i token di aggiornamento e i token di sessione.

> [!IMPORTANT]
> A partire dal 30 gennaio 2021 non è possibile configurare la durata dei token di aggiornamento e di sessione. Azure Active Directory rispetta più la configurazione del token di aggiornamento e di sessione nei criteri esistenti.  I nuovi token rilasciati dopo la scadenza dei token esistenti vengono ora impostati sulla [configurazione predefinita](#configurable-token-lifetime-properties). È comunque possibile configurare la durata dei token di accesso, SAML e ID dopo il ritiro della configurazione del token di aggiornamento e di sessione.
>
> La durata del token esistente non verrà modificata. Dopo la scadenza, verrà emesso un nuovo token in base al valore predefinito.
>
> Se è necessario continuare a definire il periodo di tempo prima che a un utente venga richiesto di accedere di nuovo, configurare la frequenza di accesso in Accesso condizionale. Per altre informazioni sull'accesso condizionale, vedere [Configurare la gestione della sessione di autenticazione con l'accesso condizionale.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>Proprietà configurabili per la durata dei token
I criteri per la durata dei token rappresentano un tipo di oggetto criteri contenente le regole di durata dei token. Questo criterio controlla per quanto tempo i token di accesso, SAML e ID per questa risorsa sono considerati validi. I criteri di durata dei token non possono essere impostati per i token di aggiornamento e di sessione. Se non si impostano criteri, il valore di durata predefinito viene applicato dal sistema.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Proprietà dei criteri di accesso, ID e durata dei token SAML2

Riducendo la proprietà Durata dei token di accesso è possibile ridurre il rischio che un token di accesso o un token ID vengano usati da un attore malintenzionato per un lungo periodo di tempo. Questi token non possono essere revocati. Il compromesso è che le prestazioni sono influenzate negativamente, perché i token devono essere sostituiti più spesso.

Per un esempio, vedere [Creare un criterio per l'accesso Web.](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)

La configurazione dei token di accesso, ID e SAML2 è interessata dalle proprietà seguenti e dai rispettivi valori impostati:

- **Proprietà**: Durata del token di accesso
- **Stringa della proprietà dei** criteri: AccessTokenLifetime
- **Influisce** su : token di accesso, token ID, token SAML2
- **Predefinito**:
    - Token di accesso: varia a seconda dell'applicazione client che richiede il token. Ad esempio, i client che supportano la valutazione dell'accesso continuo (CAE) che negoziano sessioni compatibili con CAE avranno una durata dei token di lunga durata (fino a 28 ore).
    - Token ID, token SAML2: 1 ora
- **Minimo:** 10 minuti
- **Massimo:** 1 giorno

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Proprietà dei criteri di durata dei token di aggiornamento e di sessione

La configurazione dei token di aggiornamento e di sessione è interessata dalle proprietà seguenti e dai rispettivi valori impostati. Dopo il ritiro della configurazione del token di aggiornamento e di sessione il 30 gennaio 2021, Azure AD verranno rispettati solo i valori predefiniti descritti di seguito. Se si decide [](../conditional-access/howto-conditional-access-session-lifetime.md) di non usare l'accesso condizionale per gestire la frequenza di accesso, i token di aggiornamento e di sessione verranno impostati sulla configurazione predefinita in tale data e non sarà più possibile modificarne la durata.  

|Proprietà   |Stringa proprietà criteri    |Impatto |Predefinito |
|----------|-----------|------------|------------|
|Tempo inattività massimo token di aggiornamento |MaxInactiveTime  |Token di aggiornamento |90 giorni  |
|Validità massima token di aggiornamento a fattore singolo  |MaxAgeSingleFactor  |Token di aggiornamento (per tutti gli utenti)  |Fino a revoca  |
|Validità massima token di aggiornamento a più fattori  |MaxAgeMultiFactor  |Token di aggiornamento (per tutti gli utenti) |Fino a revoca  |
|Validità massima token di sessione a fattore singolo  |MaxAgeSessionSingleFactor |Token di sessione (permanenti e non permanenti)  |Fino a revoca |
|Validità massima token di sessione a più fattori  |MaxAgeSessionMultiFactor  |Token di sessione (permanenti e non permanenti)  |Fino a revoca |

È possibile usare PowerShell per trovare i criteri che saranno interessati dal ritiro.  Usare i [cmdlet di PowerShell](configure-token-lifetimes.md#get-started) per visualizzare tutti i criteri creati nell'organizzazione o per trovare le app e le entità servizio collegate a un criterio specifico.

## <a name="policy-evaluation-and-prioritization"></a>Definizione della priorità e valutazione dei criteri
È possibile creare e quindi assegnare criteri per la durata dei token a un'applicazione specifica, all'organizzazione e alle entità servizio. A un'applicazione specifica si possono applicare più criteri. Di seguito sono riportate le regole su cui si basano i criteri per la durata dei token validi:

* Se i criteri sono assegnati in modo esplicito all'entità servizio, vengono applicati.
* Se all'entità servizio non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati in modo esplicito all'elemento organizzazione padre dell'entità servizio.
* Se all'entità servizio o all'organizzazione non sono assegnati criteri in modo esplicito, vengono applicati i criteri assegnati all'applicazione.
* Se all'entità servizio, all'organizzazione o all'oggetto applicazione non è stato assegnato alcun criterio, vengono applicati i valori predefiniti. Vedere in proposito la tabella in [Proprietà configurabili per la durata dei token](#configurable-token-lifetime-properties).

Per altre informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](app-objects-and-service-principals.md).

La validità di un token viene valutata quando questo viene usato. Vengono applicati i criteri con la priorità più alta per l'applicazione a cui si accede.

Tutti gli intervalli di tempo usati qui sono formattati in base all'oggetto[TimeSpan](/dotnet/api/system.timespan) C# - D.HH:MM:SS.  Quindi 80 giorni e 30 minuti sarebbe `80.00:30:00`.  La D iniziale può essere eliminata se è zero, pertanto 90 minuti sarebbe `00:90:00`.  

## <a name="cmdlet-reference"></a>Informazioni di riferimento sui cmdlet

Questi sono i cmdlet nel modulo Azure Active Directory [PowerShell per Graph Preview.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)

### <a name="manage-policies"></a>Gestire i criteri

È possibile usare i cmdlet riportati di seguito per gestire i criteri.

| Cmdlet | Descrizione | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Crea nuovi criteri. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene tutti i criteri di Azure AD o i criteri specificati. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Ottiene tutte le app e le entità servizio collegate a criteri specifici. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aggiorna i criteri esistenti. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Elimina i criteri specificati. |

### <a name="application-policies"></a>Criteri dell'applicazione
È possibile usare i cmdlet riportati di seguito per i criteri dell'applicazione.</br></br>

| Cmdlet | Descrizione | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Collega i criteri specificati a un'applicazione. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene i criteri assegnati a un'applicazione. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Rimuove i criteri da un'applicazione. |

### <a name="service-principal-policies"></a>Criteri dell'entità servizio
È possibile usare i cmdlet riportati di seguito per i criteri dell'entità servizio.

| Cmdlet | Descrizione | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Collega i criteri specificati a un'entità servizio. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Ottiene i criteri collegati all'entità servizio specificata.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Rimuove i criteri dall'entità servizio specificata.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [esempi di come configurare la durata dei token.](configure-token-lifetimes.md)
