---
title: Informazioni sui registri, i repository, le immagini e gli artefatti
description: Introduzione ai concetti chiave di registri contenitori di Azure, repository, immagini del contenitore e altri elementi.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578091"
---
# <a name="about-registries-repositories-and-artifacts"></a>Informazioni sui registri, i repository e gli artefatti

Questo articolo presenta i concetti chiave relativi a registri contenitori, repository e immagini del contenitore ed elementi correlati. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registro di sistema, repository ed elementi":::

## <a name="registry"></a>Registro

Un *Registro* contenitori è un servizio che archivia e distribuisce le immagini del contenitore e gli artefatti correlati. Docker Hub è un esempio di registro contenitori pubblico che funge da catalogo generale delle immagini del contenitore docker. Azure Container Registry consente agli utenti di controllare direttamente il contenuto del contenitore, con l'autenticazione integrata, la [replica geografica](container-registry-geo-replication.md) che supporta la distribuzione e l'affidabilità globali per le distribuzioni con chiusura in rete, la [configurazione della rete virtuale con collegamento privato](container-registry-private-link.md), [blocco dei tag](container-registry-image-lock.md)e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore compatibili con Docker, Azure Container Registry supporta una gamma di [elementi di contenuto](container-registry-image-formats.md) , inclusi i grafici Helm e i formati di immagine OCI (Open Container Initiative).

## <a name="repository"></a>Archivio

Un *repository* è una raccolta di immagini del contenitore o di altri artefatti in un registro con lo stesso nome, ma con tag diversi. Ad esempio, le tre immagini seguenti si trovano nel `acr-helloworld` repository:

- *ACR-HelloWorld: più recente*
- *ACR-HelloWorld: V1*
- *ACR-HelloWorld: V2*

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di identificare i repository correlati e la proprietà dell'artefatto nell'organizzazione usando nomi delimitati da barre. Tuttavia, il registro di sistema gestisce tutti i repository in modo indipendente, non come una gerarchia. Ad esempio:

- *Marketing/campaign10-18/Web: V2*
- *Marketing/campaign10-18/API: V3*
- *Marketing/campaign10-18/email-sender: V2*
- *prodotto-restituzione/Web-invio: 20180604*
- *Product-Returns/legacy-Integrator: 20180715*

I nomi dei repository possono includere solo caratteri alfanumerici minuscoli, punti, trattini, caratteri di sottolineatura e barre. 

Per le regole di denominazione complete del repository, vedere la [specifica Open Container Initiative Distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Elemento

Un'immagine del contenitore o un altro artefatto all'interno di un registro è associato a uno o più tag, ha uno o più livelli ed è identificato da un manifesto. Comprendere in che modo questi componenti sono correlati tra loro possono aiutare a gestire il registro di sistema in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro artefatto ne specifica la versione. Un singolo artefatto all'interno di un repository può essere assegnato a uno o più tag e può anche essere "senza tag". Ovvero, è possibile eliminare tutti i tag da un'immagine, mentre i dati dell'immagine (i relativi livelli) rimangono nel registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione. Il tag `latest` viene usato per impostazione predefinita se non ne viene fornito uno nei comandi di Docker.

Il modo in cui vengono contrassegnate le immagini del contenitore è guidato dagli scenari per lo sviluppo o la distribuzione. Ad esempio, sono consigliati Tag stabili per la manutenzione delle immagini di base e tag univoci per la distribuzione di immagini. Per altre informazioni, vedere [consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md).

Per le regole di denominazione dei tag, vedere la [documentazione di Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Livello

Le immagini del contenitore e gli artefatti sono costituiti da uno o più *livelli*. Tipi di artefatti diversi definiscono i livelli in modo diverso. In un'immagine del contenitore Docker, ad esempio, ogni livello corrisponde a una riga nel Dockerfile che definisce l'immagine:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Livelli di un'immagine del contenitore":::

Gli artefatti in un registro condividono livelli comuni, aumentando l'efficienza di archiviazione. Ad esempio, più immagini in repository diversi potrebbero avere un livello di base ASP.NET Core comune, ma solo una copia di tale livello viene archiviata nel registro di sistema. La condivisione dei livelli ottimizza anche la distribuzione dei livelli nei nodi, con più elementi che condividono livelli comuni. Se un'immagine già presente in un nodo include il livello ASP.NET Core come base, il pull successivo di un'immagine diversa che fa riferimento allo stesso livello non trasferisce il livello al nodo. ma farà invece riferimento a quello già presente.

Per garantire l'isolamento e la protezione da potenziali manipolazioni dei livelli, i livelli non vengono condivisi tra i registri.

### <a name="manifest"></a>manifesto

Ogni immagine del contenitore o artefatto di cui è stato eseguito il push in un registro contenitori è associato a un *manifesto*. Il manifesto, generato dal registro di sistema quando viene eseguito il push del contenuto, identifica in modo univoco gli artefatti e specifica i livelli. È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [AZ ACR repository Show-manifests][az-acr-repository-show-manifests]. 

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

È possibile elencare i manifesti per un repository con il comando dell'interfaccia della riga di comando di Azure [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ad esempio, elencare i manifesti per il repository "ACR-HelloWorld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp&quot;: &quot;2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Hash di manifesto

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ogni immagine o artefatto, indipendentemente dal fatto che sia contrassegnato o meno, viene identificato dal digest. Il valore digest è univoco anche se i dati del livello dell'artefatto sono identici a quelli di un altro artefatto. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile effettuare il pull di un elemento da un registro specificandone il digest nell'operazione pull. Alcuni sistemi possono essere configurati per eseguire il pull mediante Digest perché garantisce che la versione dell'immagine venga tirata, anche se un'immagine con tag identico viene inserita in un secondo momento nel registro di sistema.

> [!IMPORTANT]
> Se si effettuano ripetutamente il push degli artefatti modificati con tag identici, è possibile creare "orfani", ovvero artefatti senza tag, ma che utilizzano ancora spazio nel registro di sistema. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. L'eliminazione di un'immagine senza tag libera lo spazio del registro di sistema quando il manifesto è l'unico, o l'ultimo, che punta a un livello specifico. Per informazioni su come liberare spazio usato dalle immagini non contrassegnate, vedere [eliminare immagini del contenitore in Azure container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Indirizzamento di un elemento

Per risolvere un elemento del registro di sistema per le operazioni push e pull con Docker o altri strumenti client, combinare il nome completo del registro di sistema, il nome del repository (incluso il percorso dello spazio dei nomi se applicabile) e un tag artefatto o un digest del manifesto. Vedere le sezioni precedenti per le spiegazioni di questi termini.

  **Indirizzo per tag**: `[loginServerUrl]/[repository][:tag]`
    
  **Indirizzo in base al digest**: `[loginServerUrl]/[repository@sha256][:digest]`  

Quando si usa Docker o altri strumenti client per eseguire il pull o il push di elementi in un registro contenitori di Azure, usare l'URL completo del registro di sistema, denominato anche nome del *server di accesso* . Nel cloud di Azure l'URL completo di un registro contenitori di Azure ha il formato `myregistry.azurecr.io` (tutti minuscole).

> [!NOTE]
> * Non è possibile specificare un numero di porta nell'URL del server di accesso del registro di sistema, ad esempio `myregistry.azurecr.io:443` . 
> * Il tag `latest` viene usato per impostazione predefinita se non si specifica un tag nel comando.  

   
### <a name="push-by-tag"></a>Push per tag

Esempi: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Pull per tag

Esempio: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Pull in base al digest del manifesto


Esempio:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [archiviazione del registro di sistema](container-registry-storage.md) e i [formati di contenuto supportati](container-registry-image-formats.md) in Azure container Registry.

Informazioni su come eseguire il [push e il pull delle immagini](container-registry-get-started-docker-cli.md) da container Registry di Azure.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


