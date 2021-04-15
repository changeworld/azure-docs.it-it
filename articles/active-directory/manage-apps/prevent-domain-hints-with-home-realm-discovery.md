---
title: Impedire l'accelerazione automatica dell'accesso in Azure AD criteri di individuazione dell'area di autenticazione principale
description: Informazioni su come impedire domain_hint'accelerazione automatica agli IDP federati.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375551"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Disabilitare l'accelerazione automatica per un provider di identità federato durante l'accesso utente con i criteri di individuazione dell'area di autenticazione principale

[I criteri di individuazione dell'area di](/graph/api/resources/homeRealmDiscoveryPolicy) autenticazione principale (HRD) offrono agli amministratori diversi modi per controllare come e dove gli utenti eseguono l'autenticazione. La sezione dei criteri HRD viene usata per facilitare la migrazione di utenti federati a credenziali gestite dal cloud come FIDO, assicurandosi che visitino sempre la pagina di accesso di Azure AD e non siano accelerati automaticamente a un provider di identità federato a causa di hint di `domainHintPolicy` dominio. [](../authentication/howto-authentication-passwordless-security-key.md)

Questo criterio è necessario nelle situazioni in cui un amministratore non può controllare o aggiornare i suggerimenti per l'aggiunta di domini durante l'accesso.  Ad esempio, invia l'utente a una pagina di accesso con il parametro aggiunto, per accelerare automaticamente l'utente direttamente `outlook.com/contoso.com` `&domain_hint=contoso.com` all'IDP federato per il `contoso.com` dominio. Gli utenti con credenziali gestite inviate a un provider di identità federato non possono accedere usando le credenziali gestite, riducendo la sicurezza e frustrando gli utenti con esperienze di accesso casuali. Gli amministratori che esercitono le credenziali gestite devono anche configurare [questo criterio](#suggested-use-within-a-tenant) per garantire che gli utenti possano sempre usare le credenziali gestite.

## <a name="domainhintpolicy-details"></a>Dettagli di DomainHintPolicy

La sezione DomainHintPolicy dei criteri HRD è un oggetto JSON che consente a un amministratore di rifiutare esplicitamente determinati domini e applicazioni dall'utilizzo di hint di dominio.  Dal punto di vista funzionale, Azure AD pagina di accesso si comporta come se non fosse presente un parametro nella `domain_hint` richiesta di accesso.

### <a name="the-respect-and-ignore-policy-sections"></a>Sezioni Rispetta e ignora criteri

|Sezione | Significato | Valori |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Se questo hint di dominio viene inviato nella richiesta, ignorarlo. |Matrice di indirizzi di dominio (ad esempio `contoso.com` ). Supporta anche `all_domains`|
|`RespectDomainHintForDomains`| Se questo hint di dominio viene inviato nella richiesta, rispettarlo anche se indica che l'app nella richiesta non deve `IgnoreDomainHintForApps` accelerare automaticamente. Viene usato per rallentare l'implementazione di hint di dominio deprecati all'interno della rete. È possibile indicare che alcuni domini devono essere ancora accelerati. | Matrice di indirizzi di dominio (ad esempio `contoso.com` ). Supporta anche `all_domains`|
|`IgnoreDomainHintForApps`| Se una richiesta da questa applicazione include un hint di dominio, ignorarla. | Matrice di ID applicazione (GUID). Supporta anche `all_apps`|
|`RespectDomainHintForApps` |Se una richiesta da questa applicazione include un hint di dominio, rispettarla anche se `IgnoreDomainHintForDomains` include tale dominio. Usato per garantire che alcune app continuino a funzionare se si rilevano interruzioni senza hint di dominio. | Matrice di ID applicazione (GUID). Supporta anche `all_apps`|

### <a name="policy-evaluation"></a>Valutazione dei criteri

La logica DomainHintPolicy viene eseguita su ogni richiesta in ingresso che contiene un hint di dominio e accelera in base a due parti di dati nella richiesta: il dominio nell'hint di dominio e l'ID client (l'app). In breve: "Rispetto" per un dominio o un'app ha la precedenza su un'istruzione per "Ignorare" un hint di dominio per un dominio o un'applicazione specifica.

1. In assenza di criteri di hint di dominio o se nessuna delle 4 sezioni fa riferimento all'app o all'hint di dominio indicato, il resto dei criteri [HRD verrà valutato](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Se una o entrambe le sezioni o includono l'hint per l'app o il dominio nella richiesta, l'utente verrà accelerato automaticamente al provider di identità federato come `RespectDomainHintForApps` `RespectDomainHintForDomains` richiesto.
1. Se una (o entrambe) di o fa riferimento all'app o all'hint di dominio nella richiesta e non vi fa riferimento nelle sezioni "Rispetto", la richiesta non verrà accelerata automaticamente e l'utente rimarrà nella pagina di accesso di Azure AD per fornire un nome `IgnoreDomainHintsForApps` `IgnoreDomainHintsForDomains` utente.

Dopo aver immesso un nome utente nella pagina di accesso, un utente può usare le proprie credenziali gestite, se registrate.  Se scelgono di non usare una credenziale gestita o non ne hanno registrate, verranno usate come di consueto nel proprio provider di identità federato per l'immissione delle credenziali.

## <a name="suggested-use-within-a-tenant"></a>Uso consigliato all'interno di un tenant

Gli amministratori dei domini federati devono configurare questa sezione dei criteri HRD in un piano in quattro fasi. L'obiettivo di questo piano è quello di consentire a tutti gli utenti di un tenant di usare le credenziali gestite indipendentemente dal dominio o dall'applicazione, salvare le app con dipendenze rigide `domain_hint` dall'utilizzo.  Questo piano consente agli amministratori di trovare tali app, esentarle dai nuovi criteri e continuare a eseguire l'implementazione della modifica nel resto del tenant.

1. Selezionare un dominio in cui implementare inizialmente questa modifica.  Questo sarà il dominio di test, quindi selezionarne uno che possa essere più ricettivo alle modifiche nell'esperienza utente( ad esempio, visualizzare una pagina di accesso diversa).  Verranno ignorati tutti gli hint di dominio di tutte le applicazioni che usano questo nome di dominio. [Impostare](#configuring-policy-through-graph-explorer) questo criterio nei criteri HRD predefiniti del tenant:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Raccogliere commenti e suggerimenti dagli utenti del dominio di test. Raccogliere i dettagli per le applicazioni che si sono interrotto in seguito a questa modifica: hanno una dipendenza dall'utilizzo di hint di dominio e devono essere aggiornati. Per il momento, aggiungerli alla `RespectDomainHintForApps` sezione :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Continuare a espandere l'implementazione dei criteri nei nuovi domini, raccogliendo altri commenti e suggerimenti.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Completare l'implementazione: scegliere come destinazione tutti i domini, esentando quelli che devono continuare ad essere accelerati:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Dopo aver completato il passaggio 4, tutti gli utenti, ad eccezione di quelli in , possono accedere alla pagina di accesso di Azure AD anche quando gli hint di dominio causerebbero altrimenti un'accelerazione automatica a un provider di identità `guestHandlingDomain.com` federato.  L'eccezione è se l'app che richiede l'accesso è una di quelle esentate. Per tali app, tutti i suggerimenti di dominio verranno comunque accettati.

## <a name="configuring-policy-through-graph-explorer"></a>Configurazione dei criteri tramite Graph Explorer

Impostare i [criteri HRD](/graph/api/resources/homeRealmDiscoveryPolicy) come di consueto, usando Microsoft Graph.  

1. Concedere l'autorizzazione Policy.ReadWrite.ApplicationConfiguration in [Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer)  
1. Usare l'URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Pubblicare i nuovi criteri in questo URL o applicare PATCH se `/policies/homerealmdiscoveryPolicies/{policyID}` ne viene sovrascritto uno esistente.

Contenuto POST o PATCH:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Assicurarsi di usare le barre per eseguire l'escape della `Definition` sezione JSON quando si usa Graph.  

`isOrganizationDefault` deve essere true, ma displayName e la definizione possono cambiare.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare l'accesso con chiave di sicurezza senza password](../authentication/howto-authentication-passwordless-security-key.md)
* [Abilitare l'accesso senza password con l Microsoft Authenticator app](../authentication/howto-authentication-passwordless-phone.md)