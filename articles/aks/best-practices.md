---
title: Procedure consigliate per il servizio Azure Kubernetes
description: Raccolta delle procedure consigliate per sviluppatori e operatori del cluster per la creazione e la gestione di applicazioni nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/09/2021
ms.openlocfilehash: f4bd109ce6827de26b1649380b6f6a2ba7c10e17
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105902"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Procedure consigliate per sviluppatori e operatori del cluster per la creazione e la gestione di applicazioni nel servizio Azure Kubernetes

La compilazione e l'esecuzione di applicazioni in Azure Kubernetes Service (AKS) richiedono la comprensione e l'implementazione di alcune considerazioni chiave, tra cui:
* Funzionalità di multi-tenant e utilità di pianificazione.
* Sicurezza del cluster e del Pod.
* Continuità aziendale e ripristino di emergenza. 


Il gruppo di prodotti AKS, i team di progettazione e i team dei campi (incluse le cinture nere globali [GBBs]) hanno contribuito, hanno scritto e raggruppato le procedure consigliate e gli articoli concettuali seguenti. Lo scopo è quello di aiutare gli operatori e gli sviluppatori di cluster a comprendere le considerazioni precedenti e implementare le funzionalità appropriate.


## <a name="cluster-operator-best-practices"></a>Procedure consigliate per gli operatori di cluster

Si consiglia agli operatori di cluster di collaborare con i proprietari di applicazioni e gli sviluppatori per comprendere le proprie esigenze. È quindi possibile usare le procedure consigliate seguenti per configurare i cluster del servizio Azure Kubernetes (AKS) in base a queste esigenze.

**Multi-tenancy**

* [Procedure consigliate per l'isolamento del cluster](operator-best-practices-cluster-isolation.md)
    * Principali componenti multi-tenant e isolamento logico con spazi dei nomi.
* [Procedure consigliate per le funzionalità di base dell'utilità di pianificazione](operator-best-practices-scheduler.md)
    * Quote di risorse e budget di interruzione dei pod.
* [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione](operator-best-practices-advanced-scheduler.md)
    * Uso di taint e tolleranze, selettori di nodi e affinità, affinità fra pod e anti-affinità.
* [Procedure consigliate per autenticazione e autorizzazione](operator-best-practices-identity.md)
    * Include l'integrazione con Azure Active Directory, usando il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC), usando le identità di Azure RBAC e Pod.

**Sicurezza**

* [Procedure consigliate per la sicurezza e gli aggiornamenti del cluster](operator-best-practices-cluster-security.md)
    * Protezione dell'accesso al server API, limitazione dell'accesso ai contenitori e gestione degli aggiornamenti e dei riavvii dei nodi.
* [Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore](operator-best-practices-container-image-management.md)
    * Include la protezione dell'immagine e dei runtime e delle compilazioni automatiche sugli aggiornamenti delle immagini di base.
* [Procedure consigliate per la sicurezza dei pod](developer-best-practices-pod-security.md)
    * Protezione dell'accesso alle risorse, limitazione dell'esposizione delle credenziali e uso di identità dei pod e insiemi di credenziali delle chiavi digitali.

**Rete e di archiviazione**

* [Procedure consigliate per la connettività di rete](operator-best-practices-network.md)
    * Diversi modelli di rete, uso di controller del traffico in ingresso e web application firewall (WAF) e protezione dell'accesso SSH ai nodi.
* [Procedure consigliate per archiviazione e backup](operator-best-practices-storage.md)
    * Scelta del tipo di archiviazione e delle dimensioni dei nodi appropriati, provisioning dinamico dei volumi e backup dei dati.

**Esecuzione di carichi di lavoro di livello aziendale**

* [Procedure consigliate su continuità aziendale e ripristino di emergenza](operator-best-practices-multi-region.md)
    * Uso di coppie di aree, più cluster con Gestione traffico di Azure e replica geografica delle immagini del contenitore.

## <a name="developer-best-practices"></a>Procedure consigliate per lo sviluppatore

Gli sviluppatori o i proprietari di applicazioni possono semplificare l'esperienza di sviluppo e definire le esigenze di prestazioni delle applicazioni.

* [Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse](developer-best-practices-resource-management.md)
    * Definizione delle richieste di risorse dei pod e dei relativi limiti, configurazione degli strumenti di sviluppo e controllo dei problemi delle applicazioni.
* [Procedure consigliate per la sicurezza dei pod](developer-best-practices-pod-security.md)
    * Protezione dell'accesso alle risorse, limitazione dell'esposizione delle credenziali e uso di identità dei pod e insiemi di credenziali delle chiavi digitali.

## <a name="kubernetes--aks-concepts"></a>Concetti relativi a Kubernetes e al servizio Azure Kubernetes

Per comprendere meglio alcuni dei componenti e delle funzionalità di queste procedure consigliate, vedere anche i seguenti articoli concettuali per i cluster nel servizio Azure Kubernetes:

* [Concetti principali di Kubernetes](concepts-clusters-workloads.md)
* [Accesso e identità](concepts-identity.md)
* [Concetti relativi alla sicurezza](concepts-security.md)
* [Concetti di rete](concepts-network.md)
* [Opzioni di archiviazione](concepts-storage.md)
* [Opzioni di scalabilità](concepts-scale.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni introduttive sul servizio Azure Kubernetes, vedere una delle guide di avvio rapido per la distribuzione di un cluster del servizio Azure Kubernetes (AKS) tramite l'[interfaccia della riga di comando di Azure](kubernetes-walkthrough.md) o il [portale di Azure](kubernetes-walkthrough-portal.md).
