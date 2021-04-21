---
title: GitHub Actions & servizio Azure Kubernetes (anteprima)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Esaminare e testare le modifiche da una richiesta pull direttamente servizio Azure Kubernetes usando GitHub Actions e Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Kubernetes, contenitori, GitHub Actions, Helm, mesh di servizio, routing della mesh di servizio, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777546"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & servizio Azure Kubernetes (anteprima)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces fornisce un flusso di lavoro che usa GitHub Actions che consente di testare le modifiche da una richiesta pull direttamente nel servizio AppKs prima che la richiesta pull venga unita nel ramo principale del repository. La presenza di un'applicazione in esecuzione per esaminare le modifiche di una richiesta pull può aumentare l'attendibilità sia dello sviluppatore che dei membri del team. Questa applicazione in esecuzione può anche aiutare i membri del team, ad esempio i responsabili di prodotto e i progettisti, a partecipare al processo di revisione durante le fasi iniziali dello sviluppo.

In questa guida si apprenderà come:

* Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
* Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
* Configurare CI/CD con le azioni di GitHub.
* Testare un singolo microservizio in uno spazio di sviluppo isolato all'interno del contesto dell'applicazione completa.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli-installed].
* [Helm 3 installato][helm-installed].
* Un account GitHub con GitHub Actions [abilitato.][github-actions-beta-signup]
* [L'Azure Dev Spaces di esempio Bike Sharing in](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) esecuzione in un cluster del servizio Web Disaks.

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

Creare un Registro Azure Container (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Il nome del controllo di accesso deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Tutte le lettere usate devono essere minuscole.

Salvare il *valore loginServer* dall'output perché viene usato in un passaggio successivo.

## <a name="create-a-service-principal-for-authentication"></a>Creare un'entità servizio per l'autenticazione

Usare [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per creare un'entità servizio. Ad esempio:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salvare l'output JSON perché viene usato in un passaggio successivo.

Usare [az aks show][az-aks-show] per visualizzare l'ID del cluster del servizio AKS: 

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Usare [az acr show][az-acr-show] per visualizzare *l'ID* del controllo di accesso:

```azurecli
az acr show --name <acrName> --query id
```

Usare [az role assignment create per][az-role-assignment-create] concedere al *collaboratore* l'accesso al cluster del servizio Web Del servizio Web e *AcrPush* all'ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Per concedere all'entità servizio l'accesso a tali risorse, è necessario essere il proprietario sia del cluster del servizio AKS che di ACR.

## <a name="configure-your-github-action"></a>Configurare l'azione GitHub

> [!IMPORTANT]
> È necessario aver abilitato GitHub Actions per il repository. Per abilitare GitHub Actions per il repository, passare al repository in GitHub, fare clic sulla scheda Azioni e scegliere di abilitare le azioni per questo repository.

Passare al repository con fork e fare clic su *Impostazioni*. Fare clic *su Segreti* nella barra laterale sinistra. Fare *clic su Aggiungi un nuovo segreto* per aggiungere ogni nuovo segreto di seguito:

1. *AZURE_CREDENTIALS*: l'intero output della creazione dell'entità servizio.
1. *RESOURCE_GROUP*: il gruppo di risorse per il cluster del servizio Web Diaks, che in questo esempio è *MyResourceGroup*.
1. *CLUSTER_NAME*: il nome del cluster del servizio Web Disassociato di Microsoft, che in questo esempio è *MyAKS.*
1. *CONTAINER_REGISTRY*: *loginServer per* il Registro Controllo di accesso.
1. *HOST:* host per Dev Space, che assume il formato *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, che in questo esempio *è dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: il nome del segreto da usare, ad esempio *demo-secret.*
1. *MASTER_SPACE*: il nome dello spazio di sviluppo padre, che in questo esempio è *dev*.
1. *REGISTRY_USERNAME*: *clientId dall'output* JSON della creazione dell'entità servizio.
1. *REGISTRY_PASSWORD:* *clientSecret* dall'output JSON della creazione dell'entità servizio.

> [!NOTE]
> Tutti questi segreti vengono usati dall'azione GitHub e sono configurati in [.github/workflows/bikes.yml.][github-action-yaml]

Facoltativamente, se si vuole aggiornare lo spazio master dopo il merge della richiesta pull, aggiungere il segreto *GATEWAY_HOST,* che assume il formato *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, che in questo esempio è *dev.gateway.fedcab0987.eus.azds.io*. Dopo aver eseguito il merge delle modifiche nel ramo principale del fork, verrà eseguita un'altra azione per ricompilare ed eseguire l'intera applicazione nello spazio di sviluppo master. In questo esempio lo spazio master è *dev*. Questa azione è configurata in [.github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

Inoltre, se si vuole che le modifiche nella richiesta pull vengono  eseguite in uno spazio grandchild, aggiornare i segreti MASTER_SPACE *e HOST.* Ad esempio, se l'applicazione è in esecuzione in *dev* con uno spazio figlio *dev/azureuser1,* per fare in modo che la richiesta pull sia eseguita in uno spazio figlio *di dev/azureuser1:*

* Aggiornare *MASTER_SPACE* allo spazio figlio desiderato come spazio padre, in questo esempio *azureuser1*.
* Aggiornare *HOST* a *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, in questo esempio *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Creare un nuovo ramo per le modifiche al codice

Passare a `BikeSharingApp/` e creare un nuovo ramo denominato *bike-images.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Modificare [Bikes/server.js][bikes-server-js] per rimuovere le righe 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La sezione dovrebbe ora essere simile alla seguente:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salvare il file e quindi usare `git add` e per lo stage delle `git commit` modifiche.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Eseguire il push delle modifiche

Usare `git push` per eseguire il push del nuovo ramo nel repository con fork:

```cmd
git push origin bike-images
```

Al termine del push, passare al repository con fork in GitHub per creare una richiesta pull con il ramo principale nel repository con fork come ramo di base rispetto al ramo *bike-images.* 

Dopo aver aperto la richiesta pull, passare alla *scheda* Azioni. Verificare che sia stata avviata una nuova azione e che il *servizio Bikes sia* stato creato.

## <a name="view-the-child-space-with-your-changes"></a>Visualizzare lo spazio figlio con le modifiche

Al termine dell'azione, verrà visualizzato un commento con un URL per il nuovo spazio figlio in base alle modifiche nella richiesta pull.

> [!div class="mx-imgBorder"]
> ![URL azione GitHub](../media/github-actions/github-action-url.png)

Passare al *servizio bikesharingweb* aprendo l'URL dal commento. Selezionare *Aurelia Briggs (cliente)* come utente, quindi selezionare una bicicletta da noleggiare. Verificare che l'immagine segnaposto per la bicicletta non sia più visualizzata.

Se si uniscono le modifiche nel *ramo* principale nel fork, verrà eseguita un'altra azione per ricompilare ed eseguire l'intera applicazione nello spazio di sviluppo padre. In questo esempio lo spazio padre è *dev*. Questa azione è configurata in [.github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

## <a name="clean-up-your-azure-resources"></a>Pulire le risorse di Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
