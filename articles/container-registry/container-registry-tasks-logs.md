---
title: Visualizzare i log di esecuzione delle attività - Attività
description: Come visualizzare e gestire i log di esecuzione generati da ACR Tasks.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781056"
---
# <a name="view-and-manage-task-run-logs"></a>Visualizzare e gestire i log di esecuzione delle attività

Ogni attività eseguita in Registro Azure Container [genera](container-registry-tasks-overview.md) l'output del log che è possibile controllare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. 

Questo articolo illustra come visualizzare e gestire i log di esecuzione delle attività.

## <a name="view-streamed-logs"></a>Visualizzare i log trasmessi

Quando si attiva un'attività manualmente, l'output del log viene trasmesso direttamente alla console. Ad esempio, quando si attiva un'attività manualmente usando il comando [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run)o [az acr task run,](/cli/azure/acr/task#az_acr_task_run) l'output del log viene trasmesso alla console. 

Il comando [az acr run di](/cli/azure/acr#az_acr_run) esempio seguente attiva manualmente un'attività che esegue un contenitore estratto dallo stesso registro:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Log trasmesso:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Visualizzare i log archiviati 

Registro Azure Container archivia i log di esecuzione per tutte le attività. È possibile visualizzare i log di esecuzione archiviati nel portale di Azure. In caso contrario, usare [il comando az acr task logs](/cli/azure/acr/task#az_acr_task_logs) per visualizzare un log selezionato. Per impostazione predefinita, i log vengono conservati per 30 giorni.

Se un'attività viene attivata automaticamente, ad esempio da un  aggiornamento del codice sorgente, l'accesso ai log archiviati è l'unico modo per visualizzare i log di esecuzione. I trigger di attività automatici includono commit del codice sorgente o richieste pull, aggiornamenti dell'immagine di base e trigger timer.

Per visualizzare i log di esecuzione nel portale:

1. Passare al registro contenitori.
1. In **Servizi** selezionare **Attività**  >  **Esegue**.
1. Selezionare un **ID esecuzione** per visualizzare lo stato dell'esecuzione e i log di esecuzione. Il log contiene le stesse informazioni di un log in streaming, se ne viene generato uno.

![Visualizzare il portale di accesso per l'esecuzione di attività](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Per visualizzare un log usando l'interfaccia della riga di comando di Azure, eseguire [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) e specificare un ID esecuzione, un nome di attività o un'immagine specifica creata da un'attività di compilazione. Se viene specificato un nome di attività, il comando visualizza il log per l'ultima esecuzione creata.

Nell'esempio seguente viene restituito il log per l'esecuzione con ID *cf4:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Archiviazione log alternativa

È possibile archiviare i log di esecuzione delle attività in un file system locale o usare una soluzione di archiviazione alternativa, ad esempio Archiviazione di Azure.

Ad esempio, creare una directory *tasklogs* locale e reindirizzare l'output dei log attività [az acr](/cli/azure/acr/task#az_acr_task_logs) a un file locale:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

È anche possibile salvare i file di log locali Archiviazione di Azure. Ad esempio, usare l'interfaccia della riga di comando di [Azure,](../storage/blobs/storage-quickstart-blobs-cli.md) [portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md)o altri metodi per caricare file in un account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [Registro Azure Container attività](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
