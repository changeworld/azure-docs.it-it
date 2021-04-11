---
title: Integrazione continua/distribuzione continua per contenitori personalizzati
description: Configurare la distribuzione continua in un contenitore Windows o Linux personalizzato nel servizio app Azure.
keywords: servizio app di azure, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643346"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Distribuzione continua con contenitori personalizzati nel servizio app Azure

In questa esercitazione verrà configurata la distribuzione continua per un'immagine personalizzata del contenitore da repository gestite del [Registro Azure Container](https://azure.microsoft.com/services/container-registry/) o dall'[hub Docker](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Vai al centro distribuzione

Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

Nel menu a sinistra fare clic su impostazioni di **Deployment Center**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. scegliere l'origine della distribuzione

**Scegliere** l'origine della distribuzione dipende dallo scenario:
- **Registro contenitori** consente di configurare ci/CD tra il registro contenitori e il servizio app.
- L'opzione **GitHub actions** è relativa all'utente se si gestisce il codice sorgente per l'immagine del contenitore in GitHub. Attivato da nuovi commit nel repository GitHub, l'azione di distribuzione può `docker build` essere eseguita e `docker push` direttamente nel registro contenitori, quindi aggiornare l'app del servizio app per eseguire la nuova immagine. Per altre informazioni, vedere [come ci/CD funziona con le azioni di GitHub](#how-cicd-works-with-github-actions).
- Per configurare CI/CD con **Azure Pipelines**, vedere [distribuire un contenitore di app Web di Azure da Azure Pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Per un'app Docker Compose, selezionare **container Registry**.

Se si scelgono le azioni di GitHub, **fare clic su** **autorizza** e seguire le istruzioni di autorizzazione. Se è già stata eseguita l'autorizzazione con GitHub in precedenza, è possibile eseguire la distribuzione da un repository di un altro utente facendo clic su **modifica account**.

Dopo aver autorizzato l'account Azure con GitHub, **selezionare** l' **organizzazione**, il **repository** e il **ramo** da cui eseguire la distribuzione.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. configurare le impostazioni del registro di sistema
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. configurare le impostazioni del registro di sistema

Per distribuire un'app con più contenitori (Docker Compose), **selezionare** **Docker compose** in **tipo di contenitore**.

Se l'elenco a discesa **tipo di contenitore** non è visibile, scorrere il backup in **origine** e **selezionare** **container Registry**.
::: zone-end

In **origine del registro** di sistema **selezionare** la posizione del registro contenitori. Se non è né Azure Container Registry né l'hub Docker, **selezionare** **Registro di sistema privato**.

::: zone pivot="container-linux"
> [!NOTE]
> Se l'app multicontenitore (Docker Compose) USA più di un'immagine privata, assicurarsi che le immagini private si trovino nello stesso registro privato e accessibili con le stesse credenziali utente. Se l'app a più contenitori usa solo immagini pubbliche, **selezionare** **Docker Hub**, anche se alcune immagini non sono nell'hub docker.
::: zone-end  

Seguire i passaggi successivi selezionando la scheda corrispondente alla propria scelta.

# <a name="azure-container-registry"></a>[Registro Azure Container](#tab/acr)

Nell'elenco a discesa **del registro di sistema** vengono visualizzati i registri nella stessa sottoscrizione dell'app. **Selezionare** il registro di sistema desiderato.

> [!NOTE]
> Per eseguire la distribuzione da un registro in una sottoscrizione diversa, **selezionare** invece **Registro privato** nell' **origine del registro di sistema** .

::: zone pivot="container-windows"
**Selezionare** l' **immagine** e il **tag** da distribuire. Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda del **tipo di contenitore**:
- Per **Docker compose** **selezionare** il registro di sistema per le immagini private. **Fare clic su** **Choose file** per caricare il [file di Docker compose](https://docs.docker.com/compose/compose-file/)o semplicemente **incollare** il contenuto del file di Docker compose in **config**.
- Per un **singolo contenitore**, **selezionare** l' **immagine** e il **tag** da distribuire. Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa nel **file di avvio** alla [fine del `docker run` comando (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) quando si avvia il contenitore.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
In **accesso** al repository **selezionare** se l'immagine da distribuire è pubblica o privata.
::: zone-end
::: zone pivot="container-linux"
In **accesso** al repository **selezionare** se l'immagine da distribuire è pubblica o privata. Per un'app Docker Compose con una o più immagini private, **selezionare** **privato**.
::: zone-end

Se si seleziona un'immagine privata, **specificare** l'account di **accesso** (username) e la **password** dell'account docker.

::: zone pivot="container-windows"
**Specificare** il nome dell'immagine e del tag in **nome immagine completo e tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda del **tipo di contenitore**:
- Per **Docker compose** **selezionare** il registro di sistema per le immagini private. **Fare clic su** **Choose file** per caricare il [file di Docker compose](https://docs.docker.com/compose/compose-file/)o semplicemente **incollare** il contenuto del file di Docker compose in **config**.
- Per un **singolo contenitore**, **specificare** l'immagine e il nome del tag in **nome immagine completo e tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa nel **file di avvio** alla [fine del `docker run` comando (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) quando si avvia il contenitore.

# <a name="private-registry"></a>[Registro privato](#tab/private)

In **URL server** **digitare** l'URL del server, a partire da **https://**.

In **account di accesso** e **password** **digitare** le credenziali di accesso per il registro privato.

::: zone pivot="container-windows"
**Specificare** il nome dell'immagine e del tag in **nome immagine completo e tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end
::: zone pivot="container-linux"
Seguire il passaggio successivo a seconda del **tipo di contenitore**:
- Per **Docker compose** **selezionare** il registro di sistema per le immagini private. **Fare clic su** **Choose file** per caricare il [file di Docker compose](https://docs.docker.com/compose/compose-file/)o semplicemente **incollare** il contenuto del file di Docker compose in **config**.
- Per un **singolo contenitore**, **specificare** l'immagine e il nome del tag in **nome immagine completo e tag**, separati da un `:` (ad esempio, `nginx:latest` ). Se lo si desidera, **digitare** il comando di avvio nel **file di avvio**. 
::: zone-end

Il servizio app aggiunge la stringa nel **file di avvio** alla [fine del `docker run` comando (come `[COMMAND] [ARG...]` segmento)](https://docs.docker.com/engine/reference/run/) quando si avvia il contenitore.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. abilitare CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. abilitare CI/CD
::: zone-end

Il servizio app supporta l'integrazione CI/CD con Container Registry di Azure e l'hub docker. Per abilitarla, **selezionare** **on** in **distribuzione continua**.

::: zone pivot="container-linux"
> [!NOTE]
> Se si seleziona **azioni GitHub** nell' **origine**, questa opzione non viene selezionata perché ci/CD è gestito direttamente da azioni github. Viene invece visualizzata una sezione di **configurazione del flusso di lavoro** , in cui è possibile **fare clic su** **file di anteprima** per esaminare il file del flusso di lavoro. Azure esegue il commit di questo file nel repository di origine GitHub selezionato per gestire le attività di compilazione e distribuzione. Per altre informazioni, vedere [come ci/CD funziona con le azioni di GitHub](#how-cicd-works-with-github-actions).
::: zone-end

Quando si abilita questa opzione, il servizio app aggiunge un webhook al repository in Azure Container Registry o nell'hub docker. Il repository invia il post a questo webhook ogni volta che l'immagine selezionata viene aggiornata con `docker push` . Il webhook fa in modo che l'app del servizio app venga riavviata ed eseguita `docker pull` per ottenere l'immagine aggiornata.

**Per altri registri privati**, il può pubblicare il post sul webhook manualmente o come passaggio in una pipeline di integrazione continua/distribuzione continua. In **URL webhook** **fare clic** sul pulsante **copia** per ottenere l'URL del webhook.

::: zone pivot="container-linux"
> [!NOTE]
> Il supporto per le app a più contenitori (Docker Compose) è limitato: 
> - Per Container Registry di Azure, il servizio app crea un webhook nel registro di sistema selezionato con il registro di sistema come ambito. Un oggetto `docker push` a qualsiasi repository nel registro di sistema (inclusi quelli a cui non viene fatto riferimento dal file di Docker compose) attiva un riavvio dell'app. Potrebbe essere necessario [modificare il webhook](../container-registry/container-registry-webhook.md) in un ambito più ristretto.
> - Docker Hub non supporta i webhook a livello del registro di sistema. È necessario **aggiungere** i webhook manualmente alle immagini specificate nel file di Docker compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. salvare le impostazioni
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. salvare le impostazioni
::: zone-end

**Fare clic su** **Salva**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Funzionamento dell'integrazione continua/distribuzione continua con le azioni di GitHub

Se si scelgono le **azioni GitHub** nell' **origine** (vedere [scegliere l'origine della distribuzione](#2-choose-deployment-source)), il servizio app configura ci/CD nei modi seguenti:

- Deposita un file del flusso di lavoro di azioni GitHub nel repository GitHub per gestire le attività di compilazione e distribuzione nel servizio app.
- Aggiunge le credenziali per il registro privato come segreti GitHub. Il file del flusso di lavoro generato esegue l'azione [Azure/Docker-login](https://github.com/Azure/docker-login) per accedere al registro privato, quindi esegue `docker push` per la distribuzione.
- Aggiunge il profilo di pubblicazione per l'app come segreto GitHub. Il file del flusso di lavoro generato usa questo segreto per l'autenticazione con il servizio app, quindi esegue l'azione [Azure/webapps-Distribuisci](https://github.com/Azure/webapps-deploy) per configurare l'immagine aggiornata, che attiva il riavvio dell'app per eseguire il pull dell'immagine aggiornata.
- Acquisisce le informazioni dai [log di esecuzione del flusso di lavoro](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) e le Visualizza nella scheda **log** del **centro distribuzione** dell'app.

È possibile personalizzare il provider di compilazione azioni di GitHub nei modi seguenti:

- Personalizzare il file del flusso di lavoro dopo che è stato generato nel repository GitHub. Per altre informazioni, vedere [sintassi del flusso di lavoro per le azioni di GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). È sufficiente assicurarsi che il flusso di lavoro termini con l'azione [Azure/webapps-Distribuisci](https://github.com/Azure/webapps-deploy) per attivare un riavvio dell'app.
- Se il ramo selezionato è protetto, è ancora possibile visualizzare l'anteprima del file del flusso di lavoro senza salvare la configurazione, quindi aggiungerla manualmente e i segreti GitHub necessari nel repository. Questo metodo non fornisce l'integrazione dei log con la portale di Azure.
- Anziché un profilo di pubblicazione, eseguire la distribuzione usando un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Eseguire l'autenticazione con un'entità servizio

Questa configurazione facoltativa sostituisce l'autenticazione predefinita con i profili di pubblicazione nel file del flusso di lavoro generato.

**Generare** un'entità servizio con il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](/cli/azure/). Nell'esempio seguente sostituire *\<subscription-id>* , *\<group-name>* e *\<app-name>* con i propri valori. **Salvare** l'intero output JSON per il passaggio successivo, incluso il primo livello `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Per la sicurezza, concedere l'accesso minimo necessario all'entità servizio. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

In [GitHub](https://github.com/) **passare** al repository, quindi **selezionare** **Impostazioni > Secrets > aggiungere un nuovo segreto**. **Incollare** l'intero output JSON dal comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. **Assegnare** al segreto un nome come `AZURE_CREDENTIALS` .

Nel file del flusso di lavoro generato da **Deployment Center**, **rivedere** il `azure/webapps-deploy` passaggio con codice simile a quello riportato nell'esempio seguente:

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

Per configurare il registro contenitori e l'immagine Docker, **eseguire** il comando [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set).

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
Per configurare un'app per più contenitori (Docker Compose), **preparare** un file di Docker compose localmente, quindi **eseguire** il comando [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) con il `--multicontainer-config-file` parametro. Se il file di Docker Compose contiene immagini private, **aggiungere** `--docker-registry-server-*` parametri, come illustrato nell'esempio precedente.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Per configurare l'integrazione continua/distribuzione continua dal registro contenitori all'app, **eseguire** il comando [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) con il `--enable-cd` parametro. Il comando restituisce l'URL del webhook, ma è necessario creare manualmente il webhook nel registro di sistema in un passaggio separato. L'esempio seguente abilita l'integrazione continua/recapito continuo nell'app, quindi usa l'URL del webhook nell'output per creare il webhook in Azure Container Registry.

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
* [Azioni flussi di lavoro da distribuire in Azure](https://github.com/Azure/actions-workflow-samples)
