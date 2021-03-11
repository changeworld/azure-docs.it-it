---
title: Scopri lo stato corrente della collaborazione esterna con Azure Active Directory
description: Informazioni sui metodi per individuare lo stato corrente della collaborazione.
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
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553426"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Scopri lo stato attuale della collaborazione esterna nell'organizzazione 

Prima di individuare lo stato corrente della collaborazione esterna, è necessario [determinare il comportamento di sicurezza desiderato](1-secure-access-posture.md). Si prenderanno in considerazione le esigenze dell'organizzazione per il controllo centralizzato rispetto a quello delegato ed eventuali destinazioni di governance, normative e conformità pertinenti. 

Gli utenti dell'organizzazione probabilmente già collaborano con gli utenti di altre organizzazioni. La collaborazione può avvenire attraverso le funzionalità di applicazioni di produttività come Microsoft 365, tramite posta elettronica o condividendo in altro modo risorse con utenti esterni. I pilastri del piano di governance si formano durante l'individuazione 
*   utenti che avviano la collaborazione esterna.
*   utenti e organizzazioni esterni con cui si collabora.
*   accesso concesso a utenti esterni.


## <a name="users-initiating-external-collaboration"></a>Utenti che avviano la collaborazione esterna

Gli utenti che avviano la collaborazione esterna comprendono meglio le applicazioni più rilevanti per la collaborazione esterna e quando tale accesso deve terminare. La comprensione di questi utenti può essere utile per determinare gli utenti che devono disporre dell'autorizzazione delegata per invitare utenti esterni, creare pacchetti di accesso e verifiche di accesso complete.

Per trovare gli utenti che attualmente collaborano, esaminare il [Registro di controllo Microsoft 365 per le attività di condivisione e di richiesta di accesso](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). È anche possibile esaminare il [Registro di controllo Azure ad per informazioni dettagliate sugli utenti che hanno invitato](../external-identities/auditing-and-reporting.md) gli utenti B2B alla propria directory.

## <a name="find-current-collaboration-partners"></a>Trova i partner di collaborazione correnti

Gli utenti esterni possono essere [Azure ad utenti B2B](../external-identities/what-is-b2b.md) (preferibili) con credenziali gestite dal partner o utenti esterni con credenziali con provisioning locale. Questi utenti sono in genere (ma non sempre) contrassegnati con un UserType di Guest. È possibile enumerare gli utenti guest tramite l' [API Microsoft Graph](/graph/api/user-list?tabs=http), [PowerShell](/graph/api/user-list?tabs=http)o l' [portale di Azure](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Usare i domini di posta elettronica e la proprietà companyName

Le organizzazioni esterne possono essere determinate dai nomi di dominio degli indirizzi di posta elettronica degli utenti esterni. Se sono supportati provider di identità utente come Google, questa operazione potrebbe non essere possibile. In questo caso è consigliabile scrivere l'attributo companyName per identificare chiaramente l'organizzazione esterna dell'utente.

### <a name="use-allow-or-deny-lists"></a>Usare gli elenchi Consenti o nega

Valutare se l'organizzazione desidera consentire la collaborazione solo con organizzazioni specifiche. In alternativa, valutare se l'organizzazione vuole bloccare la collaborazione con organizzazioni specifiche.  A livello di tenant è disponibile un [elenco Consenti o nega](../external-identities/allow-deny-list.md), che può essere usato per controllare gli inviti e i riscatti B2B complessivi indipendentemente dall'origine, ad esempio teams, SharePoint e il portale di Azure.
Se si usa la gestione dei diritti, è anche possibile definire l'ambito dei pacchetti di accesso a un subset di partner usando le specifiche organizzazioni connesse, come illustrato di seguito.


![Screenshot dell'elenco Consenti negazione per la creazione di un nuovo pacchetto di accesso.](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Trovare l'accesso concesso a utenti esterni

Quando si dispone di un inventario di utenti e organizzazioni esterni, è possibile determinare l'accesso concesso a questi utenti usando l'API Microsoft Graph per determinare l' [appartenenza](/graph/api/resources/groups-overview) a un gruppo Azure ad o l' [assegnazione diretta dell'applicazione](/graph/api/resources/approleassignment) in Azure ad.


### <a name="enumerate-application-specific-permissions"></a>Enumera autorizzazioni specifiche dell'applicazione

Potrebbe anche essere possibile eseguire l'enumerazione delle autorizzazioni specifiche dell'applicazione. Ad esempio, è possibile generare a livello di codice un report sulle autorizzazioni per SharePoint Online utilizzando [questo script](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64).

Esaminare in modo specifico l'accesso a tutte le app aziendali e critiche per l'azienda, in modo da essere pienamente a conoscenza di qualsiasi accesso esterno.

### <a name="detect-ad-hoc-sharing"></a>Rileva condivisione ad hoc
Se il messaggio di posta elettronica e i piani di rete lo abilitano, è possibile esaminare i contenuti condivisi tramite posta elettronica o tramite app non autorizzate Software as a Service (SaaS). [Microsoft 365 la protezione dalla perdita di dati](/microsoft-365/compliance/data-loss-prevention-policies) consente di identificare, prevenire e monitorare la condivisione accidentale di informazioni riservate nell'infrastruttura Microsoft 365. [Microsoft cloud app Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) possono essere utili per identificare l'uso di app SaaS non autorizzate nell'ambiente in uso.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individuare lo stato corrente](2-secure-access-current-state.md) .

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)