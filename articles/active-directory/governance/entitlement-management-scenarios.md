---
title: Scenari comuni nella gestione degli entitlement - Azure AD
description: Informazioni sui passaggi di alto livello da seguire per scenari comuni nella Azure Active Directory entitlement.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531965"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Scenari comuni nella gestione Azure AD entitlement

Esistono diversi modi per configurare la gestione dei diritti per l'organizzazione. Tuttavia, se si è appena iniziato, è utile comprendere gli scenari comuni per amministratori, proprietari del catalogo, gestori di pacchetti di accesso, responsabili approvazione e richiedenti.

## <a name="delegate"></a>Delegato

### <a name="administrator-delegate-management-of-resources"></a>Amministratore: Delegare la gestione delle risorse

1. [Video: Delega dal reparto IT al responsabile del reparto](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegare gli utenti al ruolo di autore del catalogo](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Autore del catalogo: Delegare la gestione delle risorse

- [Crea un nuovo catalogo](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Proprietario del catalogo: Delegare la gestione delle risorse

1. [Aggiungere co-proprietari al catalogo](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Aggiungere risorse al catalogo](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Proprietario del catalogo: Delegare la gestione dei pacchetti di accesso

1. [Video: Delega dal proprietario del catalogo per accedere a Gestione pacchetti](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegare gli utenti per accedere al ruolo gestione pacchetti](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Gestire l'accesso per gli utenti dell'organizzazione

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Gestione pacchetti di accesso: consente ai dipendenti dell'organizzazione di richiedere l'accesso alle risorse

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere gruppi, Team, applicazioni o siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-create.md#resource-roles)
1. [Aggiungere criteri di richiesta per consentire agli utenti nella directory di richiedere l'accesso](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Richiedente: richiedere l'accesso alle risorse

1. [Accedere al portale Accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Trovare il pacchetto di accesso
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile approvazione: approvare le richieste alle risorse

1. [Open request in Accesso personale portal (Apri richiesta Accesso personale portale)](entitlement-management-request-approve.md#open-request)
1. [Approvare o negare la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Richiedente: visualizzare le risorse a cui si ha già accesso

1. [Accedere al portale Accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivo

## <a name="govern-access-for-users-outside-your-organization"></a>Gestire l'accesso per gli utenti esterni all'organizzazione

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Amministratore: collaborare con un'organizzazione partner esterna

1. [Informazioni sul funzionamento dell'accesso per gli utenti esterni](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Esaminare le impostazioni per gli utenti esterni](entitlement-management-external-users.md#settings-for-external-users)
1. [Aggiungere una connessione all'organizzazione esterna](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Accedere a Gestione pacchetti: collaborare con un'organizzazione partner esterna

1. [Creare un nuovo pacchetto di accesso](entitlement-management-access-package-create.md#start-new-access-package)
1. [Aggiungere gruppi, Team, applicazioni o siti di SharePoint per accedere al pacchetto](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Aggiungere criteri di richiesta per consentire agli utenti non presenti nella directory di richiedere l'accesso](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Specificare le impostazioni di scadenza](entitlement-management-access-package-create.md#lifecycle)
1. [Copiare il collegamento per richiedere il pacchetto di accesso](entitlement-management-access-package-settings.md)
1. Inviare il collegamento al partner partner esterno per condividerlo con gli utenti

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Richiedente: richiedere l'accesso alle risorse come utente esterno

1. Trovare il collegamento al pacchetto di accesso ricevuto dal contatto
1. [Accedere al portale Accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Richiedere l'accesso](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Responsabile approvazione: approvare le richieste alle risorse

1. [Open request in Accesso personale portal (Apri richiesta Accesso personale portale)](entitlement-management-request-approve.md#open-request)
1. [Approvare o negare la richiesta di accesso](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Richiedente: visualizzare le risorse a cui l'utente ha già accesso

1. [Accedere al portale Accesso personale](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visualizzare i pacchetti di accesso attivo

## <a name="day-to-day-management"></a>Gestione quotidiana

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Accedere a Gestione pacchetti: aggiornare le risorse per un progetto

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aggiungere o rimuovere gruppi, Team, applicazioni o siti di SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Accedere a Gestione pacchetti: aggiornare la durata di un progetto

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Aprire il pacchetto di accesso
1. [Aprire le impostazioni del ciclo di vita](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aggiornare le impostazioni di scadenza](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Gestione pacchetti di Accesso: aggiornare la modalità di approvazione dell'accesso per un progetto

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Aprire un criterio esistente di impostazioni della richiesta](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aggiornare le impostazioni di approvazione](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Accedere a Gestione pacchetti: aggiornare le persone per un progetto

1. [Video: Gestione quotidiana: Le cose sono cambiate](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Rimuovere gli utenti che non necessitano più dell'accesso](entitlement-management-access-package-assignments.md)
1. [Aprire un criterio esistente di impostazioni della richiesta](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aggiungere gli utenti che necessitano dell'accesso](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Accedere a Gestione pacchetti: assegnare direttamente utenti specifici a un pacchetto di accesso

1. [Se gli utenti necessitano di impostazioni del ciclo di vita diverse, aggiungere un nuovo criterio al pacchetto di accesso](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Assegnare direttamente utenti specifici al pacchetto di accesso](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Assegnazioni e report

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Amministratore: visualizzare chi ha le assegnazioni a un pacchetto di accesso

1. Aprire un pacchetto di accesso
1. [Visualizzare le assegnazioni](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiviare report e log](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Amministratore: visualizzare le risorse assegnate agli utenti

1. [Visualizzare i pacchetti di accesso per un utente](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visualizzare le assegnazioni di risorse per un utente](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Amministrazione a livello di codice

È anche possibile gestire pacchetti di accesso, cataloghi, criteri, richieste e assegnazioni usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione che dispone dell'autorizzazione delegata `EntitlementManagement.ReadWrite.All` può chiamare [l'API di gestione entitlement](/graph/tutorial-access-package-api).

## <a name="next-steps"></a>Passaggi successivi

- [Delega e ruoli](entitlement-management-delegate.md)
- [Richiedere il processo e le notifiche tramite posta elettronica](entitlement-management-process.md)