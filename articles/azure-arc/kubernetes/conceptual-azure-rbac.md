---
title: Azure RBAC-Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica concettuale della funzionalità RBAC di Azure in Azure Arc Enabled Kubernetes
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451100"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC in Azure Arc abilitato Kubernetes

I tipi di oggetto Kubernetes [ClusterRoleBinding e Rolet](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) consentono di definire l'autorizzazione in Kubernetes in modo nativo. Con il controllo degli accessi in base al ruolo di Azure, è possibile usare Azure Active Directory (Azure AD) e assegnazioni di ruolo in Azure per controllare i controlli di autorizzazione nel cluster.

Con questa funzionalità, tutti i vantaggi delle assegnazioni di ruolo di Azure, ad esempio i log attività che mostrano tutte le modifiche RBAC di Azure a una risorsa di Azure, diventano ora applicabili per il cluster Kubernetes abilitato per Azure Arc.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architettura-controllo degli accessi in base al ruolo di Azure abilitato Kubernetes

[![Architettura ](./media/conceptual-azure-rbac.png) RBAC di Azure](./media/conceptual-azure-rbac.png#lightbox)

Per eseguire il routing di tutti i controlli di accesso di autorizzazione al servizio di autorizzazione in Azure, viene distribuito un server webhook ([Guard](https://github.com/appscode/guard)) nel cluster.

Il `apiserver` del cluster è configurato per usare l' [autenticazione basata su token webhook](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) e l' [autorizzazione webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) , in modo che le `TokenAccessReview` `SubjectAccessReview` richieste e vengano indirizzate al server di Webhook Guard. Le `TokenAccessReview` `SubjectAccessReview` richieste e vengono attivate dalle richieste di risorse Kubernetes inviate a `apiserver` .

Guard esegue quindi una `checkAccess` chiamata sul servizio di autorizzazione in Azure per verificare se l'entità richiesta Azure ad ha accesso alla risorsa di interesse. 

Se un ruolo nell'assegnazione che consente l'accesso esiste, `allowed` viene inviata una risposta da Authorization Service Guard. Guard, a sua volta, invia una `allowed` risposta a `apiserver` , consentendo all'entità chiamante di accedere alla risorsa Kubernetes richiesta.


Se un ruolo nell'assegnazione che consente questo accesso non esiste, `denied` viene inviata una risposta dal servizio di autorizzazione a Guard. Guard invia una `denied` risposta a `apiserver` , assegnando all'entità chiamante un errore 403 non consentito nella risorsa richiesta.

## <a name="next-steps"></a>Passaggi successivi

* Usare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* [Configurare](./azure-rbac.md) il controllo degli accessi in base al ruolo di Azure nel cluster Kubernetes abilitato per Azure Arc.