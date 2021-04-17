---
title: Creare un servizio collegato con Synapse e Azure Machine Learning aree di lavoro (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come collegare Azure Synapse e Azure Machine Learning aree di lavoro per un'esperienza data wrangling unificata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575737"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Collegare Azure Synapse Analytics e Azure Machine Learning di lavoro (anteprima)

In questo articolo si apprenderà come [](/azure/synapse-analytics/overview-what-is) creare un servizio collegato che collega l'area di lavoro Azure Synapse Analytics e l'area [Azure Machine Learning lavoro .](concept-workspace.md)

Con l'area di lavoro Azure Machine Learning collegata all'area di lavoro Azure Synapse, è possibile collegare un pool Apache Spark come calcolo dedicato per data wrangling su larga scala o eseguire il training del modello dallo stesso notebook Python.

È possibile collegare l'area di lavoro di ML e l'area di lavoro Synapse tramite [Python SDK](#link-sdk) o [studio di Azure Machine Learning](#link-studio).

È anche possibile collegare aree di lavoro e collegare un pool Synapse Spark con un singolo [Azure Resource Manager (ARM).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> L'Azure Machine Learning e Azure Synapse è disponibile in anteprima pubblica. Le funzionalità presentate dal pacchetto `azureml-synapse` sono funzionalità di anteprima [sperimentali](/python/api/overview/azure/ml/#stable-vs-experimental) e possono cambiare in qualsiasi momento.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Creare un'area di lavoro Synapse in portale di Azure](/azure/synapse-analytics/quickstart-create-workspace).

* [Creare Apache Spark pool usando portale di Azure, strumenti Web o Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Installare [l'sdk Azure Machine Learning Python](/python/api/overview/azure/ml/intro)

* Accesso [all'studio di Azure Machine Learning](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Collegare aree di lavoro con Python SDK

> [!IMPORTANT]
> Per eseguire correttamente il collegamento all'area di lavoro Synapse, è necessario disporre del ruolo **Proprietario** dell'area di lavoro Synapse. Controllare l'accesso nel [portale di Azure](https://ms.portal.azure.com/).
>
> Se non si è un **proprietario e** si è solo un **collaboratore** all'area di lavoro Synapse, è possibile usare solo i servizi collegati esistenti. Vedere Come [ritentare e usare un servizio collegato esistente.](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)

Il codice seguente usa le [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) classi [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) e per,

* Collegare l'area di lavoro di Machine Learning `ws` con l'Azure Synapse lavoro.
* Registrare l'area di lavoro di Synapse Azure Machine Learning come servizio collegato.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Viene creata `system_assigned_identity_principal_id` un'identità gestita, , per ogni servizio collegato. A questa identità gestita deve essere concesso il ruolo di amministratore Apache Spark **Synapse** dell'area di lavoro Synapse prima di avviare la sessione di Synapse. [Assegnare il ruolo Amministratore Apache Spark Synapse all'identità gestita nel Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Per trovare `system_assigned_identity_principal_id` l'oggetto di un servizio collegato specifico, usare `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Gestire i servizi collegati

Visualizza tutto i servizi collegati associati all'area di lavoro di Machine Learning.

```python
LinkedService.list(ws)
```

Per scollegare le aree di lavoro, usare il `unregister()` metodo

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Collegare aree di lavoro tramite Studio

Collegare l'area di lavoro di Machine Learning e l'area di lavoro Synapse tramite studio di Azure Machine Learning con la procedura seguente: 

1. Accedere al [studio di Azure Machine Learning](https://ml.azure.com/).
1. Selezionare **Servizi collegati** nella **sezione** Gestisci del riquadro sinistro.
1. Selezionare **Aggiungi integrazione.**
1. Nel modulo **Collega area di** lavoro compilare i campi

    |Campo| Descrizione    
    |---|---
    |Nome| Specificare un nome per il servizio collegato. Questo nome verrà usato per fare riferimento a questo particolare servizio collegato.
    |Nome sottoscrizione | Selezionare il nome della sottoscrizione associata all'area di lavoro di Machine Learning. 
    |Area di lavoro di Synapse | Selezionare l'area di lavoro di Synapse a cui si vuole creare un collegamento.
    
1. Selezionare **Avanti** per aprire il **modulo Selezionare i pool di Spark (facoltativo).** In questo modulo si seleziona il pool di Synapse Spark da collegare all'area di lavoro

1. Selezionare **Avanti** per aprire il **modulo Rivedi** e controllare le selezioni.
1. Selezionare **Crea** per completare il processo di creazione del servizio collegato.

## <a name="next-steps"></a>Passaggi successivi

* [Collegare pool di Synapse Spark per la preparazione dei dati con Azure Synapse (anteprima).](how-to-data-prep-synapse-spark-pool.md)
* [Come usare Apache Spark nella pipeline di Machine Learning con Azure Synapse (anteprima)](how-to-use-synapsesparkstep.md)
* [Training di un modello](how-to-set-up-training-targets.md).
