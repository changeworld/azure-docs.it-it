---
title: Immagini multiarchitettura nel registro di sistema
description: Usa il registro contenitori di Azure per creare, importare, archiviare e distribuire immagini multiarchitettura (a più Arch)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012343"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Immagini multiarchitettura nel registro contenitori di Azure

Questo articolo presenta le immagini *multiarchitettura* (a più *Arch*) e come è possibile usare le funzionalità di Azure container Registry per crearle, archiviarle e usarle. 

Un'immagine a più Arch è un tipo di immagine contenitore che può combinare varianti per diverse architetture e talvolta per sistemi operativi diversi. Quando si esegue un'immagine con supporto per più architetture, i client contenitore selezioneranno automaticamente una variante dell'immagine che corrisponde al sistema operativo e all'architettura.

## <a name="manifests-and-manifest-lists"></a>Manifesti ed elenchi di manifesti

Le immagini a più Arch sono basate su manifesti di immagine ed elenchi di manifesti.

### <a name="manifest"></a>manifesto

Ogni immagine del contenitore è rappresentata da un [manifesto](container-registry-concepts.md#manifest). Un manifesto è un file JSON che identifica in modo univoco l'immagine, facendo riferimento ai livelli e alle dimensioni corrispondenti. 

Un manifesto di base per un'immagine Linux ha un `hello-world` aspetto simile al seguente:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
È possibile visualizzare un manifesto in Azure Container Registry usando il portale di Azure o gli strumenti, ad esempio il comando [AZ ACR repository Show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) nell'interfaccia della riga di comando di Azure.

### <a name="manifest-list"></a>Elenco manifesti

Un *elenco di manifesti* per un'immagine a più Arch (noto più in generale come [indice di immagine](https://github.com/opencontainers/image-spec/blob/master/image-index.md) per le immagini OCI) è una raccolta (indice) di immagini e ne viene creata una specificando uno o più nomi di immagine. Sono inclusi i dettagli su ognuna delle immagini, ad esempio il sistema operativo e l'architettura, le dimensioni e il digest del manifesto supportati. L'elenco di manifesti può essere usato in modo analogo al nome di un'immagine nei `docker pull` `docker run` comandi e. 

L' `docker` interfaccia della riga di comando gestisce manifesti ed elenchi di manifesti usando il comando [Docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> Attualmente, il `docker manifest` comando e i sottocomandi sono sperimentali. Per informazioni dettagliate sull'uso dei comandi sperimentali, vedere la documentazione di Docker.

È possibile visualizzare un elenco di manifesti usando il `docker manifest inspect` comando. Di seguito è riportato l'output per l'immagine a più Arch `mcr.microsoft.com/mcr/hello-world:latest` , che presenta tre manifesti: due per le architetture del sistema operativo Linux e una per un'architettura di Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Quando un elenco di manifesti a più Arch viene archiviato in Azure Container Registry, è anche possibile visualizzare l'elenco di manifesti usando il portale di Azure o con strumenti come il comando [AZ ACR repository Show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) .

## <a name="import-a-multi-arch-image"></a>Importare un'immagine a più Arch 

Un'immagine a più Arch esistente può essere importata in un registro contenitori di Azure usando il comando [AZ ACR Import](/cli/azure/acr#az_acr_import) . La sintassi per l'importazione di immagini è identica a quella di un'immagine a architettura singola. Analogamente all'importazione di un'immagine a architettura singola, l'importazione di un'immagine a più Arch non usa i comandi di Docker. 

Per informazioni dettagliate, vedere [importare le immagini del contenitore in un registro contenitori](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Eseguire il push di un'immagine a più Arch

Quando si dispone di flussi di lavoro di compilazione per creare immagini del contenitore per diverse architetture, seguire questa procedura per eseguire il push di un'immagine a più Arch nel registro contenitori di Azure.

1. Contrassegnare ed effettuare il push di ogni immagine specifica dell'architettura nel registro contenitori. Nell'esempio seguente si presuppone l'esistenza di due architetture Linux: arm64 e amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Eseguire `docker manifest create` per creare un elenco di manifesti per combinare le immagini precedenti in un'immagine a più Arch.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Eseguire il push del manifesto nel registro contenitori usando `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Usare il `docker manifest inspect` comando per visualizzare l'elenco di manifesti. Un esempio di output del comando è illustrato in una sezione precedente.

Dopo aver eseguito il push del manifesto di più Arch nel registro, è possibile usare l'immagine a più Arch nello stesso modo in cui si esegue l'immagine con un'unica architettura. Ad esempio, eseguire il pull dell'immagine usando `docker pull` e usare il comando [AZ ACR repository](/cli/azure/acr/repository#az_acr_repository) per visualizzare tag, manifesti e altre proprietà dell'immagine.

## <a name="build-and-push-a-multi-arch-image"></a>Creazione e push di un'immagine a più Arch

Usando le funzionalità delle [attività di ACR](container-registry-tasks-overview.md), è possibile creare ed eseguire il push di un'immagine a più Arch nel registro contenitori di Azure. Ad esempio, definire un' [attività](container-registry-tasks-multi-step.md) in più passaggi in un file YAML che compila un'immagine a più Arch di Linux.

Nell'esempio seguente si presuppone che si disponga di Dockerfile separati per due architetture, arm64 e amd64. Compila e inserisce le immagini specifiche dell'architettura, quindi crea e inserisce un manifesto a più Arch con il `latest` Tag:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Passaggi successivi

* Usare [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) per compilare immagini del contenitore per diverse architetture.
* Informazioni sulla creazione di immagini multipiattaforma con il plug-in di Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) sperimentale.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
