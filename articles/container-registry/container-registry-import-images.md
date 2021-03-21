---
title: Importare immagini del contenitore
description: Importare immagini del contenitore in un registro Azure Container usando le API di Azure, senza bisogno di eseguire comandi di Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e6976f854b449f68faedd51878c2f3a7fe75cb0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988241"
---
# <a name="import-container-images-to-a-container-registry"></a>Importare immagini del contenitore in un registro contenitori

È possibile importare (copiare) facilmente immagini del contenitore in un registro Azure Container senza usare comandi di Docker. Ad esempio, è possibile importare immagini da un registro di sviluppo in un registro di produzione oppure copiare immagini di base da un registro pubblico.

Registro Azure Container supporta alcuni scenari comuni di copia di immagini da un registro esistente:

* Importazione da un registro pubblico

* Importa da un altro registro contenitori di Azure, nella stessa sottoscrizione o in una sottoscrizione di Azure o in un tenant diverso

* Importazione da un registro contenitori privato non di Azure

L'importazione di immagini in un registro Azure Container offre i vantaggi seguenti rispetto all'uso dei comandi dell'interfaccia della riga di comando di Docker:

* Poiché l'ambiente client non necessita di un'installazione locale di Docker, importare qualsiasi immagine del contenitore, indipendentemente dal tipo di sistema operativo supportato.

* Quando si importano immagini multiarchitettura (ad esempio immagini ufficiali di Docker), vengono copiate le immagini di tutte le architetture e le piattaforme specificate nell'elenco di manifesti.

* L'accesso al registro di sistema di destinazione non deve usare l'endpoint pubblico del registro di sistema.

Per importare immagini del contenitore seguendo le procedure di questo articolo, è necessario eseguire l'interfaccia della riga di comando di Azure in Azure Cloud Shell o localmente (si consiglia la versione 2.0.55 o una versione successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!NOTE]
> Se occorre distribuire immagini del contenitore identiche tra più aree di Azure, Registro Azure Container supporta anche la [replica geografica](container-registry-geo-replication.md). Con la replica geografica di un registro di sistema (è necessario il livello di servizio Premium), è possibile servire più aree con nomi identici per le immagini e i tag da un unico registro.
>

> [!IMPORTANT]
> Le modifiche apportate all'importazione di immagini tra due registri contenitori di Azure sono state introdotte a partire dal 2021 gennaio:
> * L'importazione in o da un registro contenitori di Azure con restrizioni di rete richiede il registro di sistema con restrizioni per [**consentire l'accesso da parte di servizi attendibili**](allow-access-trusted-services.md) per ignorare la rete. Per impostazione predefinita, l'impostazione è abilitata, consentendo l'importazione. Se l'impostazione non è abilitata in un registro appena creato con un endpoint privato o con regole del firewall del registro di sistema, l'importazione avrà esito negativo. 
> * In un registro contenitori di Azure con restrizioni di rete esistente usato come origine o destinazione dell'importazione, l'abilitazione di questa funzionalità di sicurezza di rete è facoltativa ma consigliata.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha già un registro Azure Container, crearne uno. Per i passaggi, vedere [Guida introduttiva: creare un registro contenitori privato usando l'interfaccia della](container-registry-get-started-azure-cli.md)riga di comando di Azure.

Per importare un'immagine in un registro contenitori di Azure, l'identità deve avere le autorizzazioni di scrittura per il registro di sistema di destinazione (almeno ruolo Collaboratore o un ruolo personalizzato che consente l'azione importImage). Vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importazione da un registro pubblico

### <a name="import-from-docker-hub"></a>Importare immagini dall'hub Docker

Usare ad esempio il comando [az acr import][az-acr-import] per importare l'immagine multiarchitettura `hello-world:latest` dall'hub Docker in un registro denominato *myregistry*. Poiché `hello-world` è un'immagine ufficiale dell'hub Docker, si trova nel repository `library` predefinito. Includere il nome del repository e, se si vuole, un tag nel valore del parametro dell'immagine `--source`. Si può anche identificare un'immagine in base al relativo digest del manifesto invece che in base a un tag, in modo da garantire una particolare versione dell'immagine.
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

È possibile verificare che più manifesti siano associati a questa immagine eseguendo il comando `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Se si dispone di un [account Docker Hub](https://www.docker.com/pricing), è consigliabile usare le credenziali durante l'importazione di un'immagine dall'hub docker. Passare il nome utente dell'hub Docker e la password o un [token di accesso personale](https://docs.docker.com/docker-hub/access-tokens/) come parametri a `az acr import` . L'esempio seguente importa un'immagine pubblica dal `tensorflow` repository nell'hub Docker, usando le credenziali dell'hub docker:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importare immagini dal registro contenitori di Microsoft

Ad esempio, importare l' `ltsc2019` immagine di Windows Server Core dal `windows` repository in Microsoft container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importare da un registro contenitori di Azure nello stesso tenant di Active Directory

È possibile importare un'immagine da un registro contenitori di Azure nello stesso tenant di Active Directory usando le autorizzazioni di Azure Active Directory integrata.

* È necessario che l'identità disponga delle autorizzazioni Azure Active Directory per la lettura dal registro di sistema di origine (ruolo lettore) e per l'importazione nel registro di sistema di destinazione (ruolo Collaboratore o un [ruolo personalizzato](container-registry-roles.md#custom-roles) che consente l'azione importImage).

* Il registro può trovarsi nella stessa sottoscrizione di Azure o in una sottoscrizione diversa nello stesso tenant di Active Directory.

* L' [accesso pubblico](container-registry-access-selected-networks.md#disable-public-network-access) al registro di sistema di origine potrebbe essere disabilitato. Se l'accesso pubblico è disabilitato, specificare il registro di sistema di origine tramite l'ID risorsa anziché il nome del server di accesso del registro di sistema.

* Se il registro di sistema di origine e/o il registro di sistema di destinazione ha un endpoint privato o regole del firewall del registro di sistema, assicurarsi che il registro di sistema con restrizioni [consenta ai servizi attendibili](allow-access-trusted-services.md) di accedere alla rete.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importare immagini da un registro nella stessa sottoscrizione

Ad esempio, importare l'immagine `aci-helloworld:latest` dal registro di origine *mysourceregistry* a *myregistry* nella stessa sottoscrizione di Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Nell'esempio seguente l'immagine viene importata in `aci-helloworld:latest` *Registro* di sistema da un *mysourceregistry* del registro di sistema di origine in cui l'accesso all'endpoint pubblico del registro di sistema è disabilitato. Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il `--source` parametro specifica solo il repository di origine e il tag, non il nome del server di accesso del registro di sistema.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

L'esempio seguente importa un'immagine in base al digest del manifesto (hash SHA-256, rappresentato come `sha256:...`) anziché in base a un tag:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importare immagini da un registro in un'altra sottoscrizione

Nell'esempio seguente *mysourceregistry* è in una sottoscrizione diversa da *myregistry* nello stesso tenant di Active Directory. Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il `--source` parametro specifica solo il repository di origine e il tag, non il nome del server di accesso del registro di sistema.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importare immagini da un registro usando le credenziali dell'entità servizio

Per eseguire l'importazione da un registro a cui non è possibile accedere usando autorizzazioni Active Directory integrate, è possibile usare le credenziali dell'entità servizio (se disponibili) per il registro di sistema di origine. Specificare l'ID app e la password di un'[entità servizio](container-registry-auth-service-principal.md) di Active Directory con accesso ACRPull al registro di origine. L'uso di un'entità servizio è utile per i sistemi di compilazione e altri sistemi automatici che devono importare immagini nel registro.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importare da un registro contenitori di Azure in un tenant di Active Directory diverso

Per importare da un registro contenitori di Azure in un tenant di Azure Active Directory diverso, specificare il registro di sistema di origine in base al nome del server di accesso e fornire le credenziali di nome utente e password che consentono l'accesso pull al registro di sistema. Ad esempio, usare un [token con ambito repository](container-registry-repository-scoped-permissions.md) e una password oppure l'AppID e la password di un' [entità servizio](container-registry-auth-service-principal.md) Active Directory con accesso ACRPull al registro di sistema di origine. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importazione da un registro contenitori privato non di Azure

Importare un'immagine da un registro privato non di Azure specificando le credenziali che abilitano l'accesso pull al registro di sistema. Ad esempio, eseguire il pull di un'immagine da un registro privato di Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come importare immagini del contenitore in un registro Azure Container da un registro pubblico o da un altro registro privato. Per altre opzioni di importazione di immagini, vedere la documentazione di riferimento sul comando [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
