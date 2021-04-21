---
title: Compilare un'immagine con Cloud Native Buildpack
description: Usare il comando az acr pack build per compilare un'immagine del contenitore da un'app ed eseguire il push Registro Azure Container, senza usare un Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780930"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Compilare ed eseguire il push di un'immagine da un'app usando cloud native buildpack

Il comando dell'interfaccia della riga di comando di Azure usa lo strumento dell'interfaccia della riga di comando, da `az acr pack build` [`pack`](https://github.com/buildpack/pack) [Buildpacks,](https://buildpacks.io/)per compilare un'app ed eseguire il push della relativa immagine in un Registro Azure Container. Questa funzionalità offre un'opzione per compilare rapidamente un'immagine del contenitore dal codice sorgente dell'applicazione in Node.js, Java e altri linguaggi senza dover definire un Dockerfile.

È possibile usare il Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi in questo articolo. Se si desidera usarlo in locale, è necessaria la versione 2.0.70 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="use-the-build-command"></a>Usare il comando build

Per compilare ed eseguire il push di un'immagine del contenitore usando Cloud Native Buildpacks, eseguire il [comando az acr pack build.][az-acr-pack-build] Mentre il [comando az acr build][az-acr-build] compila ed inserisce un'immagine da un'origine Dockerfile e dal codice correlato, specificando direttamente un albero di `az acr pack build` origine dell'applicazione.

Specificare come minimo quanto segue quando si esegue `az acr pack build`:

* Registro Azure Container in cui si esegue il comando
* Nome e tag dell'immagine risultante
* Uno dei percorsi [di contesto supportati per](container-registry-tasks-overview.md#context-locations) le attività ACR, ad esempio una directory locale, un repository GitHub o un tarball remoto
* Nome di un'immagine buildpack builder adatta all'applicazione. Registro Azure Container memorizza nella cache le immagini del generatore, ad esempio `cloudfoundry/cnb:0.0.34-cflinuxfs3` per compilazioni più veloci.  

`az acr pack build` supporta altre funzionalità dei comandi delle Attività del Registro Azure Container, tra cui [l'esecuzione di variabili](container-registry-tasks-reference-yaml.md#run-variables) e [i log di esecuzione delle attività](container-registry-tasks-logs.md) trasmessi e salvati per un recupero in un secondo momento.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Esempio: Compilare Node.js'immagine con Cloud Foundry builder

L'esempio seguente compila un'immagine del contenitore da un'app Node.js nel repo [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) usando il `cloudfoundry/cnb:0.0.34-cflinuxfs3` generatore. Questo generatore viene memorizzato nella cache Registro Azure Container, quindi non `--pull` è necessario un parametro:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Questo esempio compila `node-app` l'immagine con il tag e la inserisce nel registro contenitori `1.0` *myregistry.* In questo esempio il nome del registro di destinazione viene anteposto in modo esplicito al nome dell'immagine. Se non specificato, il nome del server di accesso del Registro di sistema viene automaticamente anteposto al nome dell'immagine.

L'output del comando mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo aver compilato correttamente l'immagine, è possibile eseguirla con Docker, se installata. Accedere prima di tutto al registro:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Passare a `localhost:1337` nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.

## <a name="example-build-java-image-with-heroku-builder"></a>Esempio: Compilare un'immagine Java con Heroku Builder

L'esempio seguente compila un'immagine del contenitore dall'app Java nel repo [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) usando il `heroku/buildpacks:18` generatore . Il `--pull` parametro specifica che il comando deve eseguire il pull dell'immagine del generatore più recente. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Questo esempio compila l'immagine contrassegnata con l'ID di esecuzione del comando ed esegue il push nel `java-app` *registro contenitori myregistry.*

L'output del comando mostra lo stato di avanzamento della compilazione e del push dell'immagine. 

Dopo aver compilato correttamente l'immagine, è possibile eseguirla con Docker, se installata. Accedere prima di tutto al registro:

```azurecli
az acr login --name myregistry
```

Eseguire l'immagine, sostituendo il tag dell'immagine con *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Passare a `localhost:8080` nel browser preferito per visualizzare l'app Web di esempio. Premere `[Ctrl]+[C]` per arrestare il contenitore.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato ed push di un'immagine del contenitore con , è possibile distribuirla come `az acr pack build` qualsiasi immagine in una destinazione di propria scelta. Le opzioni di distribuzione [](../app-service/tutorial-custom-container.md) di Azure includono l'esecuzione nel servizio [app o servizio Azure Kubernetes,](../aks/tutorial-kubernetes-deploy-cluster.md)tra le altre.

Per altre informazioni sulle funzionalità di Attività di ACR, vedere Automatizzare la compilazione e la manutenzione delle immagini dei contenitori [con Attività di ACR.](container-registry-tasks-overview.md)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
