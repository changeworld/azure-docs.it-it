---
title: Architettura dell'agente Kubernetes abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica dell'architettura degli agenti Kubernetes abilitati per Azure Arc
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: 287ffdd40dc9ffdb91abb58b305d8b35b0bc3674
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652565"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architettura dell'agente Kubernetes abilitato per Azure Arc

[Kubernetes](https://kubernetes.io/) è in grado di distribuire i carichi di lavoro in contenitori in modo coerente in ambienti ibridi e multicloud. Azure Arc Enabled Kubernetes, tuttavia, funziona come un piano di controllo coerente e centralizzato per la gestione di criteri, governance e sicurezza in ambienti eterogeneo. Questo articolo include:

* Panoramica dell'architettura della connessione di un cluster ad Azure Arc.
* Modello di connettività seguito dagli agenti.
* Una descrizione dei dati scambiati tra l'ambiente cluster e Azure.

## <a name="deploy-agents-to-your-cluster"></a>Distribuire gli agenti nel cluster

La maggior parte dei data center locali applica regole di rete rigide che impediscono la comunicazione in ingresso sul firewall dei limiti di rete. Il Kubernetes abilitato per Azure Arc funziona con queste restrizioni non richiedendo porte in ingresso nel firewall e abilitando solo gli endpoint in uscita selettiva per le comunicazioni in uscita. Gli agenti Kubernetes abilitati per Azure Arc avviano questa comunicazione in uscita. 

![Panoramica dell'architettura](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Connettere un cluster ad Azure Arc

1. Creare un cluster Kubernetes nell'infrastruttura scelta (VMware vSphere, Amazon Web Services, Google Cloud Platform e così via). 

    > [!NOTE]
    > Poiché Azure Arc Enabled Kubernetes attualmente supporta solo il fissaggio di cluster Kubernetes esistenti ad Azure Arc, i clienti devono creare e gestire il ciclo di vita del cluster Kubernetes.  

1. Avviare la registrazione di Azure Arc per il cluster usando l'interfaccia della riga di comando di Azure.
    * L'interfaccia della riga di comando di Azure usa Helm per distribuire il grafico Helm agente nel cluster.
    * I nodi del cluster avviano una comunicazione in uscita con il [container Registry Microsoft](https://github.com/microsoft/containerregistry) ed effettuano il pull delle immagini necessarie per creare gli agenti seguenti nello `azure-arc` spazio dei nomi:

        | Agente | Descrizione |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc Enabled Kubernetes supporta attualmente solo le [identità assegnate dal sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). `clusteridentityoperator` avvia la prima comunicazione in uscita. Questa prima comunicazione Recupera il certificato di identità del servizio gestita (MSI) usato da altri agenti per la comunicazione con Azure. |
        | `deployment.apps/config-agent` | Controlla il cluster connesso per le risorse di configurazione del controllo del codice sorgente applicate al cluster. Aggiorna lo stato di conformità. |
        | `deployment.apps/controller-manager` | Operatore di operatori che orchestra le interazioni tra i componenti di Azure Arc. |    
        | `deployment.apps/metrics-agent` | Raccoglie le metriche di altri agenti di arco per verificare le prestazioni ottimali. |
        | `deployment.apps/cluster-metadata-operator` | Raccoglie i metadati del cluster, tra cui la versione del cluster, il numero di nodi e la versione dell'agente Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Sincronizza i metadati del cluster sopra indicati in Azure. |
        | `deployment.apps/flux-logs-agent` | Raccoglie i log degli operatori Flux distribuiti come parte della configurazione del controllo del codice sorgente. |
    
1. Quando tutti i pod dell'agente Kubernetes abilitati per Azure Arc si trovano nello `Running` stato, verificare che il cluster sia connesso ad Azure Arc. Verrà visualizzato quanto segue:
    * Una risorsa Kubernetes abilitata per Azure Arc in [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure tiene traccia di questa risorsa come proiezione del cluster Kubernetes gestito dal cliente, non del cluster Kubernetes effettivo.
    * I metadati del cluster, come la versione Kubernetes, la versione dell'agente e il numero di nodi, vengono visualizzati nella risorsa Kubernetes abilitata per Azure Arc come metadati.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Scambio di dati tra ambienti cluster e Azure

| Tipo di dati | Scenario | Modalità di comunicazione |
| --------- | -------- | ------------------ |
| Versione del cluster Kubernetes | Metadati del cluster | Push dell'agente in Azure |
| Numero di nodi nel cluster | Metadati del cluster | Push dell'agente in Azure |
| Versione agente | Metadati del cluster | Push dell'agente in Azure |
| Tipo di distribuzione Kubernetes | Metadati del cluster | Push dell'interfaccia della riga di comando di Azure in Azure |
| Tipo di infrastruttura (AWS/GCP/vSphere/...) | Metadati del cluster | Push dell'interfaccia della riga di comando di Azure in Azure |
| vCPU numero di nodi nel cluster | Fatturazione | Push dell'interfaccia della riga di comando di Azure in Azure |
| Heartbeat agente | Integrità delle risorse | Push dell'agente in Azure |
| Consumo di risorse (memoria/CPU) per agenti | Diagnostica e supporto tecnico | Push dell'agente in Azure |
| Log di tutti i contenitori degli agenti | Diagnostica e supporto tecnico | Push dell'agente in Azure |
| Disponibilità aggiornamenti agente | Aggiornamento dell'agente | Pull di Agent da Azure |
| Stato desiderato della configurazione: URL del repository git, parametri dell'operatore Flux, chiave privata, contenuto host noto, nome utente HTTPS, token o password | Configurazione | Pull di Agent da Azure |
| Stato dell'installazione dell'operatore Flux | Configurazione | Push dell'agente in Azure |
| Assegnazioni di criteri di Azure che richiedono l'imposizione del gatekeeper nel cluster | Criteri di Azure | Pull di Agent da Azure |
| Stato di controllo e conformità delle imposte dei criteri in cluster | Criteri di Azure | Push dell'agente in Azure |
| Metriche e log dei carichi di lavoro dei clienti | Monitoraggio di Azure | L'agente effettua il push a Log Analytics risorsa dell'area di lavoro nel tenant e nella sottoscrizione del cliente |

## <a name="connectivity-status"></a>Stato della connettività

| Stato | Descrizione |
| ------ | ----------- |
| Connecting | La risorsa Kubernetes abilitata per Azure Arc viene creata in Azure Resource Manager, ma il servizio non ha ancora ricevuto l'heartbeat dell'agente. |
| Connesso | Il servizio Kubernetes abilitato per Azure Arc ha ricevuto un heartbeat degli agenti entro i 15 minuti precedenti. |
| Offline | La risorsa Kubernetes abilitata per Azure Arc è stata precedentemente connessa, ma il servizio non ha ricevuto heartbeat agente per 15 minuti. |
| Scaduto | Il certificato MSI ha una finestra di scadenza di 90 giorni dopo l'emissione. Una volta scaduto il certificato, la risorsa viene considerata `Expired` e tutte le funzionalità, ad esempio configurazione, monitoraggio e criteri smettono di funzionare in questo cluster. Altre informazioni su come gestire le risorse Kubernetes di Azure Arc scadute sono disponibili [nell'articolo delle domande frequenti](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Informazioni sulle modalità di connettività

| Modalità di connessione | Descrizione |
| ----------------- | ----------- |
| Connessione completa | Gli agenti possono comunicare in modo coerente con Azure con scarso ritardo nella propagazione delle configurazioni GitOps, nell'applicazione dei criteri di criteri di Azure e Gatekeeper e nella raccolta di metriche e log del carico di lavoro in monitoraggio di Azure. |
| Semi-connesso | Il certificato MSI Estratto dal `clusteridentityoperator` è valido per un massimo di 90 giorni prima della scadenza del certificato. Alla scadenza, la risorsa Kubernetes abilitata per Azure Arc smette di funzionare. Per riattivare tutte le funzionalità di Azure Arc nel cluster, eliminare e ricreare la risorsa e gli agenti Kubernetes abilitati per Azure Arc. Durante 90 giorni, connettere il cluster almeno una volta ogni 30 giorni. |
| Disconnesso | I cluster Kubernetes in ambienti disconnessi non possono accedere ad Azure attualmente non supportati da Azure Arc Enabled Kubernetes. Se questa funzionalità è di interesse, inviare o votare un'idea nel forum di UserVoice di [Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster ad Azure Arc](./connect-cluster.md)
* [Panoramica concettuale delle configurazioni](./conceptual-configurations.md)