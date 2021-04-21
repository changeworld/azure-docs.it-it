---
title: Esecuzione di attività rapide con il modello
description: Accodare l'esecuzione di un'attività ACR per compilare un'immagine usando un modello Azure Resource Manager accodato
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781236"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Eseguire le attività di ACR usando Resource Manager modelli

[Attività del Registro Azure Container](container-registry-tasks-overview.md) è un gruppo di funzionalità incluse in Registro Azure Container che consente di gestire e modificare le immagini del contenitore per tutto il ciclo di vita del contenitore. 

Questo articolo illustra Azure Resource Manager esempi di modello per accodare un'esecuzione rapida di attività, simile a quella che è possibile creare manualmente usando il [comando az acr build.][az-acr-build]

Un Resource Manager modello per accodare un'esecuzione di attività è utile negli scenari di automazione ed estende la funzionalità di `az acr build` . Ad esempio:

* Usare un modello per creare un registro contenitori e accodare immediatamente un'esecuzione di attività per compilare ed eseguire il push di un'immagine del contenitore
* Creare o abilitare risorse aggiuntive che è possibile usare in un'esecuzione di attività rapide, ad esempio un'identità gestita per le risorse di Azure

## <a name="limitations"></a>Limitazioni

* È necessario specificare un contesto remoto, ad esempio un repository GitHub, come percorso [di origine](container-registry-tasks-overview.md#context-locations) per l'esecuzione dell'attività. Non è possibile usare un contesto di origine locale.
* Per le esecuzioni di attività che usano un'identità gestita, è consentita solo *un'identità* gestita assegnata dall'utente.

## <a name="prerequisites"></a>Prerequisiti

* **Account GitHub:** creare un account https://github.com in se non ne è già presente uno. 
* **Fork sample repository** ( Fork repository di esempio): per gli esempi di attività illustrati di seguito, usare l'interfaccia utente di GitHub per creare il fork del repository di esempio seguente nell'account GitHub: https://github.com/Azure-Samples/acr-build-helloworld-node . Questo repo contiene dockerfile di esempio e codice sorgente per compilare immagini di contenitori di piccole dimensioni.

## <a name="example-create-registry-and-queue-task-run"></a>Esempio: Creare un registro ed eseguire l'attività di accodamento

Questo esempio usa un modello [di esempio per](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) creare un registro contenitori e accodare un'esecuzione di attività che compila ed esegue il push di un'immagine. 

### <a name="template-parameters"></a>Parametri di modelli

Per questo esempio, specificare i valori per i parametri di modello seguenti:

|Parametro  |Valore  |
|---------|---------|
|registryName     |Nome univoco del registro creato         |
|repository     |Repository di destinazione per l'attività di compilazione        |
|taskRunName     |Nome dell'esecuzione dell'attività, che specifica il tag di immagine |
|Sourcelocation     |Contesto remoto per l'attività di compilazione, ad esempio https://github.com/Azure-Samples/acr-build-helloworld-node . Il Dockerfile nella radice del repo compila un'immagine del contenitore per una piccola Node.js app Web. Se si desidera, usare il fork del repo come contesto di compilazione.         |

### <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello con il [comando az deployment group create.][az-deployment-group-create] Questo esempio compila ed esegue il push dell'immagine *helloworld-node:testrun* in un registro denominato *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Il comando precedente passa i parametri nella riga di comando. Se si desidera, passarli in un [file di parametri](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificare la distribuzione

Al termine della distribuzione, verificare che l'immagine sia compilata eseguendo [az acr repository show-tags:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Output:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Visualizzare il log di esecuzione

Per visualizzare i dettagli sull'esecuzione dell'attività, visualizzare il log di esecuzione.

Ottenere prima di tutto l'ID esecuzione [con az acr task list-runs][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

L'output è simile a:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Eseguire [az acr task logs per][az-acr-task-logs] visualizzare i log di esecuzione delle attività per l'ID esecuzione, in questo caso *ca1:*

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

L'output mostra il log di esecuzione dell'attività.

È anche possibile visualizzare il log di esecuzione delle attività nel portale di Azure. 

1. Passare al registro contenitori
2. In **Servizi** selezionare **Attività**  >  **Esegue**.
3. Selezionare l'ID esecuzione, in questo caso *ca1*. 

Il portale mostra il log di esecuzione delle attività.

## <a name="example-task-run-with-managed-identity"></a>Esempio: Esecuzione di attività con identità gestita

Usare un [modello di esempio per](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) accodare un'esecuzione di attività che abilita un'identità gestita assegnata dall'utente. Durante l'esecuzione dell'attività, l'identità esegue l'autenticazione per eseguire il pull di un'immagine da un altro Registro Azure Container. 

Questo scenario è simile [all'autenticazione tra registro in un'attività ACR usando un'identità gestita di Azure.](container-registry-tasks-cross-registry-authentication.md) Ad esempio, un'organizzazione potrebbe gestire un registro centralizzato con immagini di base accessibili da più team di sviluppo.

### <a name="prepare-base-registry"></a>Preparare il registro di base

A scopo dimostrativo, creare un registro contenitori separato come registro di base ed eseguire il push di un'Node.js di base estratta da Docker Hub.

1. Creare un secondo registro contenitori, ad esempio *mybaseregistry*, per archiviare le immagini di base.
1. Eseguire il `node:9-alpine` pull dell'immagine Docker Hub, contrassegnarla per il registro di base ed eseguire il push nel Registro di sistema di base:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Creare un nuovo Dockerfile

Creare un Dockerfile che estrae l'immagine di base dal registro di base. Seguire questa procedura nel fork locale del repository GitHub, ad esempio `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. Nell'interfaccia utente di GitHub selezionare **Crea nuovo file.**
1. Assegnare al file *il nome Dockerfile-test* e incollare il contenuto seguente. Sostituire il nome del Registro di sistema *con mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selezionare **Commit new file (Commit nuovo file).**

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Assegnare all'identità le autorizzazioni per eseguire il pull dal registro di base

Concedere all'identità gestita le autorizzazioni per eseguire il pull dal Registro di sistema di base, *mybaseregistry*.

Usare il comando [az acr show][az-acr-show] per ottenere l'ID risorsa del registro di base e archiviarlo in una variabile:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Usare il [comando az role assignment create][az-role-assignment-create] per assegnare l'identità del ruolo Acrpull al registro di base. Questo ruolo ha solo le autorizzazioni per eseguire il pull di immagini dal registro.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parametri di modelli

Per questo esempio, specificare i valori per i parametri di modello seguenti:

|Parametro  |Valore  |
|---------|---------|
|registryName     |Nome del registro in cui viene compilata l'immagine  |
|repository     |Repository di destinazione per l'attività di compilazione        |
|taskRunName     |Nome dell'esecuzione dell'attività, che specifica il tag di immagine |
|userAssignedIdentity |ID risorsa dell'identità assegnata dall'utente abilitata nell'attività|
|customRegistryIdentity | ID client dell'identità assegnata dall'utente abilitata nell'attività, usato per l'autenticazione con il registro personalizzato |
|customRegistry |Nome del server di accesso del Registro di sistema personalizzato a cui si accede nell'attività, ad esempio *mybaseregistry.azurecr.io*|
|Sourcelocation     |Contesto remoto per l'attività di compilazione, ad esempio *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | Percorso del Dockerfile nel contesto remoto, usato per compilare l'immagine. |

### <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello con il [comando az deployment group create.][az-deployment-group-create] Questo esempio compila ed esegue il push dell'immagine *helloworld-node:testrun* in un registro denominato *mycontainerregistry*. L'immagine di base viene estratta *da mybaseregistry.azurecr.io*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Il comando precedente passa i parametri nella riga di comando. Se si desidera, passarli in un [file di parametri](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificare la distribuzione

Al termine della distribuzione, verificare che l'immagine sia compilata eseguendo [az acr repository show-tags:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Output:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Visualizzare il log di esecuzione

Per visualizzare il log di esecuzione, vedere i passaggi nella [sezione precedente.](#view-run-log)

## <a name="next-steps"></a>Passaggi successivi

 * Vedere altri esempi di modello nel repository [GitHub di ACR.](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)
 * Per informazioni dettagliate sulle proprietà del modello, vedere le informazioni di riferimento sul modello per [Esecuzioni di attività](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) e [Attività](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
