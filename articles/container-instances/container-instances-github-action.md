---
title: Distribuire un'istanza di contenitore tramite l'azione GitHub
description: Configurare un'azione GitHub che automatizza i passaggi per compilare, eseguire il push e distribuire un'immagine del contenitore in Istanze di Azure Container
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: df8f7a546767f1198cee6fc6ceb6032645c14989
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868988"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configurare un'azione GitHub per creare un'istanza di contenitore

[GitHub Actions](https://docs.github.com/en/actions) è una suite di funzionalità di GitHub che consente di automatizzare i flussi di lavoro di sviluppo software nella stessa posizione in cui si archivia il codice e si collabora a richieste pull e problemi.

Usare [l'azione Distribuisci Istanze di Azure Container](https://github.com/azure/aci-deploy) GitHub per automatizzare la distribuzione di un singolo contenitore Istanze di Azure Container. L'azione consente di impostare proprietà per un'istanza di contenitore simili a quelle del [comando az container create.][az-container-create]

Questo articolo illustra come configurare un flusso di lavoro in un repository GitHub che esegue le azioni seguenti:

* Compilare un'immagine da un Dockerfile
* Eseguire il push dell'immagine in un Registro Azure Container
* Distribuire l'immagine del contenitore in un'istanza di contenitore di Azure

Questo articolo illustra due modi per configurare il flusso di lavoro:

* [Configurare il flusso di lavoro GitHub:](#configure-github-workflow) creare un flusso di lavoro in un repository GitHub usando l'azione Distribuisci in Istanze di Azure Container e altre azioni.  
* [Usare l'estensione dell'interfaccia](#use-deploy-to-azure-extension) della riga di comando: usare `az container app up` il comando [nell'estensione Distribuisci](https://github.com/Azure/deploy-to-azure-cli-extension) in Azure nell'interfaccia della riga di comando di Azure. Questo comando semplifica la creazione del flusso di lavoro GitHub e dei passaggi di distribuzione.

> [!IMPORTANT]
> L'azione GitHub per Istanze di Azure Container è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* **Account GitHub:** creare un account in , se https://github.com non se ne ha già uno.
* **Interfaccia della riga di** comando di Azure: è possibile usare il Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per completare i passaggi dell'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].
* **Registro Azure Container:** se non è presente, creare un Registro Azure Container nel livello Basic usando l'interfaccia della riga di comando di [Azure,](../container-registry/container-registry-get-started-azure-cli.md) [portale di Azure](../container-registry/container-registry-get-started-portal.md)o altri metodi. Prendere nota del gruppo di risorse usato per la distribuzione, usato per il flusso di lavoro GitHub.

## <a name="set-up-repo"></a>Configurare il repo

* Per gli esempi in questo articolo, usare GitHub per creare una fork del repository seguente: https://github.com/Azure-Samples/acr-build-helloworld-node

  Questo repo contiene un Dockerfile e i file di origine per creare un'immagine del contenitore di una piccola app Web.

  ![Screenshot del pulsante Fork (evidenziato) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Assicurarsi che Azioni sia abilitato per il repository. Passare al repository con fork e selezionare **Impostazioni**  >  **Azioni**. In **Autorizzazioni azioni** assicurarsi che sia selezionata l'opzione Abilita azioni locali e di terze parti per questo **repository.**

## <a name="configure-github-workflow"></a>Configurare il flusso di lavoro GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Creare un'entità servizio per l'autenticazione di Azure

Nel flusso di lavoro GitHub è necessario fornire le credenziali di Azure per l'autenticazione all'interfaccia della riga di comando di Azure. Nell'esempio seguente viene creata un'entità servizio con il ruolo Collaboratore con ambito al gruppo di risorse per il registro contenitori.

Ottenere prima di tutto l'ID risorsa del gruppo di risorse. Sostituire il nome del gruppo nel comando [az group show][az-group-show] seguente:

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Usare [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per creare l'entità servizio:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

L'output è simile a:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Salvare l'output JSON perché viene usato in un passaggio successivo. Prendere anche nota di `clientId` , che è necessario aggiornare l'entità servizio nella sezione successiva.

### <a name="update-service-principal-for-registry-authentication"></a>Aggiornare l'entità servizio per l'autenticazione del Registro di sistema

Aggiornare le credenziali dell'entità servizio di Azure per consentire l'accesso push e pull al registro contenitori. Questo passaggio consente al flusso di lavoro GitHub di usare l'entità servizio per l'autenticazione con il registro [contenitori](../container-registry/container-registry-auth-service-principal.md) e per eseguire il push e il pull di un'immagine Docker. 

Ottenere l'ID risorsa del registro contenitori. Sostituire il nome del registro nel comando [az acr show][az-acr-show] seguente:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Usare [az role assignment create][az-role-assignment-create] per assegnare il ruolo AcrPush, che fornisce l'accesso push e pull al Registro di sistema. Sostituire l'ID client dell'entità servizio:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Salvare le credenziali nel repository GitHub

1. Nell'interfaccia utente di GitHub passare al repository con fork e selezionare **Segreti**  >  **delle impostazioni.** 

1. Selezionare **Aggiungi un nuovo segreto** per aggiungere i segreti seguenti:

|Segreto  |Valore  |
|---------|---------|
|`AZURE_CREDENTIALS`     | L'intero output JSON del passaggio di creazione dell'entità servizio |
|`REGISTRY_LOGIN_SERVER`   | Nome del server di accesso del Registro di sistema (tutto minuscolo). Esempio: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  oggetto `clientId` dall'output JSON della creazione dell'entità servizio       |
|`REGISTRY_PASSWORD`     |  oggetto `clientSecret` dall'output JSON della creazione dell'entità servizio |
| `RESOURCE_GROUP` | Nome del gruppo di risorse usato per l'ambito dell'entità servizio |

### <a name="create-workflow-file"></a>Creare un file del flusso di lavoro

1. Nell'interfaccia utente di GitHub selezionare **Azioni**  >  **Nuovo flusso di lavoro.**
1. Selezionare **Configura un flusso di lavoro manualmente.**
1. In **Modifica nuovo file** incollare il contenuto YAML seguente per sovrascrivere il codice di esempio. Accettare il nome file `main.yml` predefinito o specificare un nome file scelto.
1. Selezionare **Avvia commit**, specificare facoltativamente descrizioni brevi ed estese del commit e selezionare Commit nuovo **file**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Convalidare il flusso di lavoro

Dopo aver eseguito il commit del file del flusso di lavoro, il flusso di lavoro viene attivato. Per esaminare lo stato del flusso di lavoro, passare ad **Azioni Flussi di**  >  **lavoro**. 

![Visualizzare lo stato del flusso di lavoro](./media/container-instances-github-action/github-action-progress.png)

Per [informazioni sulla visualizzazione dello](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) stato e dei risultati di ogni passaggio del flusso di lavoro, vedere Visualizzazione della cronologia di esecuzione del flusso di lavoro. Se il flusso di lavoro non viene completato, vedere [Visualizzazione dei log per diagnosticare gli errori.](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)

Quando il flusso di lavoro viene completato correttamente, ottenere informazioni sull'istanza del contenitore denominata *aci-sampleapp* eseguendo il [comando az container show.][az-container-show] Sostituire il nome del gruppo di risorse: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

L'output è simile a:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Dopo il provisioning dell'istanza, passare all'FQDN del contenitore nel browser per visualizzare l'app Web in esecuzione.

![Esecuzione dell'app Web nel browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Usare l'estensione Distribuisci in Azure

In alternativa, usare l'estensione [Distribuisci in Azure nell'interfaccia](https://github.com/Azure/deploy-to-azure-cli-extension) della riga di comando di Azure per configurare il flusso di lavoro. Il `az container app up` comando nell'estensione accetta parametri di input dall'utente per configurare un flusso di lavoro da distribuire in Istanze di Azure Container. 

Il flusso di lavoro creato dall'interfaccia della riga di comando di Azure è simile al flusso di lavoro che è [possibile creare manualmente usando GitHub.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>Prerequisito aggiuntivo

Oltre ai prerequisiti e [alla configurazione](#prerequisites) del [repo](#set-up-repo) per questo scenario, è necessario installare l'estensione  **Distribuisci** in Azure per l'interfaccia della riga di comando di Azure.

Eseguire il [comando az extension add][az-extension-add] per installare l'estensione:

```azurecli
az extension add \
  --name deploy-to-azure
```

Per informazioni su come trovare, installare e gestire le estensioni, vedere Usare le estensioni con l'interfaccia della riga di [comando di Azure.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>Eseguire `az container app up`

Per eseguire il [comando az container app up,][az-container-app-up] specificare almeno:

* Nome del Registro Azure Container, ad esempio *myregistry*
* L'URL del repository GitHub, ad esempio `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando di esempio:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Stato del comando

* Quando richiesto, specificare le credenziali di GitHub o un token di  accesso  personale (PAT) [GitHub](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) con ambito repository e utente per l'autenticazione con l'account GitHub. Se si specificano le credenziali di GitHub, il comando crea automaticamente un PAT. Seguire le istruzioni aggiuntive per configurare il flusso di lavoro.

* Il comando crea i segreti del repo per il flusso di lavoro:

  * Credenziali dell'entità servizio per l'interfaccia della riga di comando di Azure
  * Credenziali per accedere al Registro Azure Container

* Dopo che il comando ha eseguito il commit del file del flusso di lavoro nel proprio repo, il flusso di lavoro viene attivato. 

L'output è simile a:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Per visualizzare lo stato del flusso di lavoro e i risultati di ogni passaggio nell'interfaccia utente di GitHub, vedere [Visualizzazione della cronologia di esecuzione del flusso di lavoro.](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)

### <a name="validate-workflow"></a>Convalidare il flusso di lavoro

Il flusso di lavoro distribuisce un'istanza di contenitore di Azure con il nome di base del repository GitHub, in questo caso *acr-build-helloworld-node.* Quando il flusso di lavoro viene completato correttamente, ottenere informazioni sull'istanza del contenitore denominata *acr-build-helloworld-node* eseguendo il [comando az container show.][az-container-show] Sostituire il nome del gruppo di risorse: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

L'output è simile a:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Dopo il provisioning dell'istanza, passare all'FQDN del contenitore nel browser per visualizzare l'app Web in esecuzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Arrestare l'istanza del contenitore con il comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Per eliminare il gruppo di risorse e tutte le risorse in esso presenti, eseguire il [comando az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare [GitHub Marketplace per altre](https://github.com/marketplace?type=actions) azioni per automatizzare il flusso di lavoro di sviluppo


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/container/app#az_container_app_up
