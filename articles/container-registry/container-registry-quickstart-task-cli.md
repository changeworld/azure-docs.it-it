---
title: Avvio rapido - Creare un'immagine del contenitore su richiesta in Azure
description: Usare i comandi di Registro Azure Container per svolgere rapidamente attività di creazione, push ed esecuzione di un'immagine del contenitore Docker su richiesta nel cloud Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d3ef126812cb36874b74c8569d141787f71a29da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781452"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Avvio rapido: Compilare ed eseguire un'immagine del contenitore con Attività del Registro Azure Container

In questo argomento di avvio rapido si usano i comandi di [Attività del Registro Azure Container][container-registry-tasks-overview] per svolgere rapidamente attività di creazione, push ed esecuzione di un'immagine del contenitore Docker in modalità nativa in Azure, senza un'installazione locale di Docker. Attività del Registro Azure Container è un gruppo di funzionalità incluse in Registro Azure Container che consente di gestire e modificare le immagini del contenitore per tutto il ciclo di vita del contenitore. Questo esempio illustra come eseguire l'offload del ciclo di sviluppo dell'immagine del contenitore del "ciclo interno" nel cloud con compilazioni su richiesta usando un documento Dockerfile locale. 

Al termine di questo argomento di avvio rapido, è possibile esplorare funzionalità più avanzate di Attività del Registro Azure Container usando le [esercitazioni](container-registry-tutorial-quick-task.md). Attività del Registro Azure Container consente di automatizzare le compilazioni di immagini basate su commit del codice o aggiornamento delle immagini di base oppure di testare più contenitori in parallelo, tra gli altri scenari. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.58 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se non è già disponibile un registro contenitori, creare prima un gruppo di risorse con il comando [az group create][az-group-create]. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creare un registro contenitori

Creare un registro contenitori con il comando [az acr create][az-acr-create]. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Nell'esempio seguente viene usato il nome *myContainerRegistry008*. Aggiornarlo a un valore univoco.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

In questo esempio viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [Livelli di servizio del registro contenitori][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Compilare un'immagine da un Dockerfile ed eseguirne il push

A questo punto usare Registro Azure Container per compilare un'immagine ed eseguirne il push. Creare prima di tutto prima una directory di lavoro locale e quindi creare un documento Dockerfile denominato *Dockerfile* che contiene la riga seguente: `FROM mcr.microsoft.com/hello-world`. Questo è un semplice esempio relativo alla compilazione di un'immagine del contenitore Linux dall'immagine `hello-world` ospitata in Registro Microsoft Container. È possibile creare un Dockerfile standard personalizzato e compilare immagini per altre piattaforme. Se si sta lavorando con una shell bash, usare il comando seguente per creare il Dockerfile:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Eseguire il comando [az acr build][az-acr-build], che consente di compilare l'immagine e, dopo la corretta compilazione, ne esegue il push nel registro. L'esempio seguente mostra come compilare l'immagine `sample/hello-world:v1` ed eseguirne il push. Il carattere `.` alla fine del comando consente di impostare il percorso del Dockerfile, in questo caso la directory corrente.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

L'output restituito in seguito a un'operazione di compilazione e push riuscita è simile al seguente:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Eseguire l'immagine

A questo punto è possibile eseguire rapidamente l'immagine compilata di cui è stato eseguito il push nel registro. Usare [az acr run][az-acr-run] per eseguire il comando del contenitore. Nel flusso di lavoro per lo sviluppo del contenitore, questa operazione potrebbe essere un passaggio di convalida prima della distribuzione dell'immagine oppure si potrebbe includere il comando in un file [YAML in più passaggi][container-registry-tasks-multi-step]. 

Nell'esempio seguente viene usato `$Registry` per specificare il registro in cui eseguire il comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Il parametro `cmd` di questo esempio esegue il contenitore nella configurazione predefinita, ma `cmd` supporta parametri aggiuntivi di `docker run` o anche altri comandi di `docker`.

L'output è simile al seguente:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse, il registro contenitori e le immagini del contenitore in esso archiviate non sono più necessari, è possibile usare il comando [az group delete][az-group-delete] per rimuoverli.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono state usate le funzionalità di Attività del Registro Azure Container per compilare, eseguire il push ed eseguire un'immagine del contenitore Docker in modalità nativa in Azure, senza un'installazione locale di Docker. Procedere con le esercitazioni su Attività del Registro Azure Container per informazioni su come usare queste funzionalità per automatizzare compilazioni e aggiornamenti delle immagini.

> [!div class="nextstepaction"]
> [Esercitazioni su Attività del Registro Azure Container][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
