---
title: Supporto dell'attestazione con l'helper di citazione Intel SGX DaemonSet in Azure (anteprima)
description: DaemonSet per la generazione dell'offerta al di fuori del processo dell'applicazione Intel SGX. Questo articolo illustra come viene fornita la funzionalità di attestazione out-of-process per carichi di lavoro riservati eseguiti all'interno di un contenitore.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484405"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Gestione del software della piattaforma con l'helper di citazione Intel SGX DaemonSet (anteprima)

[Le applicazioni enclave che](confidential-computing-enclaves.md) eseguono l'attestazione remota richiedono un'offerta generata. Questa citazione fornisce una prova crittografica dell'identità e dello stato dell'applicazione, nonché l'ambiente in cui è in esecuzione l'enclave. La generazione dell'offerta richiede componenti software attendibili che fanno parte di Intel Platform Software Components (PSW).

## <a name="overview"></a>Panoramica
 
Intel supporta due modalità di attestazione per l'esecuzione della generazione di citazioni:

- *In-process ospita* i componenti software attendibili all'interno del processo dell'applicazione dell'enclave.

- *Out-of-process* ospita i componenti software attendibili all'esterno dell'applicazione enclave.
 
Per impostazione predefinita, le applicazioni Intel Software Guard Extension (Intel SGX) create con Open Enclave SDK usano la modalità di attestazione in-process. Le applicazioni basate su Intel SGX consentono la modalità di attestazione out-of-process. Se si vuole usare questa modalità, è necessario un hosting aggiuntivo ed è necessario esporre i componenti necessari, ad esempio Architectural Enclave Service Manager (AESM), esterni all'applicazione.

Questa funzionalità migliora il tempo di attività per le app enclave durante gli aggiornamenti della piattaforma Intel o gli aggiornamenti dei driver DCAP. Per questo motivo, è consigliabile usarlo.

Per abilitare questa funzionalità in un cluster del servizio Azure Kubernetes, aggiungere il comando all'interfaccia della riga di comando di Azure quando si abilita il componente aggiuntivo di confidential `--enable-sgxquotehelper` computing. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Per altre informazioni, vedere Avvio rapido: Distribuire un cluster del servizio [AzureKs con nodi di confidential computing usando](confidential-nodes-aks-get-started.md)l'interfaccia della riga di comando di Azure.

## <a name="benefits-of-the-out-of-process-mode"></a>Vantaggi della modalità out-of-process

L'elenco seguente descrive alcuni dei vantaggi principali di questa modalità di attestazione:

-   Non sono necessari aggiornamenti per i componenti di generazione delle citazioni di PSW per ogni applicazione in contenitori. I proprietari dei contenitori non devono gestire gli aggiornamenti all'interno del contenitore. I proprietari dei contenitori si basano invece sull'interfaccia del provider che richiama il servizio centralizzato all'esterno del contenitore. Il provider aggiorna e gestisce il contenitore.

-   Non è necessario preoccuparsi degli errori di attestazione dovuti a componenti PSW non aggiornati. Il provider gestisce gli aggiornamenti di questi componenti.

-   La modalità out-of-process offre un utilizzo migliore della memoria EPC rispetto alla modalità in-process. In modalità in-process, ogni applicazione enclave deve creare un'istanza della copia di QE e PCE per l'attestazione remota. In modalità out-of-process non è necessario che il contenitore ospiterà tali enclave e pertanto non usa la memoria dell'enclave dalla quota del contenitore.

-   Quando si esegue l'upstream del driver Intel SGX in un kernel Linux, è necessario che un enclave abbia privilegi più elevati. Questo privilegio consente all'enclave di richiamare PCE, che interromperà l'applicazione dell'enclave in esecuzione in modalità in-process. Per impostazione predefinita, le enclave non ottengono questa autorizzazione. Per concedere questo privilegio a un'applicazione enclave è necessario modificare il processo di installazione dell'applicazione. Al contrario, nella modalità out-of-process, il provider del servizio che gestisce le richieste out-of-process garantisce che il servizio sia installato con questo privilegio.

-   Non è necessario verificare la compatibilità con le versioni precedenti con PSW e DCAP. Questa compatibilità per gli aggiornamenti dei componenti di generazione delle citazioni viene verificata dal provider prima dell'aggiornamento. In questo modo è possibile gestire i problemi di compatibilità prima di distribuire gli aggiornamenti per carichi di lavoro riservati.

## <a name="confidential-workloads"></a>Carichi di lavoro riservati

Il richiedente di offerta e la generazione di offerte vengono eseguiti separatamente, ma nello stesso computer fisico. La generazione di offerte è centralizzata e serve le richieste di virgolette da tutte le entità. Per richiedere virgolette da parte di qualsiasi entità, l'interfaccia deve essere definita correttamente e individuabile.

![Diagramma che mostra le relazioni tra il richiedente dell'offerta, la generazione di offerte e l'interfaccia.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Questo modello astratto si applica allo scenario del carico di lavoro riservato, sfruttando il servizio AESM già disponibile. AESM viene in contenitori e distribuito come DaemonSet nel cluster Kubernetes. Kubernetes garantisce che una singola istanza di un contenitore del servizio AESM, di cui è stato eseguito il wrapping in un pod, sia distribuita in ogni nodo dell'agente. La nuova citazione di Intel SGX DaemonSet avrà una dipendenza dal DaemonSet sgx-device-plugin, perché il contenitore del servizio AESM richiede memoria EPC da sgx-device-plugin per l'avvio di enclave QE e PCE.

Ogni contenitore deve acconsentire esplicitamente all'uso della generazione di citazioni out-of-process impostando la variabile di ambiente durante `SGX_AESM_ADDR=1` la creazione. Il contenitore deve includere anche il pacchetto libsgx-quote-ex, responsabile di indirizzare la richiesta al socket di dominio Unix predefinito.

Un'applicazione può comunque usare l'attestazione in-process come in precedenza, ma in-process e out-of-process non possono essere usati contemporaneamente all'interno di un'applicazione. L'infrastruttura out-of-process è disponibile per impostazione predefinita e utilizza risorse.

## <a name="sample-implementation"></a>Implementazione di esempio

Il file Docker seguente è un esempio per un'applicazione basata su Open Enclave. Impostare la variabile di ambiente nel file Docker oppure `SGX_AESM_ADDR=1` impostarla nel file di distribuzione. L'esempio seguente fornisce informazioni dettagliate per il file Docker e la distribuzione. 

  > [!Note] 
  > Per il corretto funzionamento dell'attestazione out-of-process, libsgx-quote-ex di Intel deve essere in pacchetto nel contenitore dell'applicazione.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
In alternativa, è possibile impostare la modalità di attestazione out-of-process nel file yaml di distribuzione. Ecco come:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: Distribuire un cluster del servizio AzureKs con nodi di confidential computing usando l'interfaccia della riga di comando di Azure](./confidential-nodes-aks-get-started.md)

[Esempi di contenitori riservati](https://github.com/Azure-Samples/confidential-container-samples)

[SKU DCsv2](../virtual-machines/dcv2-series.md)
