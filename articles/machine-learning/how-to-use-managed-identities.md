---
title: Usare identità gestite per il controllo di accesso (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come usare le identità gestite per controllare l'accesso alle risorse di Azure dall'area di lavoro Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a7efd57100ad89fa9824b7a635e11698515e13ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521017"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Usare identità gestite con Azure Machine Learning (anteprima)

Le [identità gestite](../active-directory/managed-identities-azure-resources/overview.md) consentono di configurare l'area di lavoro con le *autorizzazioni minime necessarie per accedere alle risorse*. 

Quando si configura Azure Machine Learning area di lavoro in modo affidabile, è importante assicurarsi che i diversi servizi associati all'area di lavoro dispongano del livello di accesso corretto. Ad esempio, durante il flusso di lavoro di Machine Learning l'area di lavoro deve accedere ad Azure Container Registry (ACR) per le immagini Docker e gli account di archiviazione per i dati di training. 

Inoltre, le identità gestite consentono un controllo con granularità fine sulle autorizzazioni, ad esempio è possibile concedere o revocare l'accesso da risorse di calcolo specifiche a un record di controllo di accesso specifico.

In questo articolo si apprenderà come usare le identità gestite per:

 * Configurare e usare ACR per l'area di lavoro di Azure Machine Learning senza che sia necessario abilitare l'accesso utente amministratore a ACR.
 * Accedere a un ACR privato esterno all'area di lavoro, per eseguire il pull delle immagini di base per il training o l'inferenza.
 * Creare un'area di lavoro con identità gestita assegnata dall'utente per accedere alle risorse associate.

> [!IMPORTANT]
> L'uso delle identità gestite per controllare l'accesso alle risorse con Azure Machine Learning è attualmente in fase di anteprima. La funzionalità di anteprima viene fornita "così com'è", senza alcuna garanzia del supporto o del contratto di servizio. Per ulteriori informazioni, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
- Estensione dell'interfaccia della riga [di comando di Azure per servizio Machine Learning](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).
- Per assegnare i ruoli, è necessario che l'account di accesso per la sottoscrizione di Azure disponga del ruolo di [operatore di identità gestito](../role-based-access-control/built-in-roles.md#managed-identity-operator) o di un altro ruolo che conceda le azioni necessarie, ad esempio __owner__.
- È necessario avere familiarità con la creazione e l'utilizzo di [identità gestite](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="configure-managed-identities"></a>Configurare le identità gestite

In alcune situazioni è necessario impedire l'accesso utente amministratore ad Azure Container Registry. Ad esempio, l'ACR può essere condiviso ed è necessario impedire l'accesso amministratore da parte di altri utenti. In alternativa, la creazione di ACR con l'utente amministratore abilitato non è consentita da un criterio a livello di sottoscrizione.

> [!IMPORTANT]
> Quando si usa Azure Machine Learning per l'inferenza nell'istanza di contenitore di Azure (ACI), è __necessario__ l'accesso utente amministratore su ACR. Non disabilitarlo se si prevede di distribuire i modelli in ACI per l'inferenza.

Quando si crea ACR senza consentire l'accesso utente amministratore, le identità gestite vengono usate per accedere a ACR per compilare ed eseguire il pull delle immagini docker.

Quando si crea l'area di lavoro, è possibile portare il proprio ACR con l'utente amministratore disattivato. In alternativa, Azure Machine Learning creare l'area di lavoro ACR e disabilitare l'utente amministratore in un secondo momento.

### <a name="bring-your-own-acr"></a>Bring your own ACR

Se l'utente amministratore di ACR non è consentito dai criteri di sottoscrizione, è necessario prima creare ACR senza utente amministratore e quindi associarlo all'area di lavoro. Inoltre, se si dispone di ACR esistente con l'utente amministratore disabilitato, è possibile collegarlo all'area di lavoro.

[Creare ACR dall'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md) senza impostare l' ```--admin-enabled``` argomento o da portale di Azure senza abilitare l'utente amministratore. Quindi, quando si crea Azure Machine Learning area di lavoro, specificare l'ID risorsa di Azure del record di verifica. L'esempio seguente illustra la creazione di una nuova area di lavoro di Azure ML che usa un record di registro esistente:

> [!TIP]
> Per ottenere il valore per il `--container-registry` parametro, usare il comando [AZ ACR Show](/cli/azure/acr#az_acr_show) per visualizzare le informazioni per il record di controllo di stato. Il `id` campo contiene l'ID risorsa per il record di registro di sistema.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Consentire a Azure Machine Learning servizio di creare un'area di lavoro ACR

Se non si usa il proprio ACR, Azure Machine Learning servizio ne creerà uno quando si esegue un'operazione che ne richiede una. Inviare, ad esempio, un'esecuzione di training a ambiente di calcolo di Machine Learning, compilare un ambiente o distribuire un endpoint del servizio Web. Per il registro contenitori creato dall'area di lavoro sarà abilitato l'utente amministratore ed è necessario disabilitare manualmente l'utente amministratore.

1. Creazione di una nuova area di lavoro

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Eseguire un'azione che richiede ACR. Ad esempio, l' [esercitazione sul training di un modello](tutorial-train-models-with-aml.md).

1. Ottenere il nome dell'ACR creato dal cluster:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Questo comando restituisce un valore simile al testo seguente. Si desidera solo l'ultima parte del testo, ovvero il nome dell'istanza di ACR:

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Aggiornare l'ACR per disabilitare l'utente amministratore:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Creare un calcolo con identità gestita per accedere alle immagini Docker per il training

Per accedere all'area di lavoro ACR, creare un cluster di calcolo di Machine Learning con l'identità gestita assegnata dal sistema abilitata. È possibile abilitare l'identità da portale di Azure o Studio quando si crea il calcolo o dall'interfaccia della riga di comando di Azure usando il comando seguente. Per altre informazioni, vedere [uso dell'identità gestita con i cluster di elaborazione](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

Quando si crea un cluster di calcolo con [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration), usare il `identity_type` parametro per impostare il tipo di identità gestito.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per informazioni sulla configurazione dell'identità gestita durante la creazione di un cluster di calcolo in studio, vedere [configurare l'identità gestita](how-to-create-attach-compute-studio.md#managed-identity).

---

A un'identità gestita viene concesso automaticamente il ruolo ACRPull nell'area di lavoro di ACR per consentire il pull di immagini Docker per il training.

> [!NOTE]
> Se si crea prima il calcolo, prima della creazione dell'area di lavoro, è necessario assegnare manualmente il ruolo ACRPull.

## <a name="access-base-images-from-private-acr"></a>Accedi alle immagini di base da ACR privato

Per impostazione predefinita, Azure Machine Learning usa le immagini di base di Docker che provengono da un repository pubblico gestito da Microsoft. Quindi compila l'ambiente di formazione o inferenza su tali immagini. Per altre informazioni, vedere [che cosa sono gli ambienti ml?](concept-environments.md).

Per usare un'immagine di base personalizzata interna all'azienda, è possibile usare le identità gestite per accedere all'ACR privato. Esistono due casi di utilizzo:

 * Utilizzare l'immagine di base per il training così come è.
 * Compilare Azure Machine Learning immagine gestita con un'immagine personalizzata come base.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Eseguire il pull dell'immagine di base Docker nel cluster di calcolo di machine learning per il training così come è

Creare un cluster di calcolo di Machine Learning con identità gestita assegnata dal sistema abilitata come descritto in precedenza. Determinare quindi l'ID entità dell'identità gestita.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Facoltativamente, è possibile aggiornare il cluster di calcolo per assegnare un'identità gestita assegnata dall'utente:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Per consentire al cluster di calcolo di eseguire il pull delle immagini di base, concedere il ruolo ACRPull identità del servizio gestito nell'ACR privato

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Infine, quando si invia un'esecuzione di training, specificare il percorso dell'immagine di base nella [definizione dell'ambiente](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Per assicurarsi che l'immagine di base venga direttamente collegata alla risorsa di calcolo, impostare `user_managed_dependencies = True` e non specificare un Dockerfile. In caso contrario Azure Machine Learning servizio tenterà di creare una nuova immagine Docker e avrà esito negativo, perché solo il cluster di calcolo ha accesso per eseguire il pull dell'immagine di base da ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Crea Azure Machine Learning ambiente gestito in un'immagine di base dall'ACR privato per il training o l'inferenza

In questo scenario, Azure Machine Learning servizio compila l'ambiente di training o di inferenza sopra un'immagine di base fornita da un ACR privato. Poiché l'attività di compilazione immagine viene eseguita nell'area di lavoro di ACR usando le attività ACR, è necessario eseguire altri passaggi per consentire l'accesso.

1. Creare un' __identità gestita assegnata dall'utente__ e concedere all'identità ACRPull l'accesso al record di accesso __privato__.  
1. Concedere all' __identità gestita assegnata dal sistema__ dell'area di lavoro un ruolo di operatore di identità gestito nell' __identità gestita assegnata dall'utente__ dal passaggio precedente. Questo ruolo consente all'area di lavoro di assegnare l'identità gestita assegnata dall'utente all'attività ACR per la compilazione dell'ambiente gestito. 

    1. Ottenere l'ID principale dell'identità gestita assegnata dal sistema dell'area di lavoro:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Concedere il ruolo di operatore identità gestita:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        L'ID di risorsa UAI è l'ID risorsa di Azure dell'identità assegnata dall'utente, nel formato `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Specificare l'ID di registro esterno e l'ID client dell' __identità gestita assegnata dall'utente__ nelle connessioni dell'area di lavoro usando [Workspace.set_Connection metodo](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Al termine della configurazione, è possibile usare le immagini di base dell'ACR privato quando si compilano ambienti per il training o l'inferenza. Il frammento di codice seguente illustra come specificare il nome dell'immagine di base ACR e il nome dell'immagine in una definizione di ambiente:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Facoltativamente, è possibile specificare l'URL della risorsa di identità gestita e l'ID client nella definizione di ambiente stessa usando [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity). Se si usa l'identità del registro di sistema in modo esplicito, sostituisce le connessioni all'area di lavoro specificate in precedenza

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Usare le immagini Docker per l'inferenza

Dopo aver configurato ACR senza utente amministratore come descritto in precedenza, è possibile accedere alle immagini Docker per inferenza senza chiavi amministrative dal servizio Azure Kubernetes (AKS). Quando si crea o si connette AKS all'area di lavoro, all'entità servizio del cluster viene assegnato automaticamente l'accesso ACRPull all'area di lavoro ACR.

> [!NOTE]
> Se si usa il proprio cluster AKS, il cluster deve avere un'entità servizio abilitata anziché un'identità gestita.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Crea area di lavoro con identità gestita assegnata dall'utente

Quando si crea un'area di lavoro, è possibile specificare un'identità gestita assegnata dall'utente che verrà usata per accedere alle risorse associate: ACR, insieme di credenziali delle credenziali, archiviazione e App Insights.

Creare prima di tutto [un'identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli])e prendere nota dell'ID risorsa ARM dell'identità gestita.

Quindi, usare l'interfaccia della riga di comando di Azure o Python SDK per creare l'area di lavoro. Quando si usa l'interfaccia della riga di comando, specificare l'ID utilizzando il `--primary-user-assigned-identity` parametro. Quando si usa l'SDK, usare `primary_user_assigned_identity` . Di seguito sono riportati alcuni esempi di uso dell'interfaccia della riga di comando di Azure e di Python per creare una nuova area di lavoro usando questi parametri:

__Interfaccia della riga di comando di Azure__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

È anche possibile usare [un modello ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) per creare un'area di lavoro con identità gestita assegnata dall'utente.

> [!IMPORTANT]
> Se si riportano risorse associate, invece di creare Azure Machine Learning servizio, è necessario concedere i ruoli di identità gestiti a tali risorse. Usare il [modello ARM di assegnazione di ruolo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) per effettuare le assegnazioni.

Per un'area di lavoro con (chiavi gestite dal cliente per la crittografia) [ https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ], è possibile passare un'identità gestita assegnata dall'utente per eseguire l'autenticazione dalla risorsa di archiviazione a Key Vault. Usare l'argomento __user-assigned-Identity-for-CMK-Encryption__ (CLI) o __user_assigned_identity_for_cmk_encryption__ (SDK) per passare l'identità gestita. Questa identità gestita può essere uguale o diversa dall'identità gestita assegnata dall'utente primario dell'area di lavoro.

Se si dispone di un'area di lavoro esistente, è possibile aggiornarla dall'identità gestita assegnata dal sistema a quella assegnata dall'utente usando il ```az ml workspace update``` comando CLI o il ```Workspace.update``` Metodo Python SDK.


## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [sicurezza aziendale in Azure Machine Learning](concept-enterprise-security.md).
