---
title: Impedisci l'accelerazione automatica dell'accesso in Azure AD usando i criteri di individuazione dell'area di autenticazione principale
description: Informazioni su come impedire l'accelerazione automatica domain_hint agli IDP federati.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589379"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Disabilitare l'accelerazione automatica a un IDP federato durante l'accesso utente con i criteri di individuazione dell'area di autenticazione principale

[Criteri di individuazione dell'area di autenticazione principale](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) offre agli amministratori diversi modi per controllare come e quando gli utenti eseguono l'autenticazione. La `domainHintPolicy` sezione del criterio HRD viene usata per aiutare a migrare gli utenti federati a credenziali gestite dal cloud, ad esempio [fido](../authentication/howto-authentication-passwordless-security-key.md), assicurandosi che visitino sempre la pagina di accesso Azure ad e non vengano accelerate automaticamente in un IDP federato a causa di hint di dominio.

Questo criterio è necessario nelle situazioni in cui un amministratore non è in grado di controllare o aggiornare gli hint di aggiunta del dominio durante l'accesso.  Ad esempio, `outlook.com/contoso.com` Invia l'utente a una pagina di accesso con il `&domain_hint=contoso.com` parametro aggiunto, per accelerare automaticamente l'utente all'IDP federato per il `contoso.com` dominio. Gli utenti con credenziali gestite inviate a un IDP federato non possono accedere usando le credenziali gestite, riducendo la sicurezza e frustrando gli utenti con esperienze di accesso casuale. Gli amministratori che implementano le credenziali gestite [devono anche configurare questi criteri](#suggested-use-within-a-tenant) per assicurarsi che gli utenti possano usare sempre le credenziali gestite.

## <a name="domainhintpolicy-details"></a>Dettagli DomainHintPolicy

La sezione DomainHintPolicy del criterio HRD è un oggetto JSON, che consente a un amministratore di rifiutare esplicitamente determinati domini e applicazioni dall'utilizzo degli hint di dominio.  Dal punto di vista funzionale, questo indica al Azure AD pagina di accesso di comportarsi come se `domain_hint` non fosse presente un parametro della richiesta di accesso.

### <a name="the-respect-and-ignore-policy-sections"></a>Sezioni relative ai criteri respect e ignore

|Sezione | Significato | Valori |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Se questo hint di dominio viene inviato nella richiesta, ignorarlo. |Matrice di indirizzi di dominio (ad esempio `contoso.com` ). Supporta anche `all_domains`|
|`RespectDomainHintForDomains`| Se questo hint di dominio viene inviato nella richiesta, rispettarlo anche se `IgnoreDomainHintForApps` indica che l'app nella richiesta non dovrebbe accelerare automaticamente. Questa operazione viene usata per rallentare l'implementazione degli hint di dominio deprecati all'interno della rete. è possibile indicare che alcuni domini dovrebbero comunque essere accelerati. | Matrice di indirizzi di dominio (ad esempio `contoso.com` ). Supporta anche `all_domains`|
|`IgnoreDomainHintForApps`| Se una richiesta proveniente da questa applicazione viene fornita con un hint di dominio, ignorarla. | Matrice di ID applicazione (GUID). Supporta anche `all_apps`|
|`RespectDomainHintForApps` |Se una richiesta proveniente da questa applicazione viene fornita con un hint di dominio, rispettarla anche se `IgnoreDomainHintForDomains` include tale dominio. Usato per assicurarsi che alcune app continuino a funzionare se si individuano interruzioni senza hint di dominio. | Matrice di ID applicazione (GUID). Supporta anche `all_apps`|

### <a name="policy-evaluation"></a>Valutazione dei criteri

La logica DomainHintPolicy viene eseguita in ogni richiesta in ingresso che contiene un hint di dominio e accelera in base a due tipi di dati nella richiesta, ovvero il dominio nell'hint di dominio e l'ID client (l'app). In breve, il "rispetto" di un dominio o di un'app ha la precedenza su un'istruzione per "ignorare" un hint di dominio per un dominio o un'applicazione specifica.

1. In assenza di criteri di hint di dominio o se nessuna delle 4 sezioni fa riferimento all'hint dell'app o del dominio indicato, [il resto del criterio HRD verrà valutato](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Se una o entrambe `RespectDomainHintForApps` `RespectDomainHintForDomains` le sezioni di o includono l'hint per l'app o il dominio nella richiesta, l'utente verrà automaticamente accelerato all'IDP federato come richiesto.
1. Se uno o entrambi `IgnoreDomainHintsForApps` i valori o `IgnoreDomainHintsForDomains` fanno riferimento all'app o all'hint di dominio nella richiesta e non vi fanno riferimento le sezioni "Respect", la richiesta non verrà accelerata automaticamente e l'utente rimarrà nella pagina di accesso Azure ad per fornire un nome utente.

Dopo che un utente ha immesso un nome utente nella pagina di accesso, può usare le credenziali gestite, se sono state registrate.  Se scelgono di non usare una credenziale gestita o se non hanno alcuna registrazione, verranno riportate al provider IDP federato per l'immissione delle credenziali come di consueto.

## <a name="suggested-use-within-a-tenant"></a>Uso consigliato in un tenant

Gli amministratori dei domini federati devono impostare questa sezione del criterio HRD in un piano a quattro fasi. L'obiettivo di questo piano è quello di fare in modo che tutti gli utenti di un tenant abbiano la possibilità di usare le credenziali gestite indipendentemente dal dominio o dall'applicazione, salvare le app che hanno dipendenze rigide dall' `domain_hint` utilizzo.  Questo piano consente agli amministratori di trovare le app, esentarle dal nuovo criterio e continuare a implementare la modifica al resto del tenant.

1. Selezionare un dominio in cui eseguire inizialmente il rollback di questa modifica.  Si tratta del dominio di test, quindi selezionare uno che potrebbe essere più ricettivo alle modifiche nell'esperienza utente (ad esempio, la visualizzazione di una pagina di accesso diversa).  Verranno ignorati tutti gli hint di dominio di tutte le applicazioni che utilizzano questo nome di dominio. [Impostare](#configuring-policy-through-graph-explorer) questo criterio nel criterio HRD predefinito del tenant:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Raccogliere commenti e suggerimenti dagli utenti del dominio di test. Raccogliere i dettagli per le applicazioni che sono state interrotta in seguito a questa modifica, che hanno una dipendenza dall'utilizzo di hint di dominio e devono essere aggiornate. Per il momento, aggiungerli alla `RespectDomainHintForApps` sezione:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Continua a espandere l'implementazione dei criteri nei nuovi domini, raccogliendo ulteriori commenti e suggerimenti.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Completare l'implementazione: specificare come destinazione tutti i domini, esentando quelli che devono continuare a essere accelerati:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Al termine del passaggio 4, tutti gli utenti, ad eccezione di quelli in `guestHandlingDomain.com` , possono accedere alla pagina di accesso Azure ad anche quando gli hint di dominio potrebbero altrimenti causare un'accelerazione automatica a un IDP federato.  L'eccezione è rappresentata dal caso in cui l'app che richiede l'accesso sia una delle esenzioni. per tali app verranno comunque accettati tutti gli hint di dominio.

## <a name="configuring-policy-through-graph-explorer"></a>Configurazione dei criteri tramite Graph Explorer

Impostare i [criteri HRD](/graph/api/resources/homeRealmDiscoveryPolicy) come di consueto, usando Microsoft Graph.  

1. Concedere l'autorizzazione Policy. ReadWrite. ApplicationConfiguration in [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. Usare l'URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Inserire il nuovo criterio in questo URL oppure `/policies/homerealmdiscoveryPolicies/{policyID}` applicarvi una patch se sovrascriverne uno esistente.

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

`isOrganizationDefault` deve essere true, ma il displayName e la definizione possono cambiare.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare l'accesso con chiave di sicurezza senza password](../authentication/howto-authentication-passwordless-security-key.md)
* [Abilitare l'accesso senza password con l'app Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)