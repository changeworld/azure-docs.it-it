---
title: Archiviare i grafici Helm
description: Informazioni su come archiviare grafici Helm per le applicazioni Kubernetes usando i repository in Registro Azure Container
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: c7dcdf222e9628daedb7e1c3617efb0b9c7af185
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772380"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Eseguire il push e il pull dei grafici Helm in un Registro Azure Container

Per gestire e distribuire rapidamente applicazioni per Kubernetes, è possibile usare il [gestore pacchetti open source Helm][helm]. Con Helm, i pacchetti dell'applicazione sono definiti [come grafici](https://helm.sh/docs/topics/charts/), che vengono raccolti e archiviati in un repository di [grafici Helm.](https://helm.sh/docs/topics/chart_repository/)

Questo articolo illustra come ospitare i repository di grafici Helm in un Registro Azure Container usando i comandi Helm 3. In molti scenari è necessario compilare e caricare grafici personalizzati per le applicazioni sviluppate. Per altre informazioni su come creare grafici Helm personalizzati, vedere la Guida per gli sviluppatori del modello [di grafico][develop-helm-charts]. È anche possibile archiviare un grafico Helm esistente da un altro repo Helm.

## <a name="helm-3-or-helm-2"></a>Helm 3 o Helm 2?

Per archiviare, gestire e installare i grafici Helm, usare un client Helm e l'interfaccia della riga di comando helm. Le versioni principali del client Helm includono Helm 3 e Helm 2. Per informazioni dettagliate sulle differenze di versione, vedere le domande [frequenti sulla versione.](https://helm.sh/docs/faq/) 

Helm 3 deve essere usato per ospitare i grafici Helm Registro Azure Container. Con Helm 3 è possibile:

* Può creare uno o più repository Helm in un Registro Azure Container
* Archiviare i grafici Helm 3 in un registro come [artefatti OCI.](container-registry-image-formats.md#oci-artifacts) Registro Azure Container fornisce il supporto a ga per [gli artefatti OCI,](container-registry-oci-artifacts.md)inclusi i grafici Helm.
* Eseguire l'autenticazione con il registro usando il `helm registry login` comando .
* Usare `helm chart` i comandi nell'interfaccia della riga di comando helm per eseguire il push, il pull e la gestione dei grafici Helm in un registro
* Usare `helm install` per installare grafici in un cluster Kubernetes da una cache del repository locale.
> [!NOTE]
> A data di Helm 3, [i comandi az acr helm][az-acr-helm] per l'uso con il client Helm 2 sono deprecati. Prima della rimozione del comando verrà fornito un preavviso minimo di 3 mesi. Se in precedenza sono stati distribuiti grafici Helm 2, vedere [Migrazione di Helm v2 a v3.](https://helm.sh/docs/topics/v2_v3_migration/)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario in questo articolo sono necessarie le risorse seguenti:

- **Un Registro Azure Container** nella sottoscrizione di Azure. Se necessario, creare un registro usando il portale di Azure [o](container-registry-get-started-portal.md) l'interfaccia della riga di comando [di Azure.](container-registry-get-started-azure-cli.md)
- **Client Helm versione 3.1.0 o successiva:** eseguire `helm version` per trovare la versione corrente. Per altre informazioni su come installare e aggiornare Helm, vedere [Installing Helm][helm-install] (Installazione di Helm).
- **Un cluster Kubernetes in** cui si installerà un chart Helm. Se necessario, creare un [cluster servizio Azure Kubernetes cluster][aks-quickstart]. 
- **Interfaccia della riga di comando di Azure versione 2.0.71** o successiva: eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Abilitare il supporto OCI

Usare il `helm version` comando per verificare di aver installato Helm 3:

```console
helm version
```

Impostare la variabile di ambiente seguente per abilitare il supporto OCI nel client Helm 3. Attualmente, questo supporto è sperimentale. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Creare un grafico di esempio

Creare un grafico di test usando i comandi seguenti:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Come esempio di base, passare alla cartella ed `templates` eliminare prima il contenuto in questa cartella:

```console
cd hello-world/templates
rm -rf *
```

Nella cartella `templates` creare un file denominato eseguendo il comando `configmap.yaml` seguente:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Per altre informazioni sulla creazione e l'esecuzione di questo esempio, [Attività iniziali](https://helm.sh/docs/chart_template_guide/getting_started/) nella documentazione helm.

## <a name="save-chart-to-local-registry-cache"></a>Salvare il grafico nella cache del registro locale

Passare alla `hello-world` sottodirectory . Eseguire quindi per salvare una copia del grafico in locale e creare anche un alias con il nome completo del Registro di sistema (tutto minuscolo) e il repository e il `helm chart save` tag di destinazione. 

Nell'esempio seguente il nome del Registro di sistema è *mycontainerregistry*, il repo di destinazione *è hello-world* e il tag del grafico di destinazione è *v1,* ma sostituisce i valori per l'ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart list` per confermare che i grafici sono stati salvati nella cache del Registro di sistema locale. L'output è simile a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Eseguire l'autenticazione con il Registro di sistema

Eseguire il `helm registry login` comando nell'interfaccia della riga di comando di Helm 3 per eseguire [l'autenticazione](container-registry-authentication.md) con il Registro di sistema usando le credenziali appropriate per lo scenario specifico.

Ad esempio, creare un'Azure Active Directory [servizio con](container-registry-auth-service-principal.md#create-a-service-principal) autorizzazioni pull e push (ruolo AcrPush) nel Registro di sistema. Fornire quindi le credenziali dell'entità servizio a `helm registry login` . L'esempio seguente fornisce la password usando una variabile di ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Eseguire il push del grafico nel registro

Eseguire il `helm chart push` comando nell'interfaccia della riga di comando di Helm 3 per eseguire il push del grafico nel repository di destinazione completo:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Dopo un push riuscito, l'output è simile al seguente:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Elencare i grafici nel repository

Come per le immagini archiviate in un Registro Azure Container, è possibile usare i comandi [az acr repository][az-acr-repository] per visualizzare i repository che ospitano i grafici e i tag e i manifesti del grafico. 

Ad esempio, eseguire [az acr repository show][az-acr-repository-show] per visualizzare le proprietà del repository creato nel passaggio precedente:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

L'output è simile a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Eseguire il [comando az acr repository show-manifests][az-acr-repository-show-manifests] per visualizzare i dettagli del grafico archiviato nel repository. Ad esempio:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

L'output, abbreviato in questo esempio, mostra un `configMediaType` oggetto di `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Eseguire il pull del grafico nella cache locale

Per installare un grafico Helm in Kubernetes, il grafico deve essere nella cache locale. In questo esempio eseguire prima di `helm chart remove` tutto per rimuovere il grafico locale esistente denominato `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Eseguire `helm chart pull` per scaricare il grafico dal Registro Azure Container nella cache locale:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Esportare il grafico Helm

Per lavorare ulteriormente con il grafico, esportarlo in una directory locale usando `helm chart export` . Ad esempio, esportare il grafico di cui è stato estratto nella `install` directory :

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Per visualizzare le informazioni per il grafico esportato nel repo, eseguire il comando `helm show chart` nella directory in cui è stato esportato il grafico.

```console
cd install
helm show chart hello-world
```

Helm restituisce informazioni dettagliate sulla versione più recente del grafico, come illustrato nell'output di esempio seguente:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Installare il chart Helm

Eseguire `helm install` per installare il chart Helm di cui è stato eseguito il pulled nella cache locale ed esportato. Specificare un nome di versione, *ad esempio myhelmtest,* o passare il `--generate-name` parametro . Ad esempio:

```console
helm install myhelmtest ./hello-world
```

L'output al termine dell'installazione del grafico è simile al seguente:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Per verificare l'installazione, eseguire il `helm get manifest` comando . 

```console
helm get manifest myhelmtest
```

Il comando restituisce i dati YAML nel `configmap.yaml` file modello.

Eseguire `helm uninstall` per disinstallare la versione del grafico nel cluster:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Eliminare un grafico dal registro

Per eliminare un grafico dal registro contenitori, usare il [comando az acr repository delete.][az-acr-repository-delete] Eseguire il comando seguente e confermare l'operazione quando richiesto:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come creare e distribuire grafici Helm, vedere [Developing Helm charts][develop-helm-charts] (Distribuzione di grafici Helm).
* Altre informazioni sull'installazione di applicazioni con Helm in [servizio Azure Kubernetes (AKS).](../aks/kubernetes-helm.md)
* I grafici Helm possono essere usati come parte del processo di compilazione del contenitore. Per altre informazioni, vedere [Usare Registro Azure Container attività.][acr-tasks]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az_configure
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
