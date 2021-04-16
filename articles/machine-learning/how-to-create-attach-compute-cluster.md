---
title: Creazione di cluster di elaborazione
titleSuffix: Azure Machine Learning
description: Informazioni su come creare cluster di calcolo nell'area Azure Machine Learning lavoro. Usare il cluster di calcolo come destinazione di calcolo per il training o l'inferenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 2d23e073a43d61a501e93e0288f222ef26407744
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538240"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Creare un cluster di elaborazione di Azure Machine Learning

Informazioni su come creare e gestire un [cluster di calcolo nell'area](concept-compute-target.md#azure-machine-learning-compute-managed) Azure Machine Learning lavoro.

È possibile usare Azure Machine Learning di calcolo per distribuire un processo di training o inferenza batch in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](../virtual-machines/sizes-gpu.md). 

In questo articolo viene spiegato come:

* Creare un cluster di elaborazione
* Ridurre il costo del cluster di calcolo
* Configurare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per il cluster

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)

* [L'estensione dell'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando di Azure Machine Learning, Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)o [l'Azure Machine Learning Visual Studio Code.](tutorial-setup-vscode-extension.md)

* Se si usa Python SDK, [configurare l'ambiente di sviluppo con un'area di lavoro](how-to-configure-environment.md).  Dopo aver configurato l'ambiente, collegarsi all'area di lavoro nello script Python:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Che cos'è un cluster di calcolo?

Azure Machine Learning cluster di calcolo è un'infrastruttura di calcolo gestita che consente di creare facilmente un calcolo a nodo singolo o a più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

I cluster di calcolo possono eseguire processi in modo sicuro in un ambiente [di rete virtuale,](how-to-secure-training-vnet.md)senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori ed esegue il pacchetto delle dipendenze del modello in un contenitore Docker. 

## <a name="limitations"></a>Limitazioni

* Alcuni degli scenari elencati in questo documento sono contrassegnati come __anteprima.__ La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Attualmente sono supportate solo la creazione (e non l'aggiornamento) di cluster tramite modelli di Gestione risorse di Microsoft Windows [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. Per aggiornare il calcolo, per il momento è consigliabile usare l'SDK, l'interfaccia della riga di comando o l'esperienza utente.

* Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](how-to-manage-quotas.md).

* Azure consente di inserire _blocchi sulle_ risorse, in modo che non sia possibile eliminare o essere di sola lettura. __Non applicare blocchi di risorse al gruppo di risorse che contiene l'area di lavoro.__ L'applicazione di un blocco al gruppo di risorse che contiene l'area di lavoro impedirà le operazioni di ridimensionamento per i cluster di calcolo di Azure ML. Per altre informazioni sul blocco delle risorse, vedere [Bloccare le risorse per impedire modifiche impreviste.](../azure-resource-manager/management/lock-resources.md)

> [!TIP]
> I cluster possono in genere essere ridimensionati fino a 100 nodi, purché si disponga di una quota sufficiente per il numero di core necessari. Per impostazione predefinita, i cluster sono impostati con la comunicazione tra i nodi abilitata, ad esempio per supportare i processi MPI. È tuttavia possibile ridimensionare i cluster a 1000 nodi semplicemente generando un [ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto e richiedendo di consentire l'elenco della sottoscrizione o dell'area di lavoro o un cluster specifico per disabilitare la comunicazione tra nodi.


## <a name="create"></a>Crea

**Tempo stimato:** circa 5 minuti.

Un ambiente di calcolo di Azure Machine Learning può essere usato su più esecuzioni. L'ambiente di calcolo può essere condiviso con altri utenti nell'area di lavoro e mantenuto da un'esecuzione all'altra, ridimensionando automaticamente i nodi in base al numero di esecuzioni inviate e all'impostazione max_nodes definita nel cluster. L'impostazione min_nodes controlla il numero minimo di nodi disponibili.

I core dedicati per area per ogni quota della famiglia di macchine virtuali e la quota totale a livello di area, che si applica alla creazione del cluster di calcolo, sono unificati e condivisi con la quota dell'istanza di calcolo di training Azure Machine Learning. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Il calcolo viene ridimensionato automaticamente fino a zero nodi quando non viene usato.   Le macchine virtuali dedicate vengono create per eseguire i processi in base alle esigenze.
    
# <a name="python"></a>[Python](#tab/python)


Per creare una risorsa permanente di ambiente di calcolo di Machine Learning in Python, specificare le proprietà **vm_size** e **max_nodes**. Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà.
    
* **vm_size**: la famiglia di macchine virtuali dei nodi creati dall'ambiente di calcolo di Azure Machine Learning.
* **max_nodes**: il numero massimo di nodi per la scalabilità automatica durante l'esecuzione di un processo in un ambiente di calcolo di Machine Learning.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Durante la creazione di un ambiente di calcolo di Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente di dimensione fissa o all'interno di una Rete virtuale di Azure esistente nella sottoscrizione.  Per informazioni dettagliate, consultare [AmlCompute class](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) (Classe AmlCompute).


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Per altre informazioni, vedere [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Per informazioni sulla creazione di un cluster di calcolo in Studio, vedere [Creare destinazioni di calcolo in studio di Azure Machine Learning](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Ridurre i costi del cluster di calcolo

È anche possibile scegliere di usare [macchine virtuali con priorità bassa per](concept-plan-manage-cost.md#low-pri-vm) eseguire alcuni o tutti i carichi di lavoro. Queste macchine virtuali non hanno una disponibilità garantita e possono essere terminate durante l'uso. Sarà necessario riavviare un processo preempted. 

Usare uno dei modi seguenti per specificare una macchina virtuale con priorità bassa:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Impostare `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

In Studio scegliere **Priorità bassa quando** si crea una macchina virtuale.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurare l'identità gestita

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Configurare l'identità gestita nella configurazione del provisioning:  

    * Identità gestita assegnata dal sistema creata in un'area di lavoro denominata `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Identità gestita assegnata dall'utente creata in un'area di lavoro denominata `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Aggiungere un'identità gestita a un cluster di calcolo esistente denominato `cpu_cluster`
    
    * Identità gestita assegnata dal sistema:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Identità gestita assegnata dall'utente:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

* Creare un nuovo cluster di calcolo gestito con identità gestita

  * Identità gestita assegnata dall'utente

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Identità gestita assegnata dal sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Aggiungere un'identità gestita a un cluster esistente:

    * Identità gestita assegnata dall'utente
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Identità gestita assegnata dal sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Vedere [Configurare l'identità gestita in Studio.](how-to-create-attach-compute-studio.md#managed-identity)

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Utilizzo delle identità gestite

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

È possibile che alcuni utenti che hanno creato l'area di lavoro Azure Machine Learning dall'portale di Azure prima della versione di Ga non siano in grado di creare AmlCompute in tale area di lavoro. È possibile generare una richiesta di supporto per il servizio o creare una nuova area di lavoro tramite il portale o l'SDK per sbloccarsi immediatamente.

Se il cluster Azure Machine Learning di calcolo viene bloccato al ridimensionamento (0 -> 0) per lo stato del nodo, ciò potrebbe essere causato dai blocchi delle risorse di Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Passaggi successivi

Usare il cluster di calcolo per:

* [Inviare un'esecuzione di training](how-to-set-up-training-targets.md) 
* [Eseguire l'inferenza batch](./tutorial-pipeline-batch-scoring-classification.md).
