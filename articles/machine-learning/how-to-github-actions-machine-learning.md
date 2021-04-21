---
title: GitHub Actions per CI/CD
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un flusso di lavoro GitHub Actions per eseguire il training di un modello in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780372"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Usare GitHub Actions con Azure Machine Learning

Introduzione [all'GitHub Actions](https://docs.github.com/en/actions) per eseguire il training di un modello Azure Machine Learning. 

> [!NOTE]
> GitHub Actions per Azure Machine Learning vengono fornite così come sono e non sono completamente supportate da Microsoft. Se si verificano problemi con un'azione specifica, aprire un problema nel repository per l'azione. Ad esempio, se si verifica un problema con l'azione aml-deploy, segnalare il problema nel [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repo.

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join).  

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file include quattro sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. |
|**Connettere** | 1. Connettersi all'area di lavoro di Machine Learning. <br /> 2. Connettersi a una destinazione di calcolo. |
|**Esegui** | 1. Inviare un'esecuzione di training. |
|**Distribuzione** | 1. Registrare il modello nel Azure Machine Learning registro. 1. Distribuire il modello. |

## <a name="create-repository"></a>Creare un repository

Creare un nuovo repository da [ML Ops con GitHub Actions e Azure Machine Learning modello](https://github.com/machine-learning-apps/ml-template-azure). 

1. Aprire il [modello](https://github.com/machine-learning-apps/ml-template-azure) in GitHub. 
2. Selezionare **Usa questo modello**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selezionare Usa questo modello":::
3. Creare un nuovo repository dal modello. Impostare il nome del repository `ml-learning` su o un nome a scelta. 


## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app simile a questo esempio. Copiare l'oggetto JSON per un uso successivo.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto.**

2. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Connettersi all'area di lavoro

Usare [l'area di lavoro di Azure Machine Learning per](https://github.com/marketplace/actions/azure-machine-learning-workspace) connettersi all'area Azure Machine Learning lavoro. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Per impostazione predefinita, l'azione prevede un `workspace.json` file. Se il file JSON ha un nome diverso, è possibile specificarlo con il `parameters_file` parametro di input. Se non è presente un file, ne verrà creato uno nuovo con il nome del repository.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
L'azione scrive le proprietà Azure Resource Manager (ARM) dell'area di lavoro in un file di configurazione, che verrà selezionato da tutti i Azure Machine Learning GitHub Actions. Il file viene salvato in `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Connettersi a una destinazione di calcolo in Azure Machine Learning

Usare [l'Azure Machine Learning calcolo per](https://github.com/Azure/aml-compute) connettersi a una destinazione di calcolo in Azure Machine Learning.  Se la destinazione di calcolo esiste, l'azione si connetterà a essa. In caso contrario, l'azione creerà una nuova destinazione di calcolo. [L'azione Calcolo AML](https://github.com/Azure/aml-compute) supporta solo il cluster di calcolo di Azure ML e servizio Azure Kubernetes (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Inviare l'esecuzione di training

Usare [l'Azure Machine Learning Training per](https://github.com/Azure/aml-run) inviare scriptRun, estimator o pipeline per Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrare il modello nel Registro di sistema

Usare [l'Azure Machine Learning Registra modello per](https://github.com/Azure/aml-registermodel) registrare un modello Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Distribuire il modello in Azure Machine Learning in ACI

Usare [l Azure Machine Learning'azione](https://github.com/Azure/aml-deploy) Distribuisci per distribuire un modello e creare un endpoint per il modello. È anche possibile usare l'Azure Machine Learning deploy per distribuire in servizio Azure Kubernetes. Vedere [questo flusso di lavoro di](https://github.com/Azure-Samples/mlops-enterprise-template) esempio per un modello che viene distribuito in servizio Azure Kubernetes.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Esempio completo

Eseguire il training del modello e distribui Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)