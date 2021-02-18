---
title: Nodi di elaborazione riservati in Azure Kubernetes Service (AKS)
description: Nodi di confidential computing nel servizio Azure Kubernetes
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9ca98c032a7c8bd1820a92bff77079a61c515d65
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653381"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Nodi di elaborazione riservati nel servizio Azure Kubernetes

Il [confidential computing di Azure](overview.md) consente di proteggere i dati sensibili durante l'uso. L'infrastruttura di elaborazione riservata sottostante protegge i dati da altre applicazioni, amministratori e provider di servizi cloud con un ambiente contenitore di esecuzione attendibile basato su hardware. L'aggiunta di nodi di elaborazione riservati consente di usare l'applicazione contenitore per l'esecuzione in un ambiente isolato, protetto da hardware e attestabile.

## <a name="overview"></a>Panoramica

Il servizio Azure Kubernetes supporta l'aggiunta di [nodi di confidential computing DCsv2](confidential-computing-enclaves.md) con tecnologia Intel SGX. Questi nodi consentono di eseguire carichi di lavoro sensibili all'interno di un ambiente di esecuzione attendibile basato su hardware. I TEE consentono al codice a livello di utente da contenitori di allocare aree private di memoria per eseguire il codice direttamente con la CPU. Queste aree di memoria privata eseguite direttamente con CPU sono denominate enclave. Le enclave consentono di proteggere la riservatezza dei dati, l'integrità dei dati e l'integrità del codice da altri processi in esecuzione negli stessi nodi. Il modello di esecuzione di Intel SGX rimuove anche i livelli intermedi del sistema operativo guest, il sistema operativo host e l'hypervisor, riducendo così la superficie di attacco. Il modello di *esecuzione isolato per contenitore basato su hardware* in un nodo consente di eseguire direttamente le applicazioni con la CPU, mantenendo al contempo il blocco di memoria speciale crittografato per ogni contenitore. I nodi di elaborazione riservati con i contenitori riservati sono un'ottima aggiunta alla strategia di sicurezza e al contenitore per la difesa del trust zero.

![Panoramica dei nodi SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funzionalità dei nodi di confidential computing del servizio Azure Kubernetes

- Isolamento di contenitori a livello di hardware e di processo con Intel SGX Trusted Execution Environment (TEE) 
- Cluster di pool di nodi eterogenei (una combinazione di pool di nodi di confidential computing e non)
- Pianificazione di Pod basati sulla memoria (EPC) crittografata (richiede il componente aggiuntivo)
- Driver DCAP Intel SGX pre-installato
- Scalabilità automatica basata sul consumo della CPU e scalabilità automatica del cluster
- Supporto dei contenitori Linux tramite i nodi di lavoro di VM Ubuntu 18.04 Gen 2

## <a name="confidential-computing-add-on-for-aks"></a>Componente aggiuntivo Confidential computing per AKS
La funzionalità componente aggiuntivo Abilita funzionalità aggiuntive su AKS quando si eseguono pool di nodi di calcolo riservati nel cluster. Questo componente aggiuntivo Abilita le funzionalità seguenti.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Plug-in del dispositivo Azure per Intel SGX <a id="sgx-plugin"></a>

Il plug-in del dispositivo implementa l'interfaccia del plug-in del dispositivo Kubernetes per la memoria della cache di pagina (EPC) crittografata ed espone i driver di dispositivo dai nodi. In effetti, questo plug-in rende la memoria EPC come un altro tipo di risorsa in Kubernetes. Gli utenti possono specificare dei limiti per questa risorsa esattamente come per altre risorse. Oltre alla funzione di pianificazione, il plug-in del dispositivo consente di assegnare le autorizzazioni dei driver di dispositivo Intel SGX ai contenitori riservati del carico di lavoro. Con questo plug-in lo sviluppatore può evitare di montare i volumi dei driver Intel SGX nei file di distribuzione. Un'implementazione di esempio della distribuzione basata sulla memoria EPC (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) è disponibile [qui](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modelli di programmazione

### <a name="confidential-containers"></a>Contenitori riservati

I [contenitori riservati](confidential-containers.md) consentono di eseguire le applicazioni contenitore non modificate esistenti dei runtime di **linguaggi di programmazione più comuni** (Python, node, Java e così via) in modo riservato. Questo modello di creazione pacchetti non richiede alcuna modifica o ricompilazione del codice sorgente. Questo è il metodo più veloce per la riservatezza che è possibile ottenere mediante la creazione di pacchetti di contenitori Docker standard con Open-Source progetti o soluzioni di Azure partner. In questo modello di creazione di pacchetti e di esecuzione tutte le parti dell'applicazione contenitore vengono caricate nel limite attendibile (enclave). Questo modello funziona bene per le applicazioni contenitore a scaffale disponibili sul mercato o sulle app personalizzate attualmente in esecuzione nei nodi per utilizzo generico.

### <a name="enclave-aware-containers"></a>Contenitori con riconoscimento dell'enclave
I nodi di elaborazione riservati su AKS supportano anche i contenitori programmati per l'esecuzione in un'enclave per utilizzare un **set di istruzioni speciale** disponibile dalla CPU. Questo modello di programmazione consente un controllo più rigoroso del flusso di esecuzione e richiede l'uso di SDK e Framework speciali. Questo modello di programmazione fornisce la maggior parte del controllo del flusso dell'applicazione con una base di calcolo attendibile più bassa (TCB). Lo sviluppo di contenitori con compatibilità con l'enclave comporta parti non attendibili e attendibili per l'applicazione contenitore, consentendo così di gestire la memoria normale e la memoria della cache delle pagine crittografata in cui viene eseguita l'enclave. Altre informazioni sui contenitori con riconoscimento dell'enclave sono disponibili [qui](enclave-aware-containers.md).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un cluster del servizio Azure Kubernetes con nodi di confidential computing](./confidential-nodes-aks-get-started.md)

[Esempi di contenitori riservati](https://github.com/Azure-Samples/confidential-container-samples)

[Elenco di SKU DCsv2](../virtual-machines/dcv2-series.md)

[Sessione di webinar sulla difesa in profondità con i contenitori riservati](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
