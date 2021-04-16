---
title: Usare Azure AD Identity Governance per esaminare e rimuovere gli utenti esterni che non hanno più accesso alle risorse
description: Usare le verifiche di accesso per estendere la rimozione dell'accesso dai membri delle organizzazioni partner
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532236"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Usare Azure Active Directory governance delle identità (Azure AD) per esaminare e rimuovere gli utenti esterni che non hanno più accesso alle risorse

Questo articolo descrive le funzionalità e i metodi che consentono di individuare e selezionare identità esterne in modo che sia possibile esaminarle e rimuoverle dal Azure AD se non sono più necessarie. Il cloud rende più semplice che mai collaborare con utenti interni o esterni. In base a Office 365, le organizzazioni iniziano a vedere la proliferazione di identità esterne (inclusi i guest), in quanto gli utenti lavorano insieme su dati, documenti o aree di lavoro digitali, ad esempio Teams. Le organizzazioni devono bilanciare, abilitare la collaborazione e soddisfare i requisiti di sicurezza e governance. Parte di questi sforzi deve includere la valutazione e la pulizia degli utenti esterni, invitati per la collaborazione nel tenant, provenienti dalle organizzazioni partner, e la loro rimozione dal Azure AD quando non sono più necessari.

>[!NOTE]
>Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Perché esaminare gli utenti di organizzazioni esterne nel tenant?

Nella maggior parte delle organizzazioni, gli utenti finali avviano il processo di invito a partner commerciali e fornitori per la collaborazione. La necessità di collaborare consente alle organizzazioni di offrire ai proprietari delle risorse e agli utenti finali un modo per valutare e attestare regolarmente gli utenti esterni. Spesso il processo di onboarding di nuovi partner di collaborazione è pianificato e contabile, ma con molte collaborazione che non hanno una data di fine chiara, non è sempre ovvio quando un utente non ha più bisogno dell'accesso. Inoltre, la gestione del ciclo di vita delle identità consente alle aziende di mantenere Azure AD e rimuovere gli utenti che non hanno più bisogno di accedere alle risorse dell'organizzazione. Mantenere nella directory solo i riferimenti di identità rilevanti per partner e fornitori consente di ridurre il rischio dei dipendenti, selezionando e concedendo inavvertitamente l'accesso a utenti esterni che dovrebbero essere stati rimossi. Questo documento illustra diverse opzioni che vanno dai suggerimenti proattivi consigliati alle attività reattive e di pulizia per gestire le identità esterne.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Usare Entitlement Management per concedere e revocare l'accesso

Le funzionalità di gestione degli entitlement consentono il ciclo di [vita automatizzato delle identità esterne](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) con accesso alle risorse. Stabilendo processi e procedure per gestire l'accesso tramite Entitlement Management e pubblicando le risorse tramite pacchetti di accesso, tenere traccia dell'accesso degli utenti esterni alle risorse diventa un problema molto meno complesso da risolvere. Quando si gestisce l'accesso tramite entitlement [management access packages](entitlement-management-overview.md) in Azure AD, l'organizzazione può definire e gestire centralmente l'accesso per gli utenti, nonché per gli utenti delle organizzazioni partner. Entitlement Management usa le approvazioni e le assegnazioni dei pacchetti di accesso per tenere traccia della posizione in cui gli utenti esterni hanno richiesto e assegnato l'accesso. In caso di perdita di tutte le assegnazioni da parte di un utente esterno, Gestione entitlement può rimuovere automaticamente questi utenti esterni dal tenant. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Trovare i guest non invitati tramite Entitlement Management

Quando i dipendenti sono autorizzati a collaborare con utenti esterni, possono invitare un numero qualsiasi di utenti esterni all'organizzazione. La ricerca e il raggruppamento di partner esterni in gruppi dinamici allineati all'azienda e la loro revisione potrebbero non essere fattibili, perché potrebbero essere presenti troppe singole aziende diverse da esaminare o non esiste alcun proprietario o sponsor per l'organizzazione. Microsoft fornisce uno script di PowerShell di esempio che consente di analizzare l'uso di identità esterne in un tenant. Lo script enumera le identità esterne e le classifica. Lo script consente di identificare ed eliminare le identità esterne che potrebbero non essere più necessarie. Come parte dell'output dello script, l'esempio di script supporta la creazione automatica di gruppi di sicurezza che contengono i partner esterni senza gruppo identificati, per un'ulteriore analisi e l'uso con le verifiche di accesso Azure AD sicurezza.
Lo script è disponibile in [GitHub.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) Al termine dell'esecuzione, lo script genera un file di output HTML che descrive le identità esterne che:

- Non sono più presenti appartenenze a gruppi nel tenant
- Avere un'assegnazione per un ruolo con privilegi nel tenant
- Avere un'assegnazione a un'applicazione nel tenant

L'output include anche i singoli domini per ognuna di queste identità esterne. 

>[!NOTE]
>Lo script a cui si fa riferimento in precedenza è uno script di esempio che verifica l'appartenenza ai gruppi, le assegnazioni di ruolo e le assegnazioni di applicazioni Azure AD. Possono essere presenti altre assegnazioni nelle applicazioni che gli utenti esterni hanno ricevuto all'esterno di Azure AD, ad esempio SharePoint (assegnazione di appartenenza diretta) o controllo degli accessi in base al ruolo di Azure o Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Esaminare le risorse usate dalle identità esterne

Se si dispone di identità esterne che usano risorse come Teams o altre applicazioni non ancora gestite da Entitlement Management, è anche possibile esaminare regolarmente l'accesso a queste risorse. Azure AD verifiche [](create-access-review.md) di accesso consente di esaminare l'accesso delle identità esterne consentendo al proprietario della risorsa, alle identità esterne o a un'altra persona delegata attendibile di attestare se l'accesso continuo è necessario. Le verifiche di accesso hanno come destinazione una risorsa e creano un'attività di revisione con ambito Tutti gli utenti che hanno accesso alla risorsa o solo agli utenti guest. Il revisore visualizza quindi l'elenco risultante degli utenti che devono esaminare, ad esempio tutti gli utenti, inclusi i dipendenti dell'organizzazione o solo le identità esterne.

![uso di un gruppo per verificare l'accesso](media/access-reviews-external-users/group-members.png)

La definizione di impostazioni cultura di revisione guidate dal proprietario della risorsa consente di controllare l'accesso per le identità esterne. I proprietari delle risorse, responsabili dell'accesso, della disponibilità e della sicurezza delle informazioni di cui sono proprietari, sono, nella maggior parte dei casi, i destinatari migliori per prendere decisioni relative all'accesso alle proprie risorse e sono più vicini agli utenti che accedono rispetto all'IT centrale o a uno sponsor che gestisce molti esterni.

## <a name="create-access-reviews-for-external-identities"></a>Creare verifiche di accesso per identità esterne

Gli utenti che non hanno più accesso alle risorse nel tenant possono essere rimossi se non lavorano più con l'organizzazione. Prima di bloccare ed eliminare queste identità esterne, è possibile contattare questi utenti esterni e assicurarsi di non aver trascurato un progetto o l'accesso permanente di cui hanno ancora bisogno. Quando si crea un gruppo che contiene tutte le identità esterne come membri che non hanno accesso alle risorse nel tenant, è possibile usare Le verifiche di accesso per fare in modo che tutti gli utenti esterni possano attestare automaticamente se hanno ancora bisogno o hanno accesso o avranno ancora bisogno dell'accesso in futuro. Come parte della revisione, l'autore della  revisione in Verifiche di accesso può usare la funzione Richiedi motivo per l'approvazione per richiedere agli utenti esterni di fornire una giustificazione per l'accesso continuo, tramite il quale è possibile scoprire dove e come devono ancora accedere al tenant. È anche possibile abilitare  l'impostazione Contenuto aggiuntivo per la funzionalità di posta elettronica del revisore, per in modo che gli utenti sappiano che perderanno l'accesso se non rispondono e, se hanno ancora bisogno di accesso, è necessaria una giustificazione. Se si vuole procedere e  consentire a Verifiche di accesso di disabilitare ed eliminare identità esterne, se non riescono a rispondere o fornire un motivo valido per l'accesso continuo, è possibile usare l'opzione Disabilita ed elimina, come descritto nella sezione successiva.

![limitazione dell'ambito della revisione solo agli utenti guest](media/access-reviews-external-users/guest-users-only.png)

Al termine della revisione, la **pagina Risultati** mostra una panoramica della risposta fornita da ogni identità esterna. È possibile scegliere di applicare automaticamente i risultati e consentire alle verifiche di accesso di disabilitarli ed eliminarli. In alternativa, è possibile esaminare le risposte fornite e decidere se si vuole rimuovere l'accesso o il follow-up di un utente e ottenere informazioni aggiuntive prima di prendere una decisione. Se alcuni utenti hanno ancora accesso alle risorse non ancora esaminate, è possibile usare la revisione come parte dell'individuazione e arricchire il ciclo di revisione e attestazione successivo.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Disabilitare ed eliminare identità esterne con verifiche Azure AD di accesso

Oltre alla possibilità di rimuovere identità esterne indesiderate da risorse come gruppi o applicazioni, le verifiche di accesso di Azure AD possono impedire alle identità esterne di accedere al tenant ed eliminare le identità esterne dal tenant dopo 30 giorni. Dopo aver selezionato Blocca l'accesso dell'utente per **30 giorni,** quindi rimuovere l'utente dal tenant, la revisione rimarrà nello stato di "applicazione" per 30 giorni. Durante questo periodo, le impostazioni, i risultati, i revisori o i log di controllo nella revisione corrente non saranno visualizzabili o configurabili. 

![al completamento delle impostazioni](media/access-reviews-external-users/upon-completion-settings.png)

Quando si crea una nuova verifica di accesso,  nella sezione "Al completamento delle impostazioni" per Azione da applicare agli utenti negati è possibile definire Blocca l'accesso degli utenti per **30 giorni,** quindi rimuovere l'utente dal tenant .

Questa impostazione consente di identificare, bloccare ed eliminare identità esterne dal tenant Azure AD tenant. Le identità esterne a cui viene esaminato e negato l'accesso continuo dal revisore verranno bloccate ed eliminate, indipendentemente dall'accesso alle risorse o dall'appartenenza al gruppo di cui dispone. Questa impostazione è consigliata come ultimo passaggio dopo aver verificato che gli utenti esterni in revisione non hanno più accesso alle risorse e possono essere rimossi senza problemi dal tenant o se si vuole assicurarsi che siano rimossi, indipendentemente dall'accesso permanente. La funzionalità "Disabilita ed elimina" blocca prima l'utente esterno, cancellando la possibilità di accedere al tenant e di accedere alle risorse. L'accesso alle risorse non viene revocato in questa fase e, nel caso in cui si voglia creare nuovamente un'istanza dell'utente esterno, è possibile riconfigurare la possibilità di accesso. In assenza di altre azioni, un'identità esterna bloccata verrà eliminata dalla directory dopo 30 giorni, rimuovendo l'account e il relativo accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Verifiche di accesso - API Graph](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Gestione entitlement - API Graph](/graph/api/resources/entitlementmanagement-root)
