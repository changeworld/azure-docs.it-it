---
title: Informazioni su registri, repository, immagini ed elementi
description: Introduzione ai concetti chiave di registri contenitori di Azure, repository, immagini del contenitore e altri artefatti.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784084"
---
# <a name="about-registries-repositories-and-artifacts"></a>Informazioni su registri, repository ed elementi

Questo articolo presenta i concetti chiave dei registri contenitori, dei repository e delle immagini dei contenitori e degli artefatti correlati. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registro di sistema, repository ed elementi":::

## <a name="registry"></a>Registro

Un registro *contenitori* è un servizio che archivia e distribuisce le immagini del contenitore e gli artefatti correlati. Docker Hub è un esempio di registro contenitori pubblico che funge da catalogo generale di immagini del contenitore Docker. Registro Azure Container offre agli utenti il controllo diretto del contenuto del contenitore, con autenticazione [integrata,](container-registry-geo-replication.md) replica geografica che supporta la distribuzione globale e l'affidabilità per le distribuzioni a chiusura della [rete,](container-registry-private-link.md)la configurazione della rete virtuale con collegamento privato, il blocco dei [tag](container-registry-image-lock.md)e molte altre funzionalità avanzate. 

Oltre alle immagini del contenitore compatibili con Docker, [](container-registry-image-formats.md) Registro Azure Container supporta una gamma di elementi di contenuto, tra cui grafici Helm e formati di immagine OCI (Open Container Initiative).

## <a name="repository"></a>Archivio

Un *repository* è una raccolta di immagini del contenitore o altri artefatti in un registro che hanno lo stesso nome, ma tag diversi. Ad esempio, nel repository sono presenti le tre immagini `acr-helloworld` seguenti:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

I nomi dei repository possono anche includere [spazi dei nomi](container-registry-best-practices.md#repository-namespaces), Gli spazi dei nomi consentono di identificare i repository correlati e la proprietà degli artefatti nell'organizzazione usando nomi delimitati da barre. Tuttavia, il Registro di sistema gestisce tutti i repository in modo indipendente, non come gerarchia. Ad esempio:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

I nomi dei repository possono includere solo caratteri alfanumerici minuscoli, punti, trattini, caratteri di sottolineatura e barre. 

Per le regole di denominazione complete dei repository, vedere [Open Container Initiative Distribution Specification](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Elemento

Un'immagine del contenitore o un altro elemento all'interno di un registro è associato a uno o più tag, ha uno o più livelli e viene identificato da un manifesto. La comprensione della relazione tra questi componenti consente di gestire il registro in modo efficace.

### <a name="tag"></a>Tag

Il *tag* per un'immagine o un altro elemento ne specifica la versione. A un singolo elemento all'interno di un repository è possibile assegnare uno o più tag e anche "senza tag". In altre parole, è possibile eliminare tutti i tag da un'immagine, mentre i dati dell'immagine (i relativi livelli) rimangono nel Registro di sistema.

Il nome di un'immagine è definito dal repository (o da repository e spazio dei nomi) e da un tag. È possibile eseguire il push e il pull di un'immagine specificandone il nome nella relativa operazione. Il tag `latest` viene usato per impostazione predefinita se non ne viene specificato uno nei comandi Docker.

La modalità di creazione di tag per le immagini dei contenitori è guidata dagli scenari per svilupparle o distribuirle. Ad esempio, i tag stabili sono consigliati per la gestione delle immagini di base e tag univoci per la distribuzione delle immagini. Per altre informazioni, vedere [Raccomandazioni per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md).

Per le regole di denominazione dei tag, vedere la [documentazione di Docker.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Livello

Le immagini e gli artefatti del contenitore sono costituito da uno o più *livelli*. Tipi di artefatti diversi definiscono i livelli in modo diverso. Ad esempio, in un'immagine del contenitore Docker ogni livello corrisponde a una riga nel Dockerfile che definisce l'immagine:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Livelli di un'immagine del contenitore":::

Gli artefatti in un registro condividono livelli comuni, aumentando l'efficienza di archiviazione. Ad esempio, diverse immagini in repository diversi potrebbero avere un livello di base ASP.NET Core comune, ma solo una copia di tale livello viene archiviata nel Registro di sistema. La condivisione dei livelli ottimizza anche la distribuzione dei livelli ai nodi, con più artefatti che condividono livelli comuni. Se un'immagine già presente in un nodo include il livello ASP.NET Core come base, il pull successivo di un'immagine diversa che fa riferimento allo stesso livello non trasferisce il livello al nodo. ma farà invece riferimento a quello già presente.

Per garantire un isolamento e una protezione sicuri dalla potenziale manipolazione dei livelli, i livelli non vengono condivisi tra i registri.

### <a name="manifest"></a>manifesto

Ogni elemento o immagine del contenitore di cui è stato push in un registro contenitori è associato a un *manifesto*. Il manifesto, generato dal registro quando viene inserito il contenuto, identifica in modo univoco gli artefatti e specifica i livelli. È possibile elencare i manifesti per un repository con il comando [az acr repository show-manifests][az-acr-repository-show-manifests]dell'interfaccia della riga di comando di Azure. 

Un manifesto di base per un'immagine Linux `hello-world` è simile al seguente:

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

Ad esempio, elencare i manifesti per il repository "acr-helloworld":

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

I manifesti sono identificati da un hash SHA-256 univoco, l'*hash di manifesto*. Ogni immagine o artefatto, indipendentemente dal fatto che sia contrassegnato o meno, viene identificato dal relativo digest. Il valore digest è univoco anche se i dati del livello dell'artefatto sono identici a quelli di un altro artefatto. Questo meccanismo consente quindi di eseguire ripetutamente il push di immagini con tag identici in un registro. Ad esempio, è possibile eseguire più volte il push di `myimage:latest` nel registro senza errori poiché ogni immagine viene identificata dal relativo hash univoco.

È possibile eseguire il pull di un elemento da un registro specificandone il digest nell'operazione pull. Alcuni sistemi possono essere configurati per il pull tramite digest perché garantisce il pull della versione dell'immagine, anche se viene successivamente inserito un'immagine con tag identico nel registro.

> [!IMPORTANT]
> Se si esegono ripetutamente elementi modificati con tag identici, è possibile creare "orfani", elementi senza tag, ma che utilizzano comunque spazio nel registro. Le immagini senza tag non vengono visualizzate nell'interfaccia della riga di comando o nel portale di Azure quando si elencano o si visualizzano le immagini in base ai tag. Tuttavia, i loro livelli sono comunque presenti e occupano spazio nel registro. L'eliminazione di un'immagine senza tag libera spazio nel Registro di sistema quando il manifesto è l'unico, o l'ultimo, che punta a un livello specifico. Per informazioni su come liberare spazio usato dalle immagini senza tag, vedere [Eliminare immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Indirizzamento di un artefatto

Per risolvere un artefatto del registro per le operazioni push e pull con Docker o altri strumenti client, combinare il nome completo del registro, il nome del repository (incluso il percorso dello spazio dei nomi, se applicabile) e un tag di artefatto o un digest del manifesto. Per le spiegazioni di questi termini, vedere le sezioni precedenti.

  **Indirizzo per tag:**`[loginServerUrl]/[repository][:tag]`
    
  **Indirizzo per digest:**`[loginServerUrl]/[repository@sha256][:digest]`  

Quando si usa Docker o altri strumenti client per eseguire il pull o il push di elementi in un registro Azure Container, usare l'URL completo del Registro di sistema, denominato anche nome *del server di* accesso. Nel cloud di Azure l'URL completo di un registro Azure Container è nel formato `myregistry.azurecr.io` (tutto minuscolo).

> [!NOTE]
> * Non è possibile specificare un numero di porta nell'URL del server di accesso del Registro di sistema, ad esempio `myregistry.azurecr.io:443` . 
> * Il tag `latest` viene usato per impostazione predefinita se non si specifica un tag nel comando.  

   
### <a name="push-by-tag"></a>Push in base al tag

Esempi: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Eseguire il pull in base al tag

Esempio: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Eseguire il pull in base al digest del manifesto


Esempio:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni [sull'archiviazione del Registro di](container-registry-storage.md) sistema e sui formati di contenuto [supportati](container-registry-image-formats.md) in Registro Azure Container.

Informazioni su come [eseguire il push e il pull di](container-registry-get-started-docker-cli.md) immagini Registro Azure Container.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
