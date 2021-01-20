---
title: "Esercitazione: Distribuire WordPress in un cluster del servizio Azure Kubernetes con il server flessibile MySQL usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare e distribuire rapidamente app WordPress nel servizio Azure Kubernetes con il server flessibile di Database di Azure per MySQL.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: b82bb9b7684d1119b6b62216b52210845d8f4c67
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "98199632"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Esercitazione: Distribuire un'app WordPress nel servizio Azure Kubernetes con il server flessibile di Database di Azure per MySQL

In questa guida di avvio rapido si distribuisce un'applicazione WordPress nel servizio Azure Kubernetes con il server flessibile di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure. 
Il **[servizio Azure Kubernetes](../../aks/intro-kubernetes.md)** è un servizio Kubernetes gestito che consente di distribuire e gestire rapidamente i cluster. Il **[server flessibile di Database di Azure per MySQL (anteprima)](overview.md)** è un servizio di database completamente gestito progettato per offrire un controllo più granulare e una maggiore flessibilità rispetto alle funzioni di gestione e alle impostazioni di configurazione del database. Attualmente il server flessibile è disponibile in anteprima.

> [!NOTE]
> - Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica
> - Questa guida di avvio rapido presuppone una comprensione di base dei concetti relativi a Kubernetes, WordPress e MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'ultima versione dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!NOTE]
> Se si eseguono i comandi in questo avvio rapido in locale (anziché con Azure Cloud Shell), assicurarsi di eseguire i comandi come amministratore.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse denominato *wordpress-project* usando il comando [az group create][az-group-create] nella località *eastus*.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> La località del gruppo di risorse è quella in cui vengono archiviati i relativi metadati. È anche quella in cui viene eseguita la risorsa in Azure se non si specifica un'area diversa durante la creazione.

L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container

Usare il comando [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Questa operazione richiede alcuni minuti.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

> [!NOTE]
> Quando si crea un cluster del servizio Azure Kubernetes, viene creato automaticamente un secondo gruppo di risorse per archiviare le risorse del servizio Azure Kubernetes. Vedere [Perché vengono creati due gruppi di risorse con il servizio Azure Kubernetes?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials). Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> Il comando riportato sopra usa il percorso predefinito del [file di configurazione di Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), ossia `~/.kube/config`. È possibile specificare un percorso diverso per il file di configurazione di Kubernetes usando *--file*.

Per verificare la connessione al cluster, usare il comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) per restituire un elenco di nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'esempio di output seguente mostra il nodo singolo creato nei passaggi precedenti. Assicurarsi che lo stato del nodo sia impostato su *Pronto*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Creare un server flessibile di Database di Azure per MySQL
Creare un server flessibile con il comando [az mysql flexible-server create](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true). Il comando seguente crea un server usando le impostazioni predefinite del servizio e i valori del contesto locale dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Il server creato ha gli attributi seguenti:
- La prima volta che si effettua il provisioning del server, viene creato un nuovo database vuoto, ```flexibleserverdb```. In questa guida di avvio rapido verrà usato questo database.
- Nome del server, nome utente amministratore, password amministratore, nome del gruppo di risorse (se non è già specificato nel contesto locale) generati automaticamente e nella stessa località del gruppo di risorse
- Impostazioni predefinite del servizio per le configurazioni server rimanenti: livello di calcolo (con possibilità di burst), dimensioni/SKU di calcolo (B1MS), periodo di conservazione dei backup (7 giorni) e versione di MySQL (5.7)
- L'uso dell'argomento public-access consente di creare un server con l'accesso pubblico protetto da regole del firewall. Specificare il proprio indirizzo IP da aggiungere alla regola del firewall per consentire l'accesso dal computer client.
- Poiché il comando usa il contesto locale, creerà il server nel gruppo di risorse ```wordpress-project``` e nell'area ```eastus```.


### <a name="build-your-wordpress-docker-image"></a>Creare l'immagine Docker di WordPress

Scaricare l'[ultima versione di WordPress](https://wordpress.org/download/). Creare una nuova directory ```my-wordpress-app``` per il progetto e usare questa semplice struttura della cartella

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Rinominare ```wp-config-sample.php``` in ```wp-config.php``` e sostituire le righe da 21 a 32 con questo frammento di codice. Il frammento di codice seguente legge l'host di database, il nome utente e la password dal file manifesto di Kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Creare un Dockerfile
Creare un nuovo Dockerfile e copiare questo frammento di codice. Usare questo Dockerfile per configurare un server Web Apache con PHP e abilitare l'estensione mysqli.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Creare l'immagine Docker
Assicurarsi di trovarsi nella directory ```my-wordpress-app``` in un terminale usando il comando ```cd```. Eseguire il comando seguente per creare l'immagine:

``` bash

docker build --tag myblog:latest .

```

Distribuire l'immagine nell'[hub Docker](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) o in [Registro Azure Container](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se si usa Registro Azure Container, eseguire il comando ```az aks update``` per collegare l'account del registro al cluster del servizio Azure Kubernetes.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Creare il file manifesto di Kubernetes

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. Creare un file manifesto denominato `mywordpress.yaml` e copiarlo nella definizione YAML seguente.

>[!IMPORTANT]
> - Sostituire ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` con il nome e il tag effettivi dell'immagine Docker WordPress, ad esempio ```docker-hub-user/myblog:latest```.
> - Aggiornare la sezione ```env``` con i valori di ```SERVERNAME```, ```YOUR-DATABASE-USERNAME```, ```YOUR-DATABASE-PASSWORD``` del server flessibile MySQL.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Distribuire WordPress nel cluster del servizio Azure Kubernetes
Distribuire l'applicazione usando il comando [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e specificare il nome del manifesto YAML:

```console
kubectl apply -f mywordpress.yaml
```

L'output di esempio seguente mostra le distribuzioni e i servizi creati correttamente:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Inizialmente il valore *EXTERNAL-IP* per il servizio *wordpress-blog* è mostrato *in sospeso*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Esplorare WordPress

Aprire un Web browser all'indirizzo IP esterno del servizio per visualizzare la pagina di installazione di WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Installazione di Wordpress riuscita nel servizio Azure Kubernetes e nel server flessibile MySQL":::

>[!NOTE]
> - Attualmente il sito WordPress non usa HTTPS. È consigliabile [abilitare TLS con i propri certificati](../../aks/ingress-own-tls.md).
> - È possibile abilitare il [routing HTTP](../../aks/http-application-routing.md) per il cluster.

## <a name="clean-up-the-resources"></a>Pulire le risorse

Per evitare addebiti per Azure, è necessario eliminare le risorse non necessarie.  Quando il cluster non è più necessario, usare il comando [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione](../../aks/kubernetes-service-principal.md#additional-considerations). Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [accedere al dashboard Web di Kubernetes](../../aks/kubernetes-dashboard.md) per il cluster del servizio Azure Kubernetes
- Informazioni su come [dimensionare il cluster](../../aks/tutorial-kubernetes-scale.md)
- Informazioni su come gestire il [server flessibile MySQL](./quickstart-create-server-cli.md)
- Informazioni su come [configurare i parametri del server ](./how-to-configure-server-parameters-cli.md) di database.

