---
title: Gestire l'accesso esterno con Azure Active Directory gestione dei diritti
description: Come usare la gestione dei diritti Azure Active Directory come parte del piano di sicurezza dell'accesso esterno globale.
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
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725179"
---
# <a name="manage-external-access-with-entitlement-management"></a>Gestire l'accesso esterno con la gestione dei diritti 


La [gestione dei diritti](../governance/entitlement-management-overview.md) è una funzionalità di governance delle identità che consente alle organizzazioni di gestire il ciclo di vita delle identità e dell'accesso su larga scala automatizzando flussi di lavoro delle richieste di accesso, assegnazioni di accesso, revisioni La gestione dei diritti consente agli utenti non amministratori delegati di creare [pacchetti di accesso](../governance/entitlement-management-overview.md) a cui gli utenti esterni di altre organizzazioni possono richiedere l'accesso. È possibile configurare i flussi di lavoro di approvazione di uno e più fasi per valutare le richieste ed effettuare il [provisioning](../governance/what-is-provisioning.md) degli utenti per un accesso limitato al tempo con revisioni ricorrenti. La gestione dei diritti consente il provisioning e il deprovisioning basati su criteri degli account esterni.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Concetti chiave per l'abilitazione della gestione dei diritti

I concetti chiave seguenti sono importanti per comprendere la gestione dei diritti.

### <a name="access-packages"></a>Accedere ai pacchetti

Un [pacchetto di accesso](../governance/entitlement-management-overview.md) è la base della gestione dei diritti. I pacchetti di accesso sono raggruppamenti di risorse gestite da criteri che un utente deve collaborare a un progetto o eseguire altre attività. Ad esempio, un pacchetto di accesso può includere:

* accesso a siti SharePoint specifici.

* applicazioni aziendali, incluse le app SaaS e interne personalizzate come Salesforce.

* Canali Microsoft teams.

* Gruppi di Microsoft 365. 

### <a name="catalogs"></a>Cataloghi

I pacchetti di accesso si trovano in [cataloghi](../governance/entitlement-management-catalog-create.md). Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti e delegare la possibilità di gestirli. Prima di tutto si aggiungono risorse a un catalogo e quindi si possono aggiungere tali risorse per accedere ai pacchetti. Ad esempio, potrebbe essere necessario creare un catalogo "Finance" e [delegare la gestione](../governance/entitlement-management-delegate.md) a un membro del team Finance. Tale utente può quindi [aggiungere risorse](../governance/entitlement-management-catalog-create.md), creare pacchetti di accesso e gestire l'approvazione dell'accesso a questi pacchetti.

Il diagramma seguente mostra un ciclo di vita di governance tipico per un utente esterno che ottiene l'accesso a un pacchetto di accesso con scadenza.

![Diagramma del ciclo di governance dell'utente esterno.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Accesso esterno self-service

È possibile far emergere i pacchetti di accesso tramite il [Azure ad portale di accesso personale](../governance/entitlement-management-request-access.md) per consentire agli utenti esterni di richiedere l'accesso. I criteri determinano chi può richiedere un pacchetto di accesso. Specificare gli utenti autorizzati a richiedere il pacchetto di accesso:

* [Organizzazioni connesse](../governance/entitlement-management-organization.md) specifiche

* Tutte le organizzazioni connesse configurate

* Tutti gli utenti di qualsiasi organizzazione

* Utenti guest o membro già presenti nel tenant

### <a name="approvals"></a>Approvazioni   
I pacchetti Access possono includere l'approvazione obbligatoria per l'accesso. **Implementare sempre processi di approvazione per utenti esterni**. Le approvazioni possono essere un'approvazione a una o più fasi. Le approvazioni sono determinate dai criteri. Se è necessario che gli utenti interni ed esterni debbano accedere allo stesso pacchetto, è probabile che si configurano criteri di accesso diversi per le diverse categorie di organizzazioni connesse e per gli utenti interni.

### <a name="expiration"></a>Scadenza  
I pacchetti Access possono includere una data di scadenza. La scadenza può essere impostata su un giorno specifico o fornire all'utente un numero specifico di giorni per l'accesso. Quando il pacchetto di accesso scade e l'utente non dispone di altri accessi, l'oggetto utente Guest B2B che rappresenta l'utente può essere eliminato o bloccato. Si consiglia di applicare la scadenza nei pacchetti di accesso per gli utenti esterni. Non tutti i pacchetti di Access hanno scadenze. Per gli utenti che non sono in grado di eseguire verifiche di accesso.

### <a name="access-reviews"></a>Verifiche di accesso

I pacchetti Access possono richiedere verifiche di [accesso](../governance/manage-guest-access-with-access-reviews.md)periodiche, che richiedono il proprietario del pacchetto o un designato per attestare la necessità di accesso degli utenti continua. 

Prima di configurare la revisione, determinare quanto segue.

* Chi

   * Quali sono i criteri per l'accesso continuo?

   * Quali sono i revisori specificati?

* Con quale frequenza vengono eseguite le verifiche pianificate?

   * Le opzioni predefinite includono mensile, trimestrale, semestrale o annuale. 

   * Per i pacchetti che supportano l'accesso esterno è consigliabile usare una frequenza trimestrale o superiore. 

 

> [!IMPORTANT]
> Le verifiche di accesso dei pacchetti Access controllano solo l'accesso concesso tramite la gestione dei diritti. È quindi necessario configurare altri processi per esaminare gli accessi forniti agli utenti esterni al di fuori della gestione dei diritti.

Per ulteriori informazioni sulle verifiche di accesso, vedere [Planning a Azure ad Access revisioni Deployment](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Uso dell'automazione nella gestione dei diritti

È possibile eseguire le [funzioni di gestione dei diritti utilizzando Microsoft Graph](/graph/tutorial-access-package-api), tra cui

* [Gestire i pacchetti di accesso](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Gestire le verifiche di accesso](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Gestire le organizzazioni connesse](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Gestire le impostazioni di gestione dei diritti](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Consigli 

È consigliabile usare le procedure per gestire l'accesso esterno con la gestione dei diritti.

**Per i progetti con uno o più partner commerciali, [creare e usare i pacchetti di accesso](../governance/entitlement-management-access-package-create.md) per eseguire il caricamento e il provisioning degli utenti del partner per l'accesso alle risorse**. 

* Se nella directory sono già presenti utenti B2B, è anche possibile assegnarli direttamente ai pacchetti di accesso appropriati.

* È possibile assegnare l'accesso all' [portale di Azure](../governance/entitlement-management-access-package-assignments.md)o tramite [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Usare le impostazioni di governance delle identità per rimuovere gli utenti dalla directory quando i pacchetti di accesso scadono**.

![Screenshot della configurazione della gestione del ciclo di vita degli utenti esterni.](media/secure-external-access/6-manage-external-lifecycle.png)

Queste impostazioni si applicano solo agli utenti che sono stati caricati tramite la gestione dei diritti.

**[Delegare la gestione dei cataloghi e accedere](../governance/entitlement-management-delegate.md) ai proprietari aziendali, che dispongono di maggiori informazioni sugli utenti che accedono a**.

![Screenshot della configurazione di un catalogo.](media/secure-external-access/6-catalog-management.png)

**[Applicare la scadenza dei pacchetti di accesso](../governance/entitlement-management-access-package-lifecycle-policy.md) a cui gli utenti esterni possono accedere.**


![Screenshot della configurazione della scadenza del pacchetto di accesso.](media/secure-external-access/6-access-package-expiration.png)

* Se si conosce la data di fine di un pacchetto di accesso basato su progetto, usare la data di inizio per impostare la data specifica. 

* In caso contrario, è consigliabile che la scadenza non sia più 365 giorni, a meno che non si tratti di un impegno multianno.

* Consente agli utenti di estendere l'accesso.

* Richiedere l'approvazione per concedere l'estensione.

**[Applicare le verifiche di accesso dei pacchetti](../governance/manage-guest-access-with-access-reviews.md) per evitare l'accesso non appropriato per gli utenti guest.**

![Screenshot della creazione di un nuovo pacchetto di accesso.](media/secure-external-access/6-new-access-package.png)

* Imponi recensioni trimestrali.

* Per i progetti sensibili alla conformità, impostare i revisori come revisori specifici, anziché eseguire la revisione autonoma per gli utenti esterni. Gli utenti che hanno accesso ai gestori di pacchetti sono un valido punto di partenza per i revisori. 

* Per i progetti meno sensibili, la revisione automatica degli utenti ridurrà il carico dell'organizzazione per rimuovere l'accesso dagli utenti che non hanno più la propria organizzazione principale.

Per altre informazioni, vedere [governare l'accesso per gli utenti esterni in Azure ad gestione dei diritti](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti (l'](6-secure-access-entitlement-managment.md) utente si trova qui).

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)

 

