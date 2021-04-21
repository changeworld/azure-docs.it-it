---
title: DA CI/CD a contenitori personalizzati
description: Configurare la distribuzione continua in un contenitore Windows o Linux personalizzato in Servizio app di Azure.
keywords: servizio app di azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782604"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Distribuzione continua con contenitori personalizzati in Servizio app di Azure

In questa esercitazione verrà configurata la distribuzione continua per un'immagine personalizzata del contenitore da repository gestite del [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Passare al Centro distribuzione

Nel [portale di Azure](https://portal.azure.com)passare alla pagina di gestione per l'app del servizio app.

Scegliere Impostazioni centro distribuzione dal menu  >  **a sinistra.** 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Scegliere l'origine della distribuzione

**La** scelta dell'origine della distribuzione dipende dallo scenario:
- **Registro contenitori** configura ci/CD tra il registro contenitori e il servizio app.
- **L GitHub Actions'opzione** è disponibile se si gestisce il codice sorgente per l'immagine del contenitore in GitHub. Attivata dai nuovi commit nel repository GitHub, l'azione di distribuzione può essere eseguita e direttamente nel registro contenitori, quindi aggiornare l'app del servizio app per eseguire `docker build` `docker push` la nuova immagine. Per altre informazioni, vedere [Funzionamento di CI/CD con GitHub Actions](#how-cicd-works-with-github-actions).
- Per configurare CI/CD con **Azure Pipelines**, vedere Distribuire [un contenitore](/azure/devops/pipelines/targets/webapp-on-container-linux)di app Web di Azure da Azure Pipelines .

> [!NOTE]
> Per un'app Docker Compose, selezionare **Registro contenitori**.

Se si sceglie GitHub Actions, fare **clic su** **Autorizza e** seguire le richieste di autorizzazione. Se gitHub è già stato autorizzato in precedenza, è possibile eseguire la distribuzione dal repository di un altro utente facendo clic **su Cambia account**.

Dopo aver autorizzato l'account Azure con GitHub, **selezionare** **l'organizzazione,** **il repository** e il **ramo** da cui eseguire la distribuzione.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Configurare le impostazioni del Registro di sistema
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Configurare le impostazioni del Registro di sistema

Per distribuire un'app multi-contenitore (Docker Compose), selezionare Docker Compose  **in** **Tipo di contenitore**.

Se l'elenco a discesa Tipo di **contenitore** non è visualizzato, scorrere verso l'alto fino a **Origine** e **selezionare** **Registro Contenitori.**
::: zone-end

In **Origine registro** selezionare la **posizione** del registro contenitori. Se non è né Registro Azure Container né Docker Hub, selezionare Private Registry **(Registro** **privato).**

::: zone pivot="container-linux"
> [!NOTE]
> Se l'app multi-contenitore (Docker Compose) usa più di un'immagine privata, assicurarsi che le immagini private siano nello stesso registro privato e accessibili con le stesse credenziali utente. Se l'app multi-contenitore usa solo immagini **pubbliche,** **selezionare Docker Hub**, anche se alcune immagini non sono in Docker Hub.
::: zone-end  

Seguire i passaggi successivi selezionando la scheda corrispondente a quella scelta.

# <a name="azure-container-registry"></a>[Registro Azure Container](#tab/acr)

**L'elenco** a discesa Registro di sistema visualizza i registri nella stessa sottoscrizione dell'app. **Selezionare** il registro desiderato.

> [!NOTE]
> Per eseguire la distribuzione da un registro in una sottoscrizione diversa, **selezionare invece** **Registro privato** **nell'origine registro.**

::: zone pivot="container-windows"
**Selezionare** **l'immagine** e **il tag** da distribuire. Se si vuole, **digitare** il comando di avvio in **File di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda di **Tipo di contenitore:**
- Per **Docker Compose**, **selezionare il** registro per le immagini private. **Fare** **clic su Scegli file** per caricare  il [file Docker Compose](https://docs.docker.com/compose/compose-file/)o semplicemente incollare il contenuto del file Docker Compose in **Config**.
- Per **Contenitore singolo** selezionare **l'immagine e** il **tag** da distribuire.  Se si vuole, **digitare il** comando di avvio in File **di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa nel **file di** avvio alla fine del comando [ `docker run` (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) all'avvio del contenitore.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
In **Accesso al** repository selezionare **se** l'immagine da distribuire è pubblica o privata.
::: zone-end
::: zone pivot="container-linux"
In **Accesso al repository** selezionare **se** l'immagine da distribuire è pubblica o privata. Per un Docker Compose app con una o più immagini private, **selezionare** **Privato.**
::: zone-end

Se si seleziona un'immagine privata, **specificare** **l'account di** accesso (nome utente) **e la password** dell'account Docker.

::: zone pivot="container-windows"
**Specificare** l'immagine e il nome del tag in Nome immagine **completo e Tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se si vuole, **digitare il** comando di avvio in File **di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda del tipo **di contenitore**:
- Per **Docker Compose**, **selezionare il** registro per le immagini private. **Fare** **clic su Scegli file** per caricare  [Docker Compose file](https://docs.docker.com/compose/compose-file/)o semplicemente incollare il contenuto del file Docker Compose file in **Config**.
- Per **Contenitore singolo**, **specificare il** nome dell'immagine e del tag in Nome immagine completo e **Tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se si vuole, **digitare il** comando di avvio in File **di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa in **File di** avvio alla fine del [comando `docker run` (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) all'avvio del contenitore.

# <a name="private-registry"></a>[Registro privato](#tab/private)

In **URL server** digitare **l'URL** del server, a partire **da https://**.

In **Account di** accesso e **Password** **digitare** le credenziali di accesso per il registro privato.

::: zone pivot="container-windows"
**Specificare** l'immagine e il nome del tag in Nome immagine **completo e Tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se si vuole, **digitare il** comando di avvio in File **di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda del tipo **di contenitore**:
- Per **Docker Compose**, **selezionare il** registro per le immagini private. **Fare** **clic su Scegli file** per caricare  [Docker Compose file](https://docs.docker.com/compose/compose-file/)o semplicemente incollare il contenuto del file Docker Compose file in **Config**.
- Per **Contenitore singolo**, **specificare il** nome dell'immagine e del tag in Nome immagine completo e **Tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se si vuole, **digitare il** comando di avvio in File **di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa in **File di** avvio alla fine del [comando `docker run` (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) all'avvio del contenitore.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Abilitare CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Abilitare CI/CD
::: zone-end

Il servizio app supporta l'integrazione ci/CD con Registro Azure Container e Docker Hub. Per abilitarlo, **selezionare Attivato**  nella **distribuzione continua.**

::: zone pivot="container-linux"
> [!NOTE]
> Se si seleziona **GitHub Actions** in **Origine**, non si ottiene questa opzione perché ci/CD viene gestito direttamente da GitHub Actions. Viene invece visualizzata una sezione Configurazione flusso **di lavoro,** in cui è possibile fare clic **su** **File di anteprima** per esaminare il file del flusso di lavoro. Azure esegue il commit di questo file nel repository di origine GitHub selezionato per gestire le attività di compilazione e distribuzione. Per altre informazioni, vedere [Funzionamento di CI/CD con GitHub Actions](#how-cicd-works-with-github-actions).
::: zone-end

Quando si abilita questa opzione, il servizio app aggiunge un webhook al repository in Registro Azure Container o Docker Hub. Il repository pubblica questo webhook ogni volta che l'immagine selezionata viene aggiornata con `docker push` . Il webhook fa sì che l'app del servizio app venga riavviata ed eseguita `docker pull` per ottenere l'immagine aggiornata.

**Per altri registri privati,** è possibile pubblicare nel webhook manualmente o come passaggio in una pipeline CI/CD. In **URL webhook** fare **clic sul** pulsante **Copia** per ottenere l'URL del webhook.

::: zone pivot="container-linux"
> [!NOTE]
> Il supporto per le app multi-contenitore (Docker Compose) è limitato: 
> - Ad Registro Azure Container, il servizio app crea un webhook nel Registro di sistema selezionato con il Registro di sistema come ambito. Un a qualsiasi repository nel Registro di sistema (inclusi quelli a cui non fa riferimento il file Docker Compose) attiva `docker push` un riavvio dell'app. È possibile modificare [il webhook in](../container-registry/container-registry-webhook.md) un ambito più ristretto.
> - Docker Hub non supporta webhook a livello di registro. È necessario **aggiungere** manualmente i webhook alle immagini specificate nel file Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Salvare le impostazioni
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Salvare le impostazioni
::: zone-end

**Fare clic** **su Salva**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Funzionamento di CI/CD con GitHub Actions

Se si **sceglie** GitHub Actions in **Origine** (vedere Scegliere l'origine della [distribuzione),](#2-choose-deployment-source)il servizio app configura CI/CD nei modi seguenti:

- Deposita un file GitHub Actions flusso di lavoro nel repository GitHub per gestire le attività di compilazione e distribuzione nel servizio app.
- Aggiunge le credenziali per il registro privato come segreti GitHub. Il file del flusso di lavoro generato esegue [l'azione Azure/docker-login](https://github.com/Azure/docker-login) per accedere con il registro privato, quindi esegue `docker push` per distribuirlo.
- Aggiunge il profilo di pubblicazione per l'app come segreto GitHub. Il file del flusso di lavoro generato usa questo segreto per l'autenticazione con il servizio app, quindi esegue l'azione [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) per configurare l'immagine aggiornata, che attiva un riavvio dell'app per eseguire il pull dell'immagine aggiornata.
- Acquisisce informazioni dai log di [esecuzione del flusso di](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) lavoro e le visualizza nella **scheda** Log nel Centro **distribuzione dell'app.**

È possibile personalizzare il provider GitHub Actions di compilazione nei modi seguenti:

- Personalizzare il file del flusso di lavoro dopo che è stato generato nel repository GitHub. Per altre informazioni, vedere [Sintassi del flusso di lavoro per GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). È sufficiente assicurarsi che il flusso di lavoro termini con [l'azione Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) per attivare un riavvio dell'app.
- Se il ramo selezionato è protetto, è comunque possibile visualizzare in anteprima il file del flusso di lavoro senza salvare la configurazione, quindi aggiungerlo e i segreti di GitHub necessari nel repository manualmente. Questo metodo non consente l'integrazione dei log con il portale di Azure.
- Anziché un profilo di pubblicazione, eseguire la distribuzione usando [un'entità](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) servizio in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Eseguire l'autenticazione con un'entità servizio

Questa configurazione facoltativa sostituisce l'autenticazione predefinita con i profili di pubblicazione nel file del flusso di lavoro generato.

**Generare** un'entità servizio con [il comando az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) nell'interfaccia della riga [di comando di Azure.](/cli/azure/) Nell'esempio seguente sostituire *\<subscription-id>* , e con valori *\<group-name>* *\<app-name>* personalizzati. **Salvare** l'intero output JSON per il passaggio successivo, incluso il primo `{}` livello.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Per motivi di sicurezza, concedere l'accesso minimo richiesto all'entità servizio. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

In [GitHub](https://github.com/)passare **al** repository, quindi **selezionare** Impostazioni > segreti > Aggiungi un **nuovo segreto.** **Incollare** l'intero output JSON dal comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. **Assegnare** al segreto un nome come `AZURE_CREDENTIALS` .

Nel file del flusso di lavoro generato dal **Centro distribuzione** **rivedere** il passaggio con codice `azure/webapps-deploy` simile all'esempio seguente:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatizzare con l'interfaccia della riga di comando

Per configurare il registro contenitori e l'immagine Docker, **eseguire** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set).

# <a name="azure-container-registry"></a>[Registro Azure Container](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Registro privato](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Per configurare un'app multi-contenitore (Docker Compose), preparare un file  Docker Compose in locale, quindi eseguire [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) con il parametro  `--multicontainer-config-file` . Se il file Docker Compose contiene immagini private, **aggiungere parametri** come `--docker-registry-server-*` illustrato nell'esempio precedente.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Per configurare ci/CD dal registro contenitori all'app, **eseguire** [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) con il parametro `--enable-cd` . Il comando restituisce l'URL del webhook, ma è necessario creare manualmente il webhook nel Registro di sistema in un passaggio separato. L'esempio seguente abilita CI/CD nell'app, quindi usa l'URL del webhook nell'output per creare il webhook in Registro Azure Container.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Altre risorse

* [Registro Azure Container](https://azure.microsoft.com/services/container-registry/)
* [Creare un'app Web .NET Core nel Servizio app in Linux](quickstart-dotnetcore.md)
* [Avvio rapido: Eseguire un contenitore personalizzato nel servizio app](quickstart-custom-container.md)
* [Domande frequenti sul servizio app in Linux](faq-app-service-linux.md)
* [Configurare contenitori personalizzati](configure-custom-container.md)
* [Flussi di lavoro delle azioni da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)
