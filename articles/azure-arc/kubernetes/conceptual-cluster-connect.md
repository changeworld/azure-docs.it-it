---
title: Connessione cluster-Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica concettuale della funzionalità di connessione cluster di Azure Arc Enabled Kubernetes
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451099"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Connessione cluster in Azure Arc abilitata Kubernetes

La funzionalità di *connessione cluster* Kubernetes abilitata per Azure Arc fornisce la connettività al `apiserver` del cluster senza che sia necessario abilitare alcuna porta in ingresso nel firewall. Un agente proxy inverso in esecuzione nel cluster può avviare in modo sicuro una sessione con il servizio Azure Arc in uscita. 

La connessione cluster consente agli sviluppatori di accedere ai propri cluster da qualsiasi luogo per lo sviluppo interattivo e il debug. Consente inoltre agli utenti e amministratori del cluster di accedere o gestire i cluster da qualsiasi luogo. È anche possibile usare gli agenti o i runner ospitati di Azure Pipelines, azioni GitHub o qualsiasi altro servizio CI/CD ospitato per distribuire le applicazioni nei cluster locali, senza richiedere agenti indipendenti.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architettura

[![Architettura ](./media/conceptual-cluster-connect.png) di connessione cluster](./media/conceptual-cluster-connect.png#lightbox)

Sul lato cluster, un agente proxy inverso denominato `clusterconnect-agent` distribuito come parte del grafico Helm di Agent, effettua chiamate in uscita al servizio Azure Arc per stabilire la sessione.

Quando l'utente chiama `az connectedk8s proxy` :
1. Il file binario del proxy di Azure Arc viene scaricato e attivato come processo nel computer client. 
1. Il proxy di Azure Arc recupera un `kubeconfig` file associato al cluster Kubernetes abilitato per Azure Arc in cui `az connectedk8s proxy` viene richiamato.
    * Il proxy di Azure Arc usa il token di accesso di Azure del chiamante e il nome ID del Azure Resource Manager. 
1. Il `kubeconfig` file, salvato nel computer dal proxy di Azure Arc, punta l'URL del server a un endpoint nel processo del proxy di Azure Arc.

Quando un utente invia una richiesta utilizzando questo `kubeconfig` file:
1. Il proxy di Azure Arc esegue il mapping dell'endpoint che riceve la richiesta al servizio Azure Arc. 
1. Il servizio Azure Arc quindi Invia la richiesta a in `clusterconnect-agent` esecuzione nel cluster. 
1. `clusterconnect-agent`Passa alla richiesta al `kube-aad-proxy` componente, che esegue l'autenticazione Azure ad sull'entità chiamante. 
1. Dopo l'autenticazione Azure AD, `kube-aad-proxy` Usa la funzionalità di [rappresentazione utente](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) di Kubernetes per inviare la richiesta al cluster `apiserver` .

## <a name="next-steps"></a>Passaggi successivi

* Usare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* [Accedi](./cluster-connect.md) in modo sicuro al cluster ovunque ti trovi usando la connessione del cluster.