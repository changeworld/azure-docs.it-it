---
title: Ripristino automatico di nodi del servizio Azure Kubernetes (AKS)
description: Informazioni sulle funzionalità di correzione automatica dei nodi e sul modo in cui AKS corregge i nodi di lavoro interrotti.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105205"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Ripristino automatico del nodo di Azure Kubernetes Service (AKS)

AKS monitora continuamente lo stato di integrità dei nodi del ruolo di lavoro ed esegue la correzione automatica del nodo se diventano non integri. La piattaforma della macchina virtuale (VM) [di Azure esegue la manutenzione delle macchine][vm-updates] virtuali che riscontrano problemi. 

AKS e le VM di Azure interagiscono per ridurre al minimo le rotture dei servizi per i cluster.

In questo documento si apprenderà come si comporta la funzionalità di correzione automatica dei nodi per i nodi Windows e Linux. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Modalità di verifica dei nodi non integri da AKS

AKS usa le regole seguenti per determinare se un nodo non è integro e deve essere ripristinato: 
* Il nodo segnala lo stato **Nopedatey** nei controlli consecutivi entro un intervallo di tempo di 10 minuti.
* Il nodo non segnala alcuno stato entro 10 minuti.

È possibile controllare manualmente lo stato di integrità dei nodi con kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Funzionamento della riparazione automatica

> [!Note]
> AKS avvia le operazioni di ripristino con l'account utente **AKS-remediator**.

Se AKS identifica un nodo non integro che rimane non integro per 10 minuti, AKS esegue le azioni seguenti:

1. Riavviare il nodo.
1. Se il riavvio ha esito negativo, ricreare l'immagine del nodo.
1. Se la ricreazione dell'immagine ha esito negativo, creare e ricreare l'immagine di un nuovo nodo.

Le correzioni alternative vengono esaminate da tecnici AKS se la riparazione automatica ha esito negativo. 

Se AKS trova più nodi non integri durante un controllo di integrità, ogni nodo viene ripristinato singolarmente prima dell'avvio di un'altra riparazione.

## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster del servizio Azure Kubernetes.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
