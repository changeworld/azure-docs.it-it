---
title: Percorsi personalizzati-Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica concettuale della funzionalità dei percorsi personalizzati di Azure Arc abilitato Kubernetes
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451131"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Percorsi personalizzati su Azure Arc abilitato Kubernetes

Come estensione del costrutto di località di Azure, i *percorsi personalizzati* consentono agli amministratori tenant di usare i cluster Kubernetes abilitati per Azure Arc come percorsi di destinazione per la distribuzione delle istanze dei servizi di Azure. Gli esempi di risorse di Azure includono Azure Arc abilitato Istanza gestita SQL e la scalabilità di PostgreSQL abilitata per Azure Arc.

Analogamente alle località di Azure, gli utenti finali all'interno del tenant con accesso a posizioni personalizzate possono distribuire le risorse con il calcolo privato della propria azienda.

[![Livelli ](./media/conceptual-arc-platform-layers.png) della piattaforma Arc](./media/conceptual-arc-platform-layers.png#lightbox)

È possibile visualizzare percorsi personalizzati come un livello di astrazione sul cluster Kubernetes abilitato per Azure Arc, sulla connessione del cluster e sulle estensioni del cluster. Percorsi personalizzati crea i RoleBindings granulari [e ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) necessari per l'accesso da parte di altri servizi di Azure al cluster. Questi altri servizi di Azure richiedono l'accesso al cluster per gestire le risorse che il cliente vuole distribuire nei cluster.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architettura

Quando l'amministratore abilita la funzionalità percorsi personalizzati nel cluster, viene creato un ClusterRoleBinding nel cluster, che autorizza l'applicazione Azure AD usata dal provider di risorse dei percorsi personalizzati. Una volta autorizzate, i percorsi personalizzati RP possono creare ClusterRoleBindings o RoleBindings richiesti da altri RPs di Azure per creare risorse personalizzate in questo cluster. Le estensioni del cluster installate nel cluster determinano l'elenco di RPs da autorizzare.

[![Usare percorsi ](./media/conceptual-custom-locations-usage.png) personalizzati](./media/conceptual-custom-locations-usage.png#lightbox)

Quando l'utente crea un'istanza del servizio dati nel cluster: 
1. La richiesta PUT viene inviata a Azure Resource Manager.
1. La richiesta PUT viene trasmessa al componente Azure Arc Enabled Data Services. 
1. Il componente recupera il `kubeconfig` file associato al cluster Kubernetes abilitato per Azure Arc, in cui esiste il percorso personalizzato. 
   * Alla `extendedLocation` richiesta PUT originale viene fatto riferimento a un percorso personalizzato. 
1. Azure Arc Enabled Data Services RP USA `kubeconfig` per comunicare con il cluster per creare una risorsa personalizzata del tipo di servizi dati abilitati per Azure Arc nello spazio dei nomi mappato al percorso personalizzato. 
   * L'operatore Azure Arc Enabled Data Services è stato distribuito tramite la creazione dell'estensione del cluster prima del percorso personalizzato. 
1. L'operatore Azure Arc Enabled Data Services legge la nuova risorsa personalizzata creata nel cluster e crea il controller dati, traducendo la realizzazione dello stato desiderato nel cluster. 

La sequenza di passaggi per la creazione dell'istanza gestita di SQL e dell'istanza di PostgreSQL è identica alla sequenza dei passaggi descritti in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Usare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* [Creare un percorso personalizzato](./custom-locations.md) nel cluster Kubernetes abilitato per Azure Arc.