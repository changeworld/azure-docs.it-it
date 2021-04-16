---
title: Gestire l'accesso esterno con Azure Active Directory Entitlement Management
description: Come usare la Azure Active Directory Entitlement Management come parte del piano di sicurezza di accesso esterno complessivo.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531899"
---
# <a name="manage-external-access-with-entitlement-management"></a>Gestire l'accesso esterno con Entitlement Management 


[La gestione degli](../governance/entitlement-management-overview.md) entitlement è una funzionalità di governance delle identità che consente alle organizzazioni di gestire il ciclo di vita delle identità e degli accessi su larga scala automatizzando i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le revisioni e la scadenza. La gestione degli entitlement consente agli utenti non amministratori delegati di creare pacchetti [di](../governance/entitlement-management-overview.md) accesso a cui gli utenti esterni di altre organizzazioni possono richiedere l'accesso. È possibile configurare flussi di lavoro di approvazione in una e più fasi per valutare le richieste ed effettuare il [provisioning](../governance/what-is-provisioning.md) degli utenti per l'accesso con limiti di tempo con revisioni ricorrenti. La gestione degli entitlement consente il provisioning basato su criteri e il deprovisioning di account esterni.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Concetti chiave per l'abilitazione di Entitlement Management

I concetti chiave seguenti sono importanti da comprendere per la gestione degli entitlement.

### <a name="access-packages"></a>Accedere ai pacchetti

Un [pacchetto di accesso](../governance/entitlement-management-overview.md) è alla base della gestione dei diritti. I pacchetti di accesso sono raggruppamenti di risorse gestite da criteri che un utente deve collaborare a un progetto o eseguire altre attività. Ad esempio, un pacchetto di accesso può includere:

* accesso a siti di SharePoint specifici.

* applicazioni aziendali, incluse le app saaS e in-house personalizzate, ad esempio Salesforce.

* Canali di Microsoft Teams.

* Gruppi di Microsoft 365. 

### <a name="catalogs"></a>Cataloghi

I pacchetti di accesso si trovano [nei cataloghi](../governance/entitlement-management-catalog-create.md). Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti e delegare la possibilità di gestirle. Prima di tutto si aggiungono risorse a un catalogo e quindi si possono aggiungere tali risorse per accedere ai pacchetti. Ad esempio, è possibile creare un catalogo "Finance" e [delegarne](../governance/entitlement-management-delegate.md) la gestione a un membro del team finanziario. Tale persona può quindi [aggiungere risorse,](../governance/entitlement-management-catalog-create.md)creare pacchetti di accesso e gestire l'approvazione dell'accesso a tali pacchetti.

Il diagramma seguente illustra un ciclo di vita di governance tipico per un utente esterno che ottiene l'accesso a un pacchetto di accesso con scadenza.

![Diagramma del ciclo di governance degli utenti esterni.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Accesso esterno self-service

È possibile visualizzare i pacchetti di accesso [tramite il portale Azure AD Accesso personale per](../governance/entitlement-management-request-access.md) consentire agli utenti esterni di richiedere l'accesso. I criteri determinano chi può richiedere un pacchetto di accesso. Specificare chi può richiedere il pacchetto di accesso:

* Organizzazioni [connesse specifiche](../governance/entitlement-management-organization.md)

* Tutte le organizzazioni connesse configurate

* Tutti gli utenti di qualsiasi organizzazione

* Utenti membri o guest già presenti nel tenant

### <a name="approvals"></a>Approvazioni   
I pacchetti di accesso possono includere l'approvazione obbligatoria per l'accesso. **Implementare sempre i processi di approvazione per gli utenti esterni.** Le approvazioni possono essere un'approvazione singola o in più fasi. Le approvazioni sono determinate dai criteri. Se sia gli utenti interni che gli utenti esterni devono accedere allo stesso pacchetto, è probabile che siano stati impostati criteri di accesso diversi per diverse categorie di organizzazioni connesse e per gli utenti interni.

### <a name="expiration"></a>Scadenza  
I pacchetti di accesso possono includere una data di scadenza. La scadenza può essere impostata su un giorno specifico o concedere all'utente un numero specifico di giorni per l'accesso. Quando il pacchetto di accesso scade e l'utente non ha altri accessi, l'oggetto utente guest B2B che rappresenta l'utente può essere eliminato o bloccato dall'accesso. È consigliabile applicare la scadenza ai pacchetti di accesso per gli utenti esterni. Non tutti i pacchetti di accesso hanno scadenze. Per quelli che non lo sono, assicurarsi di eseguire le verifiche di accesso.

### <a name="access-reviews"></a>Verifiche di accesso

I pacchetti di accesso possono [richiedere](../governance/manage-guest-access-with-access-reviews.md)verifiche di accesso periodiche, che richiedono al proprietario del pacchetto o a un designee di attestare la continua necessità di accesso degli utenti. 

Prima di configurare la revisione, determinare quanto segue.

* Chi

   * Quali sono i criteri per l'accesso continuo?

   * Chi sono i revisori specificati?

* Con quale frequenza devono essere eseguite le revisioni pianificate?

   * Le opzioni predefinite includono mensile, trimestrale, bi-annuale o annuale. 

   * È consigliabile usare una frequenza trimestrale o superiore per i pacchetti che supportano l'accesso esterno. 

 

> [!IMPORTANT]
> Le verifiche di accesso dei pacchetti di accesso verificano solo l'accesso concesso tramite Entitlement Management. È quindi necessario configurare altri processi per esaminare qualsiasi accesso fornito a utenti esterni all'esterno di Entitlement Management.

Per altre informazioni sulle verifiche di accesso, vedere [Planning an Azure AD Access Reviews deployment](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Uso dell'automazione in Entitlement Management

È possibile eseguire [le funzioni di gestione](/graph/tutorial-access-package-api)entitlement usando Microsoft Graph , tra cui

* [Gestire i pacchetti di accesso](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Gestire le verifiche di accesso](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Gestire le organizzazioni connesse](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Gestire le impostazioni di Gestione entitlement](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Consigli 

È consigliabile usare le procedure per gestire l'accesso esterno con Entitlement Management.

Per i progetti con uno o più partner commerciali, creare e usare i pacchetti di accesso per eseguire **l'onboardamento [](../governance/entitlement-management-access-package-create.md)** e il provisioning degli utenti di tali partner all'accesso alle risorse . 

* Se nella directory sono già presenti utenti B2B, è anche possibile assegnarli direttamente ai pacchetti di accesso appropriati.

* È possibile assegnare [l'accesso portale di Azure](../governance/entitlement-management-access-package-assignments.md)o tramite [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true).

**Usare le impostazioni di Governance delle identità per rimuovere gli utenti dalla directory alla scadenza dei pacchetti di accesso.**

![Screenshot della configurazione della gestione del ciclo di vita degli utenti esterni.](media/secure-external-access/6-manage-external-lifecycle.png)

Queste impostazioni si applicano solo agli utenti che sono stati onboarded tramite Entitlement Management.

**[Delegare la gestione dei cataloghi e accedere ai](../governance/entitlement-management-delegate.md) pacchetti ai proprietari aziendali,** che hanno altre informazioni su chi deve accedere a .

![Screenshot della configurazione di un catalogo.](media/secure-external-access/6-catalog-management.png)

**[Applicare la scadenza dei pacchetti di accesso](../governance/entitlement-management-access-package-lifecycle-policy.md) a cui gli utenti esterni hanno accesso.**


![Screenshot della configurazione della scadenza del pacchetto di accesso.](media/secure-external-access/6-access-package-expiration.png)

* Se si conosce la data di fine di un pacchetto di accesso basato su progetto, usare On Date per impostare la data specifica. 

* In caso contrario, è consigliabile che la scadenza non sia più di 365 giorni, a meno che non sia noto come impegno di più anni.

* Consentire agli utenti di estendere l'accesso.

* Richiedere l'approvazione per concedere l'estensione.

**[Applicare le verifiche di accesso dei pacchetti](../governance/manage-guest-access-with-access-reviews.md) per evitare accessi inappropriati per i guest.**

![Screenshot della creazione di un nuovo pacchetto di accesso.](media/secure-external-access/6-new-access-package.png)

* Applicare revisioni trimestrali.

* Per i progetti sensibili alla conformità, impostare i revisori in modo che siano revisori specifici, anziché auto-revisione per gli utenti esterni. Gli utenti che accedono ai gestori di pacchetti sono un buon punto di partenza per i revisori. 

* Per i progetti meno sensibili, l'auto-revisione degli utenti ridurrà l'onere per l'organizzazione di rimuovere l'accesso dagli utenti che non sono più con l'organizzazione principale.

Per altre informazioni, vedere [Govern access for external users in Azure AD Entitlement Management](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. È consigliabile eseguire le azioni nell'ordine elencato.

1. [Determinare la posizione di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individuare lo stato corrente](2-secure-access-current-state.md)

3. [Creare un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Proteggere l'accesso con Entitlement Management](6-secure-access-entitlement-managment.md) (l'utente è qui).

7. [Proteggere l'accesso con i criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Proteggere l'accesso a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)

 

