---
title: Domande frequenti sul supporto dei nodi riservati in Azure Kubernetes Service (AKS)
description: Trovare le risposte ad alcune domande comuni sul servizio Azure Kubernetes Service (AKS) & supporto per i nodi di Azure Confidential Computing (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653398"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Domande frequenti sui nodi di elaborazione riservati in Azure Kubernetes Service (AKS)

Questo articolo illustra le domande frequenti sui nodi di elaborazione riservati basati su Intel SGX nel servizio Azure Kubernetes. Per eventuali altre domande, inviare un messaggio di posta elettronica **acconaks@microsoft.com** .

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>I nodi di elaborazione riservati su AKS sono disponibili in GA? ###
Sì

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Che cos'è l'attestazione e in che modo è possibile eseguire l'attestazione di app in esecuzione in enclave? ###
L'attestazione è il processo di dimostrazione e convalida della creazione di un'istanza corretta di un componente software sulla piattaforma hardware specifica. Assicura inoltre che l'evidenza sia verificabile per garantire che sia in esecuzione in una piattaforma sicura e non sia stata manomessa. [Altre](attestation.md) informazioni sul modo in cui viene eseguita l'attestazione per le app enclave.

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>È possibile abilitare la rete accelerata con i cluster AKS di Azure Confidential computing? ###
No. La funzionalità rete accelerata non è supportata nelle macchine virtuali DCSv2 che comunicano nodi di elaborazione riservati in AKS. 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>È possibile portare le applicazioni esistenti in contenitori ed eseguirle su AKS con Azure Confidential computing? ###
Sì, per altre informazioni sugli abilitatori della piattaforma, vedere la [pagina dei contenitori riservati](confidential-containers.md) .

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>Quale versione della versione del driver Intel SGX è nell'immagine AKS per i nodi riservati? ### 
Attualmente, le macchine virtuali DCSv2 di Azure Confidential computing vengono installate con Intel SGX DCAP 1,33. 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>È possibile inserire script post-installazione/personalizzare i driver nei nodi di cui è stato effettuato il provisioning da AKS? ###
No. I [nodi di elaborazione confidenziale basati sul motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) supportano nodi di elaborazione riservati che consentono installazioni personalizzate e hanno il controllo completo sul piano di controllo Kubernetes.
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>È consigliabile usare un'immagine di base Docker per iniziare a usare le applicazioni enclave? ###
Vari abilitatori (ISV e progetti OSS) forniscono modi per abilitare i contenitori riservati. Esaminare la [pagina dei contenitori riservati](confidential-containers.md) per altri dettagli e riferimenti singoli alle implementazioni.

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>È possibile eseguire I nodi ACC con altri SKU standard AKS (creare un cluster di pool di nodi eterogeneo)? ###

Sì, è possibile eseguire pool di nodi diversi all'interno dello stesso cluster AKS, inclusi i nodi ACC. Per definire come destinazione le applicazioni enclave in un pool di nodi specifico, è consigliabile aggiungere i selettori di nodo o applicare i limiti EPC. Per informazioni dettagliate sulla Guida introduttiva sui nodi riservati, vedere [qui](confidential-nodes-aks-get-started.md).

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>È possibile eseguire I nodi Windows e I contenitori di Windows con ACC? ###
Attualmente non è possibile. Contattare il team del prodotto all'indirizzo *acconaks@microsoft.com* se è necessario disporre di nodi Windows o di contenitori. 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Cosa accade se la dimensione del contenitore è superiore alla memoria EPC disponibile? ###
La memoria EPC si applica alla parte dell'applicazione programmata per l'esecuzione nell'enclave. La dimensione totale del contenitore non è il modo giusto per confrontarla con il numero massimo di memoria EPC disponibile. Infatti, i computer DCSv2 con SGX consentono la massima quantità di memoria VM di 32 GB in cui verrebbe utilizzata la parte non attendibile dell'applicazione. Tuttavia, se il contenitore consuma più della memoria EPC disponibile, le prestazioni della parte del programma in esecuzione nell'enclave potrebbero essere interessate.

Per una migliore gestione della memoria EPC nei nodi di lavoro, prendere in considerazione la gestione dei limiti basata sulla memoria EPC tramite Kubernetes. Seguire l'esempio riportato di seguito come riferimento.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Cosa accade se l'enclave consuma più della quantità massima di memoria EPC disponibile? ###

Il totale della memoria EPC disponibile viene condiviso tra le applicazioni enclave nelle stesse VM o nei nodi di lavoro. Se l'applicazione usa una memoria di EPC superiore a quella disponibile, le prestazioni dell'applicazione potrebbero essere influenzate. Per questo motivo, è consigliabile impostare tolleranza per applicazione nel file YAML di distribuzione per gestire meglio la memoria EPC disponibile per ogni nodo di lavoro, come illustrato negli esempi precedenti. In alternativa, è sempre possibile scegliere di spostarsi verso l'alto nelle dimensioni della macchina virtuale del pool di nodi di lavoro o aggiungere altri nodi. 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Perché non è possibile eseguire fork () ed Exec per eseguire più processi nell'applicazione enclave? ###

Attualmente, le macchine virtuali dello SKU DCsv2 di Azure Confidential computing supportano un singolo spazio di indirizzi per il programma in esecuzione in un'enclave. Il processo singolo è una limitazione attuale progettata per garantire una sicurezza elevata. Tuttavia, gli abilitatori di contenitori riservati possono avere implementazioni alternative per ovviare a questa limitazione.
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>Installare automaticamente eventuali daemonset aggiuntivi per esporre i driver SGX? ###

Sì. Il nome di daemonset è SGX-Device-Plugin. Altre informazioni sui rispettivi scopi sono disponibili [qui](confidential-nodes-aks-overview.md).  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Qual è lo SKU della macchina virtuale da scegliere per i nodi di elaborazione riservati? ###

SKU DCSv2. Gli [SKU DCSv2](../virtual-machines/dcv2-series.md) sono disponibili nelle [aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>È possibile pianificare ed eseguire I contenitori non enclave nei nodi di elaborazione riservati? ###

Sì. Le macchine virtuali hanno anche una memoria regolare in grado di eseguire carichi di lavoro di contenitori standard. Prendere in considerazione il modello di sicurezza e di minaccia delle applicazioni prima di decidere nei modelli di distribuzione.
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>È possibile eseguire il provisioning di AKS con pool di nodi DCSv2 tramite portale di Azure? ###

Sì. L'interfaccia della riga di comando di Azure può essere usata anche come alternativa, come descritto [qui](confidential-nodes-aks-get-started.md).

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Quali sono le versioni di Ubuntu e la generazione di VM supportate? ###
18,04 sulla generazione 2. 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>È possibile modificare la versione corrente di Intel SGX DCAP Diver su AKS? ###

No. Per eseguire le installazioni personalizzate, è consigliabile scegliere distribuzioni di nodi del ruolo di [lavoro del motore di gestione](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) dei problemi. 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Quale versione di Kubernetes è supportata e consigliata? ###

Sono supportate e consigliate Kubernetes versione 1,16 e successive. 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>Quali sono le limitazioni note correnti del prodotto? ###

- Supporta solo i nodi VM Ubuntu 18,04 gen 2 
- Nessun supporto dei nodi Windows o supporto per i contenitori di Windows
- La scalabilità automatica del Pod orizzontale basata sulla memoria EPC non è supportata. La CPU e il ridimensionamento normale basato sulla memoria sono supportati.
- Gli spazi di sviluppo in AKS per le app riservate non sono attualmente supportati

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>Verranno caricate solo le immagini firmate e attendibili nell'enclave per l'informatica riservata? ###
Non in modo nativo durante l'inizializzazione dell'enclave ma sì tramite la firma del processo di attestazione può essere convalidata. Ref [qui](../attestation/basic-concepts.md#benefits-of-policy-signing). 

### <a name="next-steps"></a>Passaggi successivi
Per altri dettagli sui contenitori riservati, vedere la [pagina dei contenitori riservati](confidential-containers.md) .
