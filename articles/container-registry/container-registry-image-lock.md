---
title: Bloccare immagini
description: Impostare gli attributi per un'immagine del contenitore o un repository in modo che non possa essere eliminata o sovrascritta in un Registro Azure Container.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772344"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Bloccare un'immagine del contenitore in un Registro Azure Container

In un Registro Azure Container è possibile bloccare una versione dell'immagine o un repository in modo che non possa essere eliminata o aggiornata. Per bloccare un'immagine o un repository, aggiornarne gli attributi usando il comando [az acr repository update][az-acr-repository-update]dell'interfaccia della riga di comando di Azure. 

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure in Azure Cloud Shell o in locale (è consigliata la versione 2.0.55 o successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!IMPORTANT]
> Questo articolo non si applica al blocco di un intero registro, ad esempio all'uso di Impostazioni **> blocchi** nel portale di Azure o dei comandi nell'interfaccia della riga di comando `az lock` di Azure. Il blocco di una risorsa registro non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nei repository. Il blocco di un Registro di sistema influisce solo sulle operazioni di gestione, ad esempio l'aggiunta o l'eliminazione di repliche o l'eliminazione del Registro di sistema stesso. Per altre informazioni, vedere [Bloccare le risorse per impedire modifiche impreviste.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Scenari

Per impostazione predefinita, un'immagine contrassegnata in Registro Azure Container è modificabile, quindi con le autorizzazioni appropriate è possibile aggiornare ripetutamente un'immagine con lo stesso tag ed eseguire il push in un registro. Le immagini del contenitore possono anche [essere eliminate in](container-registry-delete.md) base alle esigenze. Questo comportamento è utile quando si sviluppano immagini ed è necessario mantenere una dimensione per il registro.

Tuttavia, quando si distribuisce un'immagine del contenitore nell'ambiente di produzione, potrebbe essere necessaria *un'immagine del contenitore non* modificabile. Un'immagine non modificabile è un'immagine che non è possibile eliminare o sovrascrivere accidentalmente.

Vedere [Raccomandazioni per l'assegnazione di tag e il controllo delle versioni delle](container-registry-image-tag-version.md) immagini del contenitore per le strategie per assegnare tag e versioni alle immagini nel registro.

Usare il [comando az acr repository update][az-acr-repository-update] per impostare gli attributi del repository in modo che sia possibile:

* Bloccare una versione dell'immagine o un intero repository

* Proteggere una versione dell'immagine o un repository dall'eliminazione, ma consentire gli aggiornamenti

* Impedire operazioni di lettura (pull) su una versione dell'immagine o su un intero repository

Per esempi, vedere le sezioni seguenti. 

## <a name="lock-an-image-or-repository"></a>Bloccare un'immagine o un repository 

### <a name="show-the-current-repository-attributes"></a>Visualizzare gli attributi correnti del repository
Per visualizzare gli attributi correnti di un repository, eseguire il comando [az acr repository show][az-acr-repository-show] seguente:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Visualizzare gli attributi dell'immagine corrente
Per visualizzare gli attributi correnti di un tag, eseguire il comando [az acr repository show][az-acr-repository-show] seguente:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Bloccare un'immagine in base al tag

Per bloccare *l'immagine myrepo/myimage:tag* in *myregistry,* eseguire il comando [az acr repository update][az-acr-repository-update] seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bloccare un'immagine in base al digest del manifesto

Per bloccare *un'immagine myrepo/myimage* identificata dal digest del manifesto (hash SHA-256, rappresentato come `sha256:...` ), eseguire il comando seguente. Per trovare il digest del manifesto associato a uno o più tag immagine, eseguire il [comando az acr repository show-manifests.][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Bloccare un repository

Per bloccare il repository *myrepo/myimage* e tutte le immagini al suo interno, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Proteggere un'immagine o un repository dall'eliminazione

### <a name="protect-an-image-from-deletion"></a>Proteggere un'immagine dall'eliminazione

Per consentire *l'aggiornamento dell'immagine myrepo/myimage:tag* ma non l'eliminazione, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Proteggere un repository dall'eliminazione

Il comando seguente imposta il repository *myrepo/myimage* in modo che non possa essere eliminato. Le singole immagini possono comunque essere aggiornate o eliminate.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Impedire operazioni di lettura su un'immagine o un repository

Per impedire operazioni di lettura (pull) *sull'immagine myrepo/myimage:tag,* eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Per impedire operazioni di lettura su tutte le immagini nel repository *myrepo/myimage,* eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Sbloccare un'immagine o un repository

Per ripristinare il comportamento predefinito dell'immagine *myrepo/myimage:tag* in modo che possa essere eliminata e aggiornata, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Per ripristinare il comportamento predefinito del repository *myrepo/myimage* e di tutte le immagini in modo che possano essere eliminate e aggiornate, eseguire il comando seguente:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare il [comando az acr repository update][az-acr-repository-update] per impedire l'eliminazione o l'aggiornamento delle versioni delle immagini in un repository. Per impostare attributi aggiuntivi, vedere le informazioni di riferimento [sul comando az acr repository update.][az-acr-repository-update]

Per visualizzare gli attributi impostati per una versione dell'immagine o un repository, usare il [comando az acr repository show.][az-acr-repository-show]

Per informazioni dettagliate sulle operazioni di eliminazione, vedere [Eliminare immagini del contenitore in Registro Azure Container][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
