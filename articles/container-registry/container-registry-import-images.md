---
title: Importare immagini del contenitore
description: Importare immagini del contenitore in un registro Azure Container usando le API di Azure, senza bisogno di eseguire comandi di Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781524"
---
# <a name="import-container-images-to-a-container-registry"></a>Importare immagini del contenitore in un registro contenitori

È possibile importare (copiare) facilmente immagini del contenitore in un registro Azure Container senza usare comandi di Docker. Ad esempio, è possibile importare immagini da un registro di sviluppo in un registro di produzione oppure copiare immagini di base da un registro pubblico.

Registro Azure Container gestisce diversi scenari comuni per copiare immagini e altri artefatti da un registro esistente:

* Importare immagini da un registro pubblico

* Importare immagini o artefatti OCI, inclusi i grafici Helm 3 da un altro Registro Azure Container, nello stesso tenant o nella stessa sottoscrizione di Azure o in un tenant diverso

* Importazione da un registro contenitori privato non di Azure

L'importazione di immagini in un registro Azure Container offre i vantaggi seguenti rispetto all'uso dei comandi dell'interfaccia della riga di comando di Docker:

* Poiché l'ambiente client non richiede un'installazione docker locale, importare qualsiasi immagine del contenitore, indipendentemente dal tipo di sistema operativo supportato.

* Quando si importano immagini multiarchitettura (ad esempio immagini ufficiali di Docker), vengono copiate le immagini di tutte le architetture e le piattaforme specificate nell'elenco di manifesti.

* L'accesso al registro di destinazione non deve usare l'endpoint pubblico del registro.

Per importare immagini del contenitore seguendo le procedure di questo articolo, è necessario eseguire l'interfaccia della riga di comando di Azure in Azure Cloud Shell o localmente (si consiglia la versione 2.0.55 o una versione successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!NOTE]
> Se occorre distribuire immagini del contenitore identiche tra più aree di Azure, Registro Azure Container supporta anche la [replica geografica](container-registry-geo-replication.md). Con la replica geografica di un registro (è necessario il livello di servizio Premium), è possibile gestire più aree con nomi di immagine e tag identici da un singolo registro.
>

> [!IMPORTANT]
> Le modifiche all'importazione di immagini tra due registri contenitori di Azure sono state introdotte a partire da gennaio 2021:
> * L'importazione da o verso un Registro Azure Container con restrizioni di rete richiede che il registro con restrizioni consenta l'accesso da parte di servizi attendibili [**per**](allow-access-trusted-services.md) ignorare la rete. Per impostazione predefinita, l'impostazione è abilitata, consentendo l'importazione. Se l'impostazione non è abilitata in un registro appena creato con un endpoint privato o con regole del firewall del Registro di sistema, l'importazione avrà esito negativo. 
> * In un Registro Azure Container con restrizioni di rete esistente usato come origine o destinazione di importazione, l'abilitazione di questa funzionalità di sicurezza di rete è facoltativa ma consigliata.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha già un registro Azure Container, crearne uno. Per la procedura, vedere Avvio rapido: Creare un [registro contenitori privato usando l'interfaccia della riga di comando di Azure.](container-registry-get-started-azure-cli.md)

Per importare un'immagine in un Registro Azure Container, l'identità deve avere le autorizzazioni di scrittura per il registro di destinazione (almeno il ruolo Collaboratore o un ruolo personalizzato che consente l'azione importImage). Vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md#custom-roles). 

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

Se si ha un [account Docker Hub](https://www.docker.com/pricing), è consigliabile usare le credenziali quando si importa un'immagine da Docker Hub. Passare il Docker Hub utente e la password o un token di [accesso personale](https://docs.docker.com/docker-hub/access-tokens/) come parametri a `az acr import` . L'esempio seguente importa un'immagine pubblica dal `tensorflow` repository in Docker Hub, usando Docker Hub credenziali:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importare immagini dal registro contenitori di Microsoft

Ad esempio, importare `ltsc2019` l'immagine di Windows Server Core dal `windows` repository in Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importare da un registro Azure Container nello stesso tenant di AD

È possibile importare un'immagine da un registro Azure Container nello stesso tenant di AD usando le Azure Active Directory integrate.

* L'identità deve Azure Active Directory autorizzazioni per la lettura dal Registro di sistema di origine (ruolo Lettore) e per l'importazione nel Registro di sistema di destinazione (ruolo Collaboratore o ruolo personalizzato che consente l'azione importImage). [](container-registry-roles.md#custom-roles)

* Il registro può trovarsi nella stessa sottoscrizione di Azure o in una sottoscrizione diversa nello stesso tenant di Active Directory.

* [L'accesso](container-registry-access-selected-networks.md#disable-public-network-access) pubblico al Registro di sistema di origine potrebbe essere disabilitato. Se l'accesso pubblico è disabilitato, specificare il Registro di sistema di origine in base all'ID risorsa anziché al nome del server di accesso del Registro di sistema.

* Se il registro di origine e/o il registro di destinazione hanno un endpoint [](allow-access-trusted-services.md) privato o regole firewall del Registro di sistema vengono applicate, assicurarsi che il registro con restrizioni consenta ai servizi attendibili di accedere alla rete.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importare immagini da un registro nella stessa sottoscrizione

Ad esempio, importare l'immagine `aci-helloworld:latest` dal registro di origine *mysourceregistry* a *myregistry* nella stessa sottoscrizione di Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

L'esempio seguente importa l'immagine in myregistry da un registro di origine `aci-helloworld:latest` *mysourceregistry* in cui l'accesso all'endpoint pubblico del Registro di sistema è disabilitato.  Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il parametro specifica solo il repository e il tag di `--source` origine, non il nome del server di accesso del Registro di sistema.

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

Nell'esempio seguente *mysourceregistry* è in una sottoscrizione diversa da *myregistry* nello stesso tenant di Active Directory. Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il parametro specifica solo il repository e il tag di `--source` origine, non il nome del server di accesso del Registro di sistema.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importare immagini da un registro usando le credenziali dell'entità servizio

Per importare da un registro a cui non è possibile accedere usando le autorizzazioni integrate di Active Directory, è possibile usare le credenziali dell'entità servizio (se disponibili) nel registro di origine. Specificare l'ID app e la password di un'[entità servizio](container-registry-auth-service-principal.md) di Active Directory con accesso ACRPull al registro di origine. L'uso di un'entità servizio è utile per i sistemi di compilazione e altri sistemi automatici che devono importare immagini nel registro.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importare da un registro Azure Container in un tenant di AD diverso

Per importare da un registro Azure Container in un tenant di Azure Active Directory diverso, specificare il registro di origine in base al nome del server di accesso e specificare le credenziali di nome utente e password che consentono l'accesso pull al Registro di sistema. Ad esempio, usare un [token](container-registry-repository-scoped-permissions.md) e una password con ambito repository oppure l'APPID e la password di un'entità servizio Active [Directory](container-registry-auth-service-principal.md) con accesso ACRPull al Registro di sistema di origine. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importazione da un registro contenitori privato non di Azure

Importare un'immagine da un registro privato non di Azure specificando le credenziali che consentono l'accesso pull al Registro di sistema. Ad esempio, eseguire il pull di un'immagine da un registro privato di Docker: 

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
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
