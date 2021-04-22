---
title: Creare e gestire un'istanza di calcolo
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e gestire un'Azure Machine Learning di calcolo. Usare come ambiente di sviluppo o come destinazione di calcolo per scopi di sviluppo/test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861227"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Creare e gestire un'istanza Azure Machine Learning di calcolo

Informazioni su come creare e gestire un'istanza [di calcolo](concept-compute-instance.md) nell'area Azure Machine Learning lavoro.

Usare un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud. Per lo sviluppo e il test, è anche possibile usare l'istanza come destinazione di calcolo di [training](concept-compute-target.md#train) o come [destinazione di inferenza.](concept-compute-target.md#deploy)   Un'istanza di calcolo può eseguire più processi in parallelo e ha una coda di processi. Come ambiente di sviluppo, un'istanza di calcolo non può essere condivisa con altri utenti nell'area di lavoro.

In questo articolo vengono illustrate le operazioni seguenti:

* Creare un'istanza di ambiente di calcolo 
* Gestire (avviare, arrestare, riavviare, eliminare) un'istanza di calcolo
* Accedere alla finestra del terminale 
* Installare pacchetti R o Python
* Creare nuovi ambienti o kernel Jupyter

Le istanze di calcolo possono eseguire processi in modo sicuro in un ambiente [di rete virtuale,](how-to-secure-training-vnet.md)senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori e comballa le dipendenze del modello in un contenitore Docker. 

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area Azure Machine Learning lavoro.](how-to-manage-workspace.md)

* [L'estensione dell'interfaccia della riga Machine Learning azure per il](reference-azure-machine-learning-cli.md)servizio , Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)o l'estensione [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

## <a name="create"></a>Create

**Tempo stimato:** circa 5 minuti.

La creazione di un'istanza di calcolo è un processo una sola volta per l'area di lavoro. È possibile riutilizzare il calcolo come workstation di sviluppo o come destinazione di calcolo per il training. È possibile avere più istanze di calcolo collegate all'area di lavoro.

I core dedicati per area per ogni quota della famiglia di macchine virtuali e la quota regionale totale, che si applica alla creazione dell'istanza di calcolo, sono unificati e condivisi con la quota del cluster di calcolo di training Azure Machine Learning. L'arresto dell'istanza di calcolo non rilascia la quota per assicurarsi che sia possibile riavviare l'istanza di calcolo. Si noti che non è possibile modificare le dimensioni della macchina virtuale dell'istanza di calcolo dopo la creazione.

L'esempio seguente illustra come creare un'istanza di calcolo:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Per altre informazioni sulle classi, i metodi e i parametri usati in questo esempio, vedere i documenti di riferimento seguenti:

* [Classe ComputeInstance](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Per altre informazioni, vedere il riferimento [az ml computetarget create computeinstance.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Nell'area di lavoro studio di Azure Machine Learning creare una nuova istanza  di calcolo dalla sezione Calcolo o nella sezione **Notebook quando** si è pronti per eseguire uno dei notebook.

Per informazioni sulla creazione di un'istanza di calcolo in studio, vedere Creare destinazioni di [calcolo in studio di Azure Machine Learning](how-to-create-attach-compute-studio.md#compute-instance).

---

È anche possibile creare un'istanza di calcolo con un [Azure Resource Manager modello .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance) 

### <a name="create-on-behalf-of-preview"></a>Creare per conto di (anteprima)

L'amministratore può creare un'istanza di calcolo per conto di un data scientist e assegnare l'istanza con:
* [Azure Resource Manager modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Per informazioni dettagliate su come trovare tenantID e ObjectID necessari in questo modello, vedere Trovare gli ID oggetto [identità per la configurazione dell'autenticazione.](../healthcare-apis/fhir/find-identity-object-ids.md)  È anche possibile trovare questi valori nel portale Azure Active Directory dati.
* API REST

Il data scientist per cui si crea l'istanza di calcolo deve avere le autorizzazioni seguenti per il controllo degli accessi in base al ruolo di [Azure:](../role-based-access-control/overview.md) 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Il data scientist può avviare, arrestare e riavviare l'istanza di calcolo. Possono usare l'istanza di calcolo per:
* Jupyter
* JupyterLab
* RStudio
* Notebook integrati

## <a name="manage"></a>Gestire

Avviare, arrestare, riavviare ed eliminare un'istanza di calcolo. Un'istanza di calcolo non viene automaticamente ridotta, quindi assicurarsi di arrestare la risorsa per evitare addebiti continui.

> [!TIP]
> L'istanza di calcolo ha un disco del sistema operativo da 120 GB. Se lo spazio su disco è insufficiente, usare il [terminale](how-to-access-terminal.md) per cancellare almeno 1-2 GB prima di arrestare o riavviare l'istanza di calcolo.

# <a name="python"></a>[Python](#tab/python)

Negli esempi seguenti il nome dell'istanza di calcolo è **instance**

* Ottenere lo stato

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Interrompere

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Avvio

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Riavvio

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Delete

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Negli esempi seguenti il nome dell'istanza di calcolo è **instance**

* Interrompere

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Per altre informazioni, vedere [az ml computetarget stop computeinstance.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)

* Avvio 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Per altre informazioni, vedere [az ml computetarget start computeinstance.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)

* Riavvio 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Per altre informazioni, vedere [az ml computetarget restart computeinstance.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)

* Delete

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Per altre informazioni, vedere [az ml computetarget delete computeinstance.](/cli/azure/ml/computetarget#az_ml_computetarget_delete)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Nell'area di lavoro in Azure Machine Learning Studio selezionare **Calcolo**, quindi selezionare **Istanza di calcolo** nella parte superiore.

![Gestire un'istanza di calcolo](./media/concept-compute-instance/manage-compute-instance.png)

È possibile eseguire queste operazione:

* Creare una nuova istanza di calcolo 
* Aggiornare la scheda delle istanze di calcolo.
* Avviare, arrestare e riavviare un'istanza di calcolo.  L'istanza viene pagata ogni volta che è in esecuzione. Arrestare l'istanza di calcolo quando non viene in uso per ridurre i costi. L'arresto di un'istanza di calcolo la dealloca. e quindi riavviarla quando è necessario.
* Eliminare un'istanza di calcolo.
* Filtrare l'elenco di istanze di calcolo per visualizzare solo le istanze create.

Per ogni istanza di calcolo nell'area di lavoro creata (o creata automaticamente), è possibile:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* Accedere con SSH all'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. Nella scheda sono disponibili i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

---


[Il controllo degli accessi in](../role-based-access-control/overview.md) base al ruolo di Azure consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare, riavviare un'istanza di calcolo. Tutti gli utenti con il ruolo collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Tuttavia, solo l'autore di un'istanza di calcolo specifica o l'utente assegnato se è stata creata per loro conto può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. Un'istanza di calcolo è dedicata a un singolo utente che dispone dell'accesso radice e può accedere tramite Jupyter/JupyterLab/RStudio. L'istanza di calcolo avrà accesso a utente singolo e tutte le azioni useranno l'identità dell'utente per il controllo degli accessi in base al ruolo di Azure e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

Queste azioni possono essere controllate dal controllo degli accessi in base al ruolo di Azure:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Passaggi successivi

* [Accedere al terminale dell'istanza di calcolo](how-to-access-terminal.md)
* [Creare e gestire file](how-to-manage-files.md)
* [Inviare un'esecuzione di training](how-to-set-up-training-targets.md)
