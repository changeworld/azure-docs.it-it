---
title: Configurare un endpoint privato
titleSuffix: Azure Machine Learning
description: Usare collegamento privato di Azure per accedere in modo sicuro all'area Azure Machine Learning lavoro da una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 5e13c97427736d60f300d2d7b502c6f3e15fb481
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861446"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Configurare la collegamento privato di Azure per un'area Azure Machine Learning lavoro

In questo documento si apprenderà come usare le collegamento privato di Azure con l'area Azure Machine Learning lavoro. Per informazioni sulla creazione di una rete virtuale per Azure Machine Learning, vedere Panoramica [dell'isolamento della rete](how-to-network-security-overview.md) virtuale e della privacy

collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro in modo che si verifichi solo tramite gli indirizzi IP privati. Collegamento privato consente di ridurre il rischio di esfiltrazione dei dati. Per altre informazioni sugli endpoint privati, vedere l'articolo [Collegamento privato di Azure](../private-link/private-link-overview.md).

> [!IMPORTANT]
> collegamento privato di Azure non influisce sul piano di controllo di Azure (operazioni di gestione), ad esempio l'eliminazione dell'area di lavoro o la gestione delle risorse di calcolo. Ad esempio, la creazione, l'aggiornamento o l'eliminazione di una destinazione di calcolo. Queste operazioni vengono eseguite sulla rete Internet pubblica come di consueto. Le operazioni del piano dati, ad esempio l'studio di Azure Machine Learning, le API (incluse le pipeline pubblicate) o l'SDK usano l'endpoint privato.
>
> È possibile che si verifichino problemi durante il tentativo di accesso all'endpoint privato per l'area di lavoro se si usa Mozilla Firefox. Questo problema può essere correlato a DNS su HTTPS in Mozilla. È consigliabile usare Microsoft Edge o Google Chrome come soluzione alternativa.

## <a name="prerequisites"></a>Prerequisiti

* Se si prevede di usare un'area di lavoro abilitata per il collegamento privato con una chiave gestita dal cliente, è necessario richiedere questa funzionalità usando un ticket di supporto. Per altre informazioni, vedere [Gestire e aumentare le quote.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

* È necessario disporre di una rete virtuale esistente in cui creare l'endpoint privato. È anche necessario [disabilitare i criteri di rete per gli endpoint privati prima](../private-link/disable-private-endpoint-network-policy.md) di aggiungere l'endpoint privato.
## <a name="limitations"></a>Limitazioni

* L'Azure Machine Learning di lavoro con collegamento privato non è disponibile nelle Azure per enti pubblici geografiche.
* Se si abilita l'accesso pubblico per un'area di lavoro protetta con collegamento privato e si usa studio di Azure Machine Learning su Internet pubblico, alcune funzionalità, ad esempio la finestra di progettazione, potrebbero non riuscire ad accedere ai dati. Questo problema si verifica quando i dati vengono archiviati in un servizio protetto dietro la rete virtuale. Ad esempio, un Archiviazione di Azure account.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Creare un'area di lavoro che usa un endpoint privato

Usare uno dei metodi seguenti per creare un'area di lavoro con un endpoint privato. Ognuno di questi metodi __richiede una rete virtuale esistente:__

> [!TIP]
> Se si desidera creare contemporaneamente un'area di lavoro, un endpoint privato e una rete virtuale, vedere Usare un modello Azure Resource Manager per creare un'area di lavoro [per](how-to-create-workspace-template.md)Azure Machine Learning .

# <a name="python"></a>[Python](#tab/python)

L Azure Machine Learning Python SDK fornisce la [classe PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig) che può essere usata con [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) per creare un'area di lavoro con un endpoint privato. Questa classe richiede una rete virtuale esistente.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[L'estensione dell'interfaccia della riga di](reference-azure-machine-learning-cli.md) comando di Azure per Machine Learning fornisce il comando [az ml workspace create.](/cli/azure/ml/workspace#az_ml_workspace_create) I parametri seguenti per questo comando possono essere usati per creare un'area di lavoro con una rete privata, ma richiede una rete virtuale esistente:

* `--pe-name`: nome dell'endpoint privato creato.
* `--pe-auto-approval`: indica se le connessioni endpoint private all'area di lavoro devono essere approvate automaticamente.
* `--pe-resource-group`: gruppo di risorse in cui creare l'endpoint privato. Deve essere lo stesso gruppo che contiene la rete virtuale.
* `--pe-vnet-name`: rete virtuale esistente in cui creare l'endpoint privato.
* `--pe-subnet-name`: nome della subnet in cui creare l'endpoint privato. Il valore predefinito è `default`.

Questi parametri sono oltre ad altri parametri obbligatori per il comando create. Ad esempio, il comando seguente crea una nuova area di lavoro nell'area Stati Uniti occidentali usando un gruppo di risorse e una rete virtuale esistenti:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

La __scheda__ Rete in studio di Azure Machine Learning consente di configurare un endpoint privato. Tuttavia, richiede una rete virtuale esistente. Per altre informazioni, vedere [Creare aree di lavoro nel portale.](how-to-manage-workspace.md)

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Aggiungere un endpoint privato a un'area di lavoro

Usare uno dei metodi seguenti per aggiungere un endpoint privato a un'area di lavoro esistente:

> [!WARNING]
>
> Se sono presenti destinazioni di calcolo esistenti associate a questa area di lavoro e non si trova dietro la stessa rete virtuale in cui viene creato l'endpoint privato, non funzioneranno.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Per altre informazioni sulle classi e i metodi usati in questo esempio, vedere [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) [e Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[L'estensione dell'interfaccia della riga](reference-azure-machine-learning-cli.md) di comando di Azure per Machine Learning fornisce il comando [az ml workspace private-endpoint add.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add)

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'Azure Machine Learning di lavoro nel portale selezionare __Connessioni endpoint privato__ e quindi + Endpoint __privato.__ Usare i campi per creare un nuovo endpoint privato.

* Quando si seleziona __l'area__, selezionare la stessa area della rete virtuale. 
* Quando si __seleziona Tipo di__ risorsa, usare __Microsoft.MachineLearningServices/workspaces__. 
* Impostare Risorsa sul __nome__ dell'area di lavoro.

Selezionare infine __Crea per__ creare l'endpoint privato.

---

## <a name="remove-a-private-endpoint"></a>Rimuovere un endpoint privato

Usare uno dei metodi seguenti per rimuovere un endpoint privato da un'area di lavoro:

# <a name="python"></a>[Python](#tab/python)

Usare [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) per rimuovere un endpoint privato.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[L'estensione dell'interfaccia della riga di comando di Azure per Machine Learning](reference-azure-machine-learning-cli.md) fornisce il comando [az ml workspace private-endpoint delete.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'Azure Machine Learning di lavoro nel portale selezionare __Connessioni endpoint privato__ e quindi selezionare l'endpoint da rimuovere. Infine, selezionare __Rimuovi__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Uso di un'area di lavoro su un endpoint privato

Poiché la comunicazione con l'area di lavoro è consentita solo dalla rete virtuale, tutti gli ambienti di sviluppo che usano l'area di lavoro devono essere membri della rete virtuale. Ad esempio, una macchina virtuale nella rete virtuale.

> [!IMPORTANT]
> Per evitare interruzioni temporanee della connettività, Microsoft consiglia di scaricare la cache DNS nei computer che si connettono all'area di lavoro dopo l'abilitazione del collegamento privato. 

Per informazioni sulle macchine virtuali di Azure, vedere la [documentazione relativa alle macchine virtuali](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Abilitare l'accesso pubblico

In alcune situazioni può essere necessario consentire a un utente di connettersi all'area di lavoro protetta tramite un endpoint pubblico anziché tramite la rete virtuale. Dopo aver configurato un'area di lavoro con un endpoint privato, è possibile abilitare facoltativamente l'accesso pubblico all'area di lavoro. In questo modo non viene rimosso l'endpoint privato. Tutte le comunicazioni tra i componenti dietro la rete virtuale sono ancora protette. Consente l'accesso pubblico solo all'area di lavoro, oltre all'accesso privato tramite la rete virtuale.

> [!WARNING]
> Quando ci si connette tramite l'endpoint pubblico, alcune funzionalità di Studio non riusciranno ad accedere ai dati. Questo problema si verifica quando i dati vengono archiviati in un servizio protetto dietro la rete virtuale. Ad esempio, un Archiviazione di Azure account. Si noti anche che la funzionalità Jupyter/JupyterLab/RStudio dell'istanza di calcolo e i notebook in esecuzione non funzioneranno.

Per abilitare l'accesso pubblico a un'area di lavoro privata abilitata per il collegamento, seguire questa procedura:

# <a name="python"></a>[Python](#tab/python)

Usare [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) per rimuovere un endpoint privato.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[L'estensione dell'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) di Azure per Machine Learning fornisce il comando [az ml workspace update.](/cli/azure/ml/workspace#az_ml_workspace_update) Per abilitare l'accesso pubblico all'area di lavoro, aggiungere il parametro `--allow-public-access true` .

# <a name="portal"></a>[Portale](#tab/azure-portal)

Attualmente non è possibile abilitare questa funzionalità tramite il portale.

---


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla protezione dell'area di lavoro Azure Machine Learning, vedere l'articolo Panoramica dell'isolamento della rete virtuale [e della privacy.](how-to-network-security-overview.md)

* Se si prevede di usare una soluzione DNS personalizzata nella rete virtuale, vedere Come usare un'area di lavoro [con un server DNS personalizzato.](how-to-custom-dns.md)
