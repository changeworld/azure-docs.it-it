---
title: Architettura dell'agente Kubernetes abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica dell'architettura degli agenti Kubernetes abilitati per Azure Arc
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561672"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architettura dell'agente Kubernetes abilitato per Azure Arc

[Kubernetes](https://kubernetes.io/) può essere usato per distribuire carichi di lavoro in contenitori in ambienti ibridi e multicloud in modo coerente. Azure Arc Enabled Kubernetes può essere usato come piano di controllo centralizzato per gestire in modo coerente criteri, governance e sicurezza in questi ambienti eterogeneo. Questo articolo include:

* Panoramica dell'architettura della connessione di un cluster ad Azure Arc.
* Modello di connettività seguito dagli agenti.
* Una descrizione dei dati scambiati tra l'ambiente cluster e Azure.

## <a name="deploy-agents-to-your-cluster"></a>Distribuire gli agenti nel cluster

La maggior parte dei data center locali applica regole di rete rigide che impediscono la comunicazione in ingresso sul firewall usato al limite di rete. Il Kubernetes abilitato per Azure Arc funziona con queste restrizioni abilitando solo gli endpoint in uscita selettiva per le comunicazioni in uscita e senza richiedere porte in ingresso nel firewall. Gli agenti Kubernetes abilitati per Azure Arc avviano le connessioni in uscita.

![Panoramica dell'architettura](./media/architectural-overview.png)

Connettere un cluster ad Azure Arc attenendosi alla procedura seguente:

1. Creare un cluster Kubernetes nell'infrastruttura scelta (VMware vSphere, Amazon Web Services, Google Cloud Platform e così via). 

    > [!NOTE]
    > I clienti sono tenuti a creare e gestire il ciclo di vita del cluster Kubernetes in quanto Azure Arc Enabled Kubernetes attualmente supporta solo il fissaggio di cluster Kubernetes esistenti ad Azure Arc.  

1. Avviare la registrazione di Azure Arc per il cluster usando l'interfaccia della riga di comando di Azure.
    * L'interfaccia della riga di comando di Azure usa Helm per distribuire il grafico Helm agente nel cluster.
    * I nodi del cluster avviano una comunicazione in uscita con il [container Registry Microsoft](https://github.com/microsoft/containerregistry) ed effettuano il pull delle immagini necessarie per creare gli agenti seguenti nello `azure-arc` spazio dei nomi:

        | Agente | Descrizione |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc Enabled Kubernetes supporta attualmente solo le [identità assegnate dal sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator rende la prima comunicazione in uscita necessaria per recuperare il certificato dell'identità del servizio gestito usato da altri agenti per la comunicazione con Azure. |
        | `deployment.apps/config-agent` | Controlla il cluster connesso per le risorse di configurazione del controllo del codice sorgente applicate al cluster e aggiorna lo stato di conformità |
        | `deployment.apps/controller-manager` | Operatore di operatori che orchestra le interazioni tra i componenti di Azure Arc |    
        | `deployment.apps/metrics-agent` | Raccoglie le metriche di altri agenti di arco per assicurarsi che questi agenti mostrino prestazioni ottimali |
        | `deployment.apps/cluster-metadata-operator` | Raccoglie i metadati del cluster, la versione del cluster, il numero di nodi e la versione dell'agente di Azure Arc |
        | `deployment.apps/resource-sync-agent` | Sincronizza i metadati del cluster sopra indicati in Azure |
        | `deployment.apps/flux-logs-agent` | Raccoglie i log degli operatori Flux distribuiti come parte della configurazione del controllo del codice sorgente |
    
1. Dopo che tutti i pod dell'agente Kubernetes sono stati abilitati in Azure Arc in `Running` stato, verificare che il cluster sia connesso ad Azure Arc. Verrà visualizzato quanto segue:
    * Una risorsa Kubernetes abilitata per Azure Arc in [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Questa risorsa viene registrata in Azure come proiezione del cluster Kubernetes gestito dal cliente, non del cluster Kubernetes effettivo.
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
| Stato desiderato della configurazione-URL del repository git, parametri dell'operatore Flux, chiave privata, contenuto host noto, nome utente HTTPS, token/password | Configurazione | Pull di Agent da Azure |
| Stato dell'installazione dell'operatore Flux | Configurazione | Push dell'agente in Azure |
| Assegnazioni di criteri di Azure che richiedono l'imposizione del gatekeeper nel cluster | Criteri di Azure | Pull di Agent da Azure |
| Stato di controllo e conformità delle imposte dei criteri in cluster | Criteri di Azure | Push dell'agente in Azure |
| Metriche e log dei carichi di lavoro dei clienti | Monitoraggio di Azure | L'agente effettua il push a Log Analytics risorsa dell'area di lavoro nel tenant e nella sottoscrizione del cliente |

## <a name="connectivity-status"></a>Stato della connettività

| Stato | Descrizione |
| ------ | ----------- |
| Connecting | La risorsa Kubernetes abilitata per Azure Arc è stata creata in Azure Resource Manager, ma il servizio non ha ancora ricevuto l'heartbeat dell'agente. |
| Connesso | Il servizio Kubernetes abilitato per Azure Arc ha ricevuto un heartbeat degli agenti entro i 15 minuti precedenti. |
| Offline | La risorsa Kubernetes abilitata per Azure Arc è stata precedentemente connessa, ma il servizio non ha ricevuto heartbeat agente per 15 minuti. |
| Scaduto | Il certificato dell'identità del servizio gestito (MSI) ha una finestra di scadenza di 90 giorni dopo l'emissione. Una volta scaduto il certificato, la risorsa viene considerata `Expired` e tutte le funzionalità, ad esempio configurazione, monitoraggio e criteri smettono di funzionare in questo cluster. Altre informazioni su come indirizzare le risorse Kubernetes scadute abilitate per Azure Arc sono disponibili [qui](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Informazioni sulle modalità di connettività

| Modalità di connessione | Descrizione |
| ----------------- | ----------- |
| Connessione completa | Gli agenti sono sempre in grado di raggiungere Azure. L'esperienza è ideale in questo caso, in quanto vi è un piccolo ritardo nella propagazione delle configurazioni (per GitOps), l'applicazione dei criteri (in criteri e Gatekeeper di Azure) e la raccolta di metriche e log dei carichi di lavoro (in monitoraggio di Azure) |
| Semi-connesso | Il certificato MSI Estratto dal `clusteridentityoperator` è valido per un massimo di 90 giorni prima della scadenza del certificato. Una volta scaduto il certificato, la risorsa Kubernetes abilitata per Azure Arc smette di funzionare. Eliminare e ricreare la risorsa e gli agenti Kubernetes abilitati per Azure Arc per ottenere tutte le funzionalità di Arc da usare nel cluster. Durante i 90 giorni, si consiglia agli utenti di connettere il cluster almeno una volta ogni 30 giorni. |
| Disconnesso | I cluster Kubernetes in ambienti disconnessi senza accesso ad Azure non sono attualmente supportati da Azure Arc Enabled Kubernetes. Se questa funzionalità è di interesse, inviare o votare un'idea nel forum di UserVoice di [Azure Arc](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster ad Azure Arc](./connect-cluster.md)
* [Panoramica concettuale delle configurazioni](./conceptual-configurations.md)