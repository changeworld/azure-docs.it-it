---
title: Informazioni sulla gestione delle applicazioni in Azure Active Directory
description: Panoramica dell'uso di Azure Active Directory (AD) come sistema di gestione delle identità e degli accessi per le applicazioni cloud e locali.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376393"
---
# <a name="what-is-application-management"></a>Cos'è la gestione delle applicazioni?

Azure AD è un sistema di gestione delle identità e degli accessi (IAM). Fornisce un'unica posizione in cui archiviare le informazioni sulle identità digitali. È possibile configurare le applicazioni software per usare Azure AD come posizione di archiviazione delle informazioni sugli utenti. 

Azure AD deve essere configurato per l'integrazione con un'applicazione. In altre parole, deve sapere quali app lo usano per le identità. La Azure AD di queste app e del modo in cui deve gestirle è nota come gestione delle applicazioni.

Le applicazioni vengono gestite nella **pagina** Applicazioni aziendali disponibile nella sezione Gestione del portale Azure Active Directory aziendale.

![Opzione Applicazioni aziendali nella sezione Gestione del portale di Azure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Che cos'è un sistema di gestione delle identità e degli accessi (IAM)?
Un'applicazione è un software usato per uno scopo specifico. La maggior parte delle app richiede agli utenti di eseguire l'accesso.

Un sistema di identità centralizzato offre un'unica posizione in cui archiviare le informazioni utente che possono quindi essere usate da tutte le applicazioni. Questi sistemi sono noti come sistemi di gestione delle identità e degli accessi (IAM). Azure Active Directory è il sistema IAM per il cloud Microsoft.

>[!TIP]
>Un sistema IAM fornisce un'unica posizione in cui tenere traccia delle identità degli utenti. Azure AD è il sistema IAM per il cloud Microsoft.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Perché gestire le applicazioni con una soluzione cloud?

In molte organizzazioni sono presenti centinaia di applicazioni a cui devono ricorrere i dipendenti per poter portare a termine il proprio lavoro. Gli utenti accedono a queste applicazioni da dispositivi e luoghi diversi. Le nuove applicazioni vengono aggiunte, sviluppate e disde affatto in qualsiasi momento. Con così tanti punti di accesso e app, è importante usare una soluzione di gestione delle identità che funzioni con tutte.

>[!TIP]
>La raccolta di app di Azure AD contiene molte delle applicazioni più diffuse già preconfigurate per interagire con Azure AD come provider di identità.

## <a name="how-does-azure-ad-work-with-apps"></a>Come funziona Azure AD le app?

Azure AD si trova al centro e fornisce la gestione delle identità per le app cloud e locali. 

![Diagramma che mostra le app federate tramite Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Ridurre i costi amministrativi [automatizzando il provisioning degli utenti](../app-provisioning/user-provisioning.md) in modo che gli utenti Azure AD aggiunti automaticamente al sistema hr aziendale. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Quali tipi di applicazioni è possibile integrare con Azure AD?

È possibile usare Azure AD come sistema di identità per qualsiasi app. Molte app sono già preconfigurato e possono essere configurate con il minimo sforzo. Queste app preconfigurato vengono pubblicate in [App Azure AD Gallery.](/azure/active-directory/saas-apps/) 

È possibile configurare manualmente la maggior parte delle app per l'accesso Single Sign-On, se non sono già presenti nella raccolta. Azure AD offre diverse opzioni per l'accesso SSO. Alcuni dei più diffusi sono l'accesso SSO basato su SAML e l'accesso SSO basato su OIDC. Per altre informazioni sull'integrazione di app per abilitare l'accesso Single Sign-On, vedere [Opzioni di Single Sign-On.](sso-options.md) 

L'organizzazione usa app locali? È possibile integrarli usando il proxy dell'app. Per altre informazioni, vedere Fornire l'accesso remoto alle applicazioni locali [Azure AD'Application Proxy](application-proxy.md).

>[!TIP]
>Quando si compilano applicazioni line-of-business personalizzate, è possibile integrarle con Azure AD per supportare l'accesso Single Sign-On. Per altre informazioni sullo sviluppo di app per Azure AD, vedere [Microsoft Identity Platform.](..//develop/v2-overview.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Gestione dei rischi con i criteri di accesso condizionale

Combinando l'accesso Single Sign-On (SSO) di Azure AD con l'[accesso condizionale](../conditional-access/concept-conditional-access-cloud-apps.md), è possibile garantire elevati livelli di sicurezza per l'accesso alle applicazioni. I criteri di accesso condizionale forniscono un controllo granulare alle app in base alle condizioni impostate. 

## <a name="improve-productivity-with-single-sign-on"></a>Migliorare la produttività con l'accesso Single Sign-On

L'accesso Single Sign-On (SSO) offre un'esperienza utente unificata tra Microsoft 365 e tutte le altre app usate. Dire arrivederci all'immissione costante del nome utente e della password.

Per altre informazioni sull'accesso Single Sign-On, vedere [Informazioni sull'accesso Single Sign-On.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Gestione della governance e della conformità

Monitorare le app tramite report che usano gli strumenti SiEM (Security Incident and Event Monitoring). È possibile accedere ai report dal portale o dalle API. A livello di codice, è possibile anche controllare chi può accedere alle applicazioni e rimuovere l'accesso agli utenti inattivi tramite verifiche di accesso.

## <a name="manage-costs"></a>Gestire i costi

Eseguendo la migrazione ad Azure AD è possibile anche risparmiare sui costi ed eliminare le complessità correlate alla gestione dell'infrastruttura locale. Azure AD consente infatti l'accesso self-service alle applicazioni, con un considerevole risparmio di tempo per gli amministratori e gli utenti. L'accesso Single Sign-On elimina le password specifiche delle applicazioni. La possibilità di eseguire l'accesso una sola volta consente di risparmiare sui costi correlati alla reimpostazione delle password delle applicazioni e alla perdita di produttività per il recupero delle password.

Per le applicazioni incentrate sulle risorse umane o altre applicazioni con un ampio set di utenti, è possibile usare il provisioning delle app per semplificare la vita. Il provisioning delle app automatizza il processo di aggiunta e rimozione di utenti. Per altre informazioni, vedere [Che cos'è il provisioning delle applicazioni?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
- [Introduzione all'integrazione delle applicazioni](plan-an-application-integration.md)
- [Come automatizzare il provisioning](../app-provisioning/user-provisioning.md)