---
title: Compilare, testare e distribuire contenitori servizio Azure Kubernetes usando GitHub Actions
description: Informazioni su come usare GitHub Actions distribuire il contenitore in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3d0b6030cc63d0d7f4eac2c72c3545cf315b1fd3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832388"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions per la distribuzione nel servizio Kubernetes

[GitHub Actions](https://docs.github.com/en/actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. È possibile usare più azioni Kubernetes per la distribuzione in contenitori da Registro Azure Container a servizio Azure Kubernetes con GitHub Actions. 

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join).  
- Un cluster Kubernetes funzionante
    - [Esercitazione: Preparare un'applicazione per servizio Azure Kubernetes](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro che ha come destinazione il servizio AKS, il file include tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | Accedere a un registro contenitori privato (ACR) |
|**Compila** | Compilare & eseguire il push dell'immagine del contenitore  |
|**Distribuzione** | 1. Impostare il cluster del servizio Web Di destinazione |
| |2. Creare un segreto generico/docker-registry nel cluster Kubernetes  |
||3. Eseguire la distribuzione nel cluster Kubernetes|

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) nell'[interfaccia della riga di comando di Azure](/cli/azure/). È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Nel comando precedente sostituire i segnaposto con l'ID sottoscrizione e il gruppo di risorse. L'output è le credenziali di assegnazione di ruolo che forniscono l'accesso alla risorsa. Il comando deve eseguire l'output di un oggetto JSON simile al seguente.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Copiare questo oggetto JSON, che potrà essere usato per eseguire l'autenticazione da GitHub.

## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

Seguire la procedura per configurare i segreti:

1. In [GitHub](https://github.com/)passare al repository, selezionare Impostazioni > **segreti > Aggiungi un nuovo segreto.**

    ![Screenshot che mostra il collegamento Aggiungi un nuovo segreto per un repository.](media/kubernetes-action/secrets.png)

2. Incollare il contenuto del `az cli` comando precedente come valore della variabile secret. Ad esempio: `AZURE_CREDENTIALS`.

3. Analogamente, definire i segreti aggiuntivi seguenti per le credenziali del registro contenitori e impostarli nell'azione di accesso Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. I segreti verranno visualizzati come illustrato di seguito dopo la definizione.

    ![Screenshot che mostra i segreti esistenti per un repository.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Creare un'immagine del contenitore e distribui servizio Azure Kubernetes cluster

La compilazione e il push delle immagini del contenitore vengono eseguite usando `Azure/docker-login@v1` l'azione . 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Eseguire la distribuzione servizio Azure Kubernetes cluster

Per distribuire un'immagine del contenitore nel servizio Container, è necessario usare `Azure/k8s-deploy@v1` l'azione . Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **Namespace** | (Facoltativo) Scegliere lo spazio dei nomi Kubernetes di destinazione. Se lo spazio dei nomi non viene specificato, i comandi verranno eseguiti nello spazio dei nomi predefinito | 
| **Manifesti** |  (Obbligatorio) Percorso dei file manifesto, che verranno usati per la distribuzione |
| **images** | (Facoltativo) URL di risorsa completo delle immagini da usare per le sostituzioni nei file manifesto |
| **imagepullsecrets** | (Facoltativo) Nome di un segreto docker-registry già configurato all'interno del cluster. Ognuno di questi nomi di segreto viene aggiunto nel campo imagePullSecrets per i carichi di lavoro trovati nei file manifesto di input |
| **kubectl-version** | (Facoltativo) Installa una versione specifica del file binario kubectl |


Prima di poter eseguire la distribuzione nel servizio Kubernetes, è necessario impostare lo spazio dei nomi Kubernetes di destinazione e creare un segreto di pull dell'immagine. Per altre informazioni sul funzionamento del pull delle immagini, vedere Eseguire il pull di immagini da un registro Azure Container a un [cluster Kubernetes.](../container-registry/container-registry-auth-kubernetes.md) 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      arguments: --force true
```


Completare la distribuzione con `k8s-deploy` l'azione . Sostituire le variabili di ambiente con i valori per l'applicazione. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il cluster Kubernetes, il registro contenitori e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle servizio Azure Kubernetes](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Altri GitHub Actions Kubernetes

* [Programma di installazione dello strumento Kubectl](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): installa una versione specifica di kubectl nel runner.
* [Contesto del set kubernetes](https://github.com/Azure/k8s-set-context) ( ): impostare il contesto del cluster Kubernetes di destinazione che verrà usato da altre azioni o eseguire qualsiasi `azure/k8s-set-context` comando kubectl.
* [Contesto del set del servizio](https://github.com/Azure/aks-set-context) Servizio Servizio Azure Kubernetes del servizio Servizio Azure Kubernetes `azure/aks-set-context` cluster.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret) ( ): crea un segreto generico o un segreto `azure/k8s-create-secret` docker-registry nel cluster Kubernetes.
* [Distribuzione di Kubernetes](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): bake e distribuzione dei manifesti nei cluster Kubernetes.
* [Installazione di Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): installare una versione specifica del file binario Helm nel runner.
* [Bake kubernetes](https://github.com/Azure/k8s-bake) ( ): file manifesto bake da usare per le distribuzioni che usano `azure/k8s-bake` helm2, kustomize o kompose.
* [Kubernetes lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): convalida/lint dei file manifesto.
