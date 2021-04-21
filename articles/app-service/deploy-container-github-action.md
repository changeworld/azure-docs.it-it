---
title: Ci/CD del contenitore personalizzato GitHub Actions
description: Informazioni su come usare GitHub Actions per distribuire il contenitore Linux personalizzato nel servizio app da una pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789430"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Eseguire la distribuzione di un contenitore personalizzato nel servizio app usando GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) offre la flessibilità necessaria per creare un flusso di lavoro di sviluppo software automatizzato. Con [l'azione Distribuzione Web Azure](https://github.com/Azure/webapps-deploy)è possibile automatizzare il flusso di lavoro per distribuire contenitori personalizzati nel servizio app [usando](overview.md) GitHub Actions.

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e parametri presenti nel flusso di lavoro.

Per un flusso Servizio app di Azure del contenitore, il file include tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Recuperare un'entità servizio o un profilo di pubblicazione. <br /> 2. Creare un segreto GitHub. |
|**Compila** | 1. Creare l'ambiente. <br /> 2. Compilare l'immagine del contenitore. |
|**Distribuzione** | 1. Distribuire l'immagine del contenitore. |

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join). È necessario avere codice in un repository GitHub per la distribuzione in Servizio app di Azure. 
- Un registro contenitori funzionante e Servizio app di Azure'app per i contenitori. In questo esempio viene Registro Azure Container. Assicurarsi di completare la distribuzione completa per Servizio app di Azure contenitori. A differenza delle normali app Web, le app Web per i contenitori non hanno una pagina di destinazione predefinita. Pubblicare il contenitore per avere un esempio funzionante.
    - [Informazioni su come creare un'applicazione Node.js contenitore usando Docker, eseguire il push dell'immagine del contenitore in un registro e quindi distribuire l'immagine in Servizio app di Azure](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

Il modo consigliato per eseguire l'autenticazione con app Azure Services for GitHub Actions è con un profilo di pubblicazione. È anche possibile eseguire l'autenticazione con un'entità servizio, ma il processo richiede più passaggi. 

Salvare le credenziali del profilo di pubblicazione o l'entità servizio come segreto [GitHub per](https://docs.github.com/en/actions/reference/encrypted-secrets) l'autenticazione con Azure. Si accederà al segreto all'interno del flusso di lavoro. 

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

Un profilo di pubblicazione è una credenziale a livello di app. Configurare il profilo di pubblicazione come segreto GitHub. 

1. Passare al servizio app nel portale di Azure. 

1. Nella pagina **Panoramica** selezionare **Ottieni profilo di pubblicazione**.

    > [!NOTE]
    > A partire da ottobre 2020, le app Web Linux dovranno impostare l'app su prima di `WEBSITE_WEBDEPLOY_USE_SCM` `true` **scaricare il file**. Questo requisito verrà rimosso in futuro. Per [informazioni su come configurare le impostazioni comuni dell'app Web, vedere](./configure-common.md)Configurare un'app del servizio app nel portale di Azure .  

1. Salvare il file scaricato. Si userà il contenuto del file per creare un segreto GitHub.

# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Nell'esempio sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app. Copiare l'oggetto JSON per un uso successivo.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

---
## <a name="configure-the-github-secret-for-authentication"></a>Configurare il segreto GitHub per l'autenticazione

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto.**

Per usare [le credenziali a livello di app,](#generate-deployment-credentials)incollare il contenuto del file del profilo di pubblicazione scaricato nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_WEBAPP_PUBLISH_PROFILE` .

Quando si configura il flusso di lavoro GitHub, si usa `AZURE_WEBAPP_PUBLISH_PROFILE` nell'azione distribuisci app Web di Azure. Ad esempio:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > segreti > Aggiungi un nuovo segreto.**

Per usare [le credenziali a livello di utente,](#generate-deployment-credentials)incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome , ad esempio `AZURE_CREDENTIALS` .

Quando in seguito si configura il file del flusso di lavoro, si usa il segreto come `creds` di input dell'azione di accesso di Azure. Ad esempio:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Configurare i segreti GitHub per il registro

Definire i segreti da usare con l'azione Docker Login. L'esempio in questo documento usa Registro Azure Container per il registro contenitori. 

1. Passare al contenitore nell'portale di Azure o in Docker e copiare il nome utente e la password. È possibile trovare il nome Registro Azure Container e la password nella portale di Azure **impostazioni** Chiavi di  >  **accesso** per il registro. 

2. Definire un nuovo segreto per il nome utente del registro denominato `REGISTRY_USERNAME` . 

3. Definire un nuovo segreto per la password del Registro di sistema denominata `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

L'esempio seguente mostra parte del flusso di lavoro che compila un'Node.JS'immagine Docker. Usare [Docker Login per](https://github.com/azure/docker-login) accedere a un registro contenitori privato. Questo esempio usa Registro Azure Container ma la stessa azione funziona per altri registri. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

È anche possibile usare [Docker Login](https://github.com/azure/docker-login) per accedere a più registri contenitori contemporaneamente. Questo esempio include due nuovi segreti GitHub per l'autenticazione con docker.io. L'esempio presuppone che sia presente un Dockerfile a livello radice del registro. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Eseguire la distribuzione in un contenitore del servizio app

Per distribuire l'immagine in un contenitore personalizzato nel servizio app, usare `azure/webapps-deploy@v2` l'azione . Questa azione ha sette parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **app-name** | (Obbligatorio) Nome dell'app del servizio app | 
| **publish-profile** | (Facoltativo) Si applica alle app Web (Windows e Linux) e ai contenitori di app Web (linux). Scenario multi-contenitore non supportato. Contenuto del file del profilo di pubblicazione (con estensione \* publishsettings) con Distribuzione Web segreti | 
| **slot-name** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |
| **package** | (Facoltativo) Si applica solo all'app Web: percorso del pacchetto o della cartella. \*.zip, \* .war, \* .jar o una cartella da distribuire |
| **images** | (Obbligatorio) Si applica solo ai contenitori di app Web: specificare il nome completo delle immagini del contenitore. Ad esempio, "myregistry.azurecr.io/nginx:latest" o "python:3.7.2-alpine/". Per un'app multi-contenitore, è possibile specificare più nomi di immagine del contenitore (separati da più righe) |
| **file di configurazione** | (Facoltativo) Si applica solo ai contenitori di app Web: percorso del file Docker-Compose web. Deve essere un percorso completo o relativo alla directory di lavoro predefinita. Obbligatorio per le app multi-contenitore. |
| **startup-command** | (Facoltativo) Immettere il comando di avvio. Ad esempio, dotnet run o dotnet filename.dll |

# <a name="publish-profile"></a>[Profilo di pubblicazione](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Entità servizio](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Passaggi successivi

Il set di azioni raggruppate in repository diversi è disponibile in GitHub. Ogni repository contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Flussi di lavoro delle azioni da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)

- [Accesso ad Azure](https://github.com/Azure/login)

- [App Web di Azure](https://github.com/Azure/webapps-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventi che attivano i flussi di lavoro](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [Distribuzione Kubernetes](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro introduttivi](https://github.com/actions/starter-workflows)