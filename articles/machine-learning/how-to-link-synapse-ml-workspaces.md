---
title: Creare un servizio collegato con le aree di lavoro sinapsi e Azure Machine Learning (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come collegare le aree di lavoro di Azure sinapsi e Azure Machine Learning per un'esperienza di data wrangling unificata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: dd62fb5e3c7450d50b9837ee5484ca480cab78aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640828"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Collegare le aree di lavoro Azure Machine Learning e di analisi delle sinapsi di Azure (anteprima)

Questo articolo illustra come creare un servizio collegato che collega l'area di lavoro di [Azure sinapsi Analytics](/synapse-analytics/overview-what-is.md) e [Azure machine learning area di lavoro](concept-workspace.md).

Con l'area di lavoro di Azure Machine Learning collegata all'area di lavoro di Azure sinapsi, è possibile collegare un pool di Apache Spark come calcolo dedicato per data wrangling su larga scala ed eseguire il training del modello dallo stesso notebook.

È possibile collegare l'area di lavoro ML e l'area di lavoro sinapsi tramite [Python SDK](#link-sdk) o il [Azure Machine Learning Studio](#link-studio).

È anche possibile collegare le aree di lavoro e collegare un pool di Spark di sinapsi a un singolo [modello di Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> Il Azure Machine Learning e l'integrazione di sinapsi di Azure sono in anteprima pubblica. Le funzionalità presentate dal `azureml-synapse` pacchetto sono funzionalità di [Anteprima sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e possono cambiare in qualsiasi momento.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md?tabs=python).

* [Creare un'area di lavoro sinapsi in portale di Azure](/azure/synapse-analytics/quickstart-create-workspace).

* [Creare Apache Spark pool con portale di Azure, strumenti Web o sinapsi Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Installare il [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)

* Accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Collegamento di aree di lavoro con Python SDK

> [!IMPORTANT]
> Per eseguire correttamente il collegamento all'area di lavoro sinapsi, è necessario disporre del ruolo **proprietario** dell'area di lavoro sinapsi. Controllare l'accesso nella [portale di Azure](https://ms.portal.azure.com/).
>
> Se non si è un **proprietario** e si è solo un **collaboratore** all'area di lavoro sinapsi, è possibile usare solo i servizi collegati esistenti. Vedere come [recuperare e usare un servizio collegato esistente](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Il codice seguente usa le [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) classi e [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) per,

* Collegare l'area di lavoro di Machine Learning `ws` con l'area di lavoro di Azure sinapsi.
* Registrare l'area di lavoro sinapsi con Azure Machine Learning come servizio collegato.

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
> Viene creata un'identità gestita `system_assigned_identity_principal_id` per ogni servizio collegato. A questa identità gestita deve essere concesso il ruolo di **amministratore apache Spark sinapsi** dell'area di lavoro sinapsi prima di avviare la sessione sinapsi. [Assegnare il ruolo di amministratore apache Spark sinapsi all'identità gestita in sinapsi Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Per trovare il `system_assigned_identity_principal_id` di un servizio collegato specifico, usare `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Gestisci servizi collegati

Visualizzare tutti i servizi collegati associati all'area di lavoro di machine learning.

```python
LinkedService.list(ws)
```

Per scollegare le aree di lavoro, usare il `unregister()` Metodo

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Collegamento di aree di lavoro tramite studio

Collegare l'area di lavoro di machine learning e l'area di lavoro sinapsi tramite il Azure Machine Learning Studio con i passaggi seguenti: 

1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **servizi collegati** nella sezione **Gestisci** del riquadro sinistro.
1. Selezionare **Aggiungi integrazione**.
1. Nel form **area di lavoro collegamento** popolare i campi

    |Campo| Descrizione    
    |---|---
    |Nome| Specificare un nome per il servizio collegato. Questo nome è quello che verrà usato per fare riferimento a questo particolare servizio collegato.
    |Nome sottoscrizione | Selezionare il nome della sottoscrizione associata all'area di lavoro di machine learning. 
    |Area di lavoro sinapsi | Selezionare l'area di lavoro sinapsi a cui si desidera collegarsi.
    
1. Selezionare **Next (avanti** ) per aprire il modulo **Select Spark pools (facoltativo)** . In questo modulo si seleziona il pool di Spark di sinapsi da connettersi all'area di lavoro

1. Selezionare **Avanti** per **aprire il modulo di verifica e** verificare le selezioni.
1. Selezionare **Crea** per completare il processo di creazione del servizio collegato.

## <a name="next-steps"></a>Passaggi successivi

* [Associare i pool di Spark sinapsi per la preparazione dei dati con la sinapsi di Azure (anteprima)](how-to-data-prep-synapse-spark-pool.md).
* [Come usare Apache Spark nella pipeline di Machine Learning con la sinapsi di Azure (anteprima)](how-to-use-synapsesparkstep.md)
* Eseguire [il training di un modello](how-to-set-up-training-targets.md).
