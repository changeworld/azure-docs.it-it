---
title: Eseguire il push e il pull dell'artefatto OCI
description: Eseguire il push e il pull di artefatti OCI (Open Container Initiative) usando un registro contenitori privato in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781488"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Eseguire il push e il pull di un artefatto OCI usando un Registro Azure Container

È possibile usare un Registro Azure Container per archiviare e gestire gli artefatti [OCI (Open Container Initiative),](container-registry-image-formats.md#oci-artifacts) nonché le immagini dei contenitori compatibili con Docker e Docker.

Per illustrare questa funzionalità, questo articolo illustra come usare lo strumento Registro OCI come archiviazione [(ORAS)](https://github.com/deislabs/oras) per eseguire il push di un artefatto di esempio, un file di testo, in un Registro Azure Container. Eseguire quindi il pull dell'artefatto dal registro. È possibile gestire un'ampia gamma di artefatti OCI in un Registro Azure Container usando diversi strumenti da riga di comando appropriati per ogni artefatto.

## <a name="prerequisites"></a>Prerequisiti

* **Registro Azure Container:** creare un registro contenitori nella sottoscrizione di Azure. Ad esempio usare il [portale di Azure](container-registry-get-started-portal.md) oppure l'[interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md).
* **Strumento ORAS:** scaricare e installare una versione corrente di ORAS per il sistema operativo dal [repository GitHub.](https://github.com/deislabs/oras/releases) Lo strumento viene rilasciato come tarball compresso `.tar.gz` (file ). Estrarre e installare il file usando le procedure standard per il sistema operativo in uso.
* **Azure Active Directory'entità servizio (facoltativo):** per eseguire l'autenticazione direttamente con ORAS, creare [un'entità servizio](container-registry-auth-service-principal.md) per accedere al registro. Assicurarsi che all'entità servizio sia assegnato un ruolo, ad esempio AcrPush, in modo che abbia le autorizzazioni per eseguire il push e il pull degli artefatti.
* **Interfaccia della riga di comando di Azure (facoltativo):** per usare una singola identità, è necessaria un'installazione locale dell'interfaccia della riga di comando di Azure. È consigliabile la versione 2.0.71 o successiva. Eseguire `az --version ` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* **Docker (facoltativo):** per usare una singola identità, è necessario che Docker sia installato in locale per l'autenticazione con il registro. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [macOS][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Accedere a un registro

Questa sezione illustra due flussi di lavoro consigliati per accedere al Registro di sistema, a seconda dell'identità usata. Scegliere il metodo appropriato per l'ambiente.

### <a name="sign-in-with-oras"></a>Accedere con ORAS

Usando [un'entità servizio](container-registry-auth-service-principal.md) con diritti di push, eseguire il comando per accedere al Registro di sistema `oras login` usando l'ID applicazione e la password dell'entità servizio. Specificare il nome completo del Registro di sistema (tutto minuscolo), in questo caso *myregistry.azurecr.io*. L'ID applicazione dell'entità servizio viene passato nella variabile di ambiente `$SP_APP_ID` e la password nella variabile `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Per leggere la password da Stdin, usare `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Accedere tramite l'interfaccia della riga di comando di Azure

[Accedere all'interfaccia](/cli/azure/authenticate-azure-cli) della riga di comando di Azure con la propria identità per eseguire il push e il pull degli elementi dal registro contenitori.

Usare quindi il comando dell'interfaccia della riga di comando di Azure [az acr login](/cli/azure/acr#az_acr_login) per accedere al Registro di sistema. Ad esempio, per eseguire l'autenticazione in un registro denominato *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` usa il client Docker per impostare un token Azure Active Directory nel `docker.config` file. Il client Docker deve essere installato e in esecuzione per completare il singolo flusso di autenticazione.

## <a name="push-an-artifact"></a>Eseguire il push di un elemento

Creare un file di testo in una directory di lavoro locale con testo di esempio. Ad esempio, in una shell bash:

```bash
echo "Here is an artifact" > artifact.txt
```

Usare il `oras push` comando per eseguire il push di questo file di testo nel Registro di sistema. L'esempio seguente inserisce il file di testo di esempio `samples/artifact` nel repo. Il Registro di sistema viene identificato con il nome completo del registro *myregistry.azurecr.io* (tutto minuscolo). L'elemento è contrassegnato con `1.0` . L'elemento ha un tipo non definito, per impostazione predefinita, identificato dalla stringa del tipo *di supporto* che segue il nome file `artifact.txt` . Per [altri tipi, vedere Artefatti OCI.](https://github.com/opencontainers/artifacts) 

**Linux o macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

L'output per un push riuscito è simile al seguente:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Per gestire gli elementi nel Registro di sistema, se si usa l'interfaccia della riga di comando di Azure, eseguire comandi standard `az acr` per la gestione delle immagini. Ad esempio, ottenere gli attributi dell'artefatto usando il [comando az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

L'output è simile al seguente:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Eseguire il pull di un artefatto

Eseguire il `oras pull` comando per eseguire il pull dell'artefatto dal registro.

Rimuovere prima di tutto il file di testo dalla directory di lavoro locale:

```bash
rm artifact.txt
```

Eseguire `oras pull` per eseguire il pull dell'elemento e specificare il tipo di supporto usato per eseguire il push dell'elemento:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verificare che il pull sia riuscito:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Rimuovere l'artefatto (facoltativo)

Per rimuovere l'artefatto dal Registro Azure Container, usare il [comando az acr repository delete.][az-acr-repository-delete] L'esempio seguente rimuove l'elemento archiviato in :

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Esempio: Compilare un'immagine Docker dall'artefatto OCI

Il codice sorgente e i file binari per compilare un'immagine del contenitore possono essere archiviati come artefatti OCI in un Registro Azure Container. È possibile fare riferimento a un elemento di origine come contesto di compilazione per [un'attività ACR.](container-registry-tasks-overview.md) Questo esempio illustra come archiviare un Dockerfile come artefatto OCI e quindi fare riferimento all'elemento per compilare un'immagine del contenitore.

Ad esempio, creare un Dockerfile di una riga:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Accedere al registro contenitori di destinazione.

```azurecli
az login
az acr login --name myregistry
```

Creare ed eseguire il push di un nuovo artefatto OCI nel registro di destinazione usando il `oras push` comando . In questo esempio viene impostato il tipo di supporto predefinito per l'artefatto.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Eseguire il [comando az acr build](/cli/azure/acr#az_acr_build) per compilare l'immagine hello-world usando il nuovo artefatto come contesto di compilazione:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [libreria ORAS,](https://github.com/deislabs/oras/tree/master/docs)inclusa la configurazione di un manifesto per un artefatto
* Visitare il [repo Artefatti OCI](https://github.com/opencontainers/artifacts) per informazioni di riferimento sui nuovi tipi di artefatto



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
