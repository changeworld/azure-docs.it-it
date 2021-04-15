---
title: "Esercitazione: Distribuire Django in un cluster del servizio Azure Kubernetes con il server flessibile PostgreSQL usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare e distribuire rapidamente app Django nel servizio Azure Kubernetes con il server flessibile di Database di Azure per PostgreSQL.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b79b470a25a63c0a46ddef94ee65f47f37c560cb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477809"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Esercitazione: Distribuire un'app Django nel servizio Azure Kubernetes con il server flessibile di Database di Azure per PostgreSQL

In questa guida di avvio rapido si distribuisce un'applicazione Django nel servizio Azure Kubernetes con il server flessibile di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure.

Il **[servizio Azure Kubernetes](../../aks/intro-kubernetes.md)** è un servizio Kubernetes gestito che consente di distribuire e gestire rapidamente i cluster. Il **[server flessibile di Database di Azure per PostgreSQL (anteprima)](overview.md)** è un servizio di database completamente gestito progettato per offrire un controllo più granulare e una maggiore flessibilità rispetto alle funzioni di gestione e alle impostazioni di configurazione del database.

> [!NOTE]
> - Il server flessibile di Database di Azure per PostgreSQL è attualmente disponibile in anteprima pubblica
> - Questa guida di avvio rapido presuppone una conoscenza di base dei concetti relativi a Kubernetes, Django e PostgreSQL.

## <a name="pre-requisites"></a>Prerequisiti
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Usare [Azure Cloud Shell](../../cloud-shell/quickstart.md) tramite l'ambiente bash.

   [![Incorpora avvio](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)  
- Se si preferisce, [installare](/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure per eseguire i relativi comandi di riferimento.
  - Se si usa un'installazione locale, accedere all'interfaccia della riga di comando usando il comando [az login](/cli/azure/reference-index#az-login).  Per completare il processo di autenticazione, seguire la procedura visualizzata nel terminale.  Per altre opzioni di accesso, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).
  - Quando richiesto, installare le estensioni dell'interfaccia della riga di comando di Azure al primo utilizzo.  Per altre informazioni sulle estensioni, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).
  - Eseguire [az version](/cli/azure/reference-index?#az_version) per trovare la versione e le librerie dipendenti installate. Per eseguire l'aggiornamento alla versione più recente, eseguire [az upgrade](/cli/azure/reference-index?#az_upgrade). Questo articolo richiede l'ultima versione dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

> [!NOTE]
> Se si eseguono i comandi in questo avvio rapido in locale (anziché con Azure Cloud Shell), assicurarsi di eseguire i comandi come amministratore.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse denominato *django-project* usando il comando [az group create][az-group-create] nella località *eastus*.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> La località del gruppo di risorse è quella in cui vengono archiviati i relativi metadati. È anche quella in cui viene eseguita la risorsa in Azure se non si specifica un'area diversa durante la creazione.

L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container

Usare il comando [az aks create](/cli/azure/aks#az-aks-create) per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Questa operazione richiede alcuni minuti.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

> [!NOTE]
> Quando si crea un cluster del servizio Azure Kubernetes, viene creato automaticamente un secondo gruppo di risorse per archiviare le risorse del servizio Azure Kubernetes. Vedere [Perché vengono creati due gruppi di risorse con il servizio Azure Kubernetes?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli](/cli/azure/aks#az-aks-install-cli):

```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials). Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Creare un server flessibile di Database di Azure per PostgreSQL
Creare un server flessibile con il comando [az postgreSQL flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create). Il comando seguente crea un server usando le impostazioni predefinite del servizio e i valori del contesto locale dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

Il server creato ha gli attributi seguenti:
- La prima volta che si effettua il provisioning del server, viene creato un nuovo database vuoto, ```postgres```. In questa guida di avvio rapido verrà usato questo database.
- Nome del server, nome utente amministratore, password amministratore, nome del gruppo di risorse (se non è già specificato nel contesto locale) generati automaticamente e nella stessa località del gruppo di risorse
- Impostazioni predefinite del servizio per le configurazioni server rimanenti: livello di calcolo (per utilizzo generico), dimensioni/SKU di calcolo (Standard_D2s_v3 che usa 2 vCore), periodo di conservazione dei backup (7 giorni) e versione di PostgreSQL (12)
- L'uso dell'argomento public-access consente di creare un server con l'accesso pubblico protetto da regole del firewall. Specificare il proprio indirizzo IP da aggiungere alla regola del firewall per consentire l'accesso dal computer client.
- Poiché il comando usa il contesto locale, creerà il server nel gruppo di risorse ```django-project``` e nell'area ```eastus```.


## <a name="build-your-django-docker-image"></a>Creare l'immagine Docker di Django

Creare una nuova [applicazione Django](https://docs.djangoproject.com/en/3.1/intro/) o usare il progetto Django esistente. Verificare che il codice si trovi in questa struttura di cartelle.

```
â””â”€â”€â”€my-djangoapp
    â””â”€â”€â”€views.py
    â””â”€â”€â”€models.py
    â””â”€â”€â”€forms.py
    â”œâ”€â”€â”€templates
          . . . . . . .
    â”œâ”€â”€â”€static
         . . . . . . .
â””â”€â”€â”€my-django-project
    â””â”€â”€â”€settings.py
    â””â”€â”€â”€urls.py
    â””â”€â”€â”€wsgi.py
        . . . . . . .
    â””â”€â”€â”€ Dockerfile
    â””â”€â”€â”€ requirements.txt
    â””â”€â”€â”€ manage.py
    
```
Aggiornare ```ALLOWED_HOSTS``` in ```settings.py``` per assicurarsi che l'applicazione Django usi l'indirizzo IP esterno che viene assegnato all'app kubernetes.

```python
ALLOWED_HOSTS = ['*']
```

Aggiornare la sezione ```DATABASES={ }``` del file ```settings.py```. Il frammento di codice seguente legge l'host di database, il nome utente e la password dal file manifesto di Kubernetes.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Generare un file requirements.txt
Creare un file ```requirements.txt``` per elencare le dipendenze per l'applicazione Django. Ecco un esempio di file ```requirements.txt```. È possibile usare [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) per generare un file requirements.txt per l'applicazione esistente.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Creare un Dockerfile
Creare un nuovo file denominato ```Dockerfile``` e copiare il frammento di codice seguente. Questo Dockerfile viene usato per configurare Python 3.8 e installare tutti i requisiti elencati nel file requirements.txt.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Creare l'immagine
Assicurarsi di trovarsi nella directory ```my-django-app``` in un terminale usando il comando ```cd```. Eseguire il comando seguente per creare l'immagine di BBS:

``` bash

docker build --tag myblog:latest .

```

Distribuire l'immagine nell'[hub Docker](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) o in [Registro Azure Container](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se si usa Registro Azure Container, eseguire il comando ```az aks update``` per collegare l'account del registro al cluster del servizio Azure Kubernetes.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Creare il file manifesto di Kubernetes

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. Creare un file manifesto denominato ```djangoapp.yaml``` e copiarlo nella definizione YAML seguente.

>[!IMPORTANT]
> - Sostituire ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` con il nome e il tag effettivi dell'immagine Docker di Django, ad esempio ```docker-hub-user/myblog:latest```.
> - Aggiornare la sezione ```env``` seguente con i valori di ```SERVERNAME```, ```YOUR-DATABASE-USERNAME```, ```YOUR-DATABASE-PASSWORD``` del server flessibile Postgres.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Distribuire Django nel cluster del servizio Azure Kubernetes
Distribuire l'applicazione usando il comando [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e specificare il nome del manifesto YAML:

```console
kubectl apply -f djangoapp.yaml
```

L'output di esempio seguente mostra le distribuzioni e i servizi creati correttamente:

```output
deployment "django-app" created
service "python-svc" created
```

Un servizio ```django-app``` consente di descrivere i dettagli della distribuzione, ad esempio le immagini da usare per l'app, il numero di pod e la configurazione dei pod. Viene creato un servizio ```python-svc``` per esporre l'applicazione tramite un indirizzo IP esterno.

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service django-app --watch
```

Inizialmente il valore di *EXTERNAL-IP* per il servizio *django-app* è visualizzato come *pending*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Ora aprire un Web browser all'indirizzo IP esterno del servizio per visualizzare l'applicazione Django.  

>[!NOTE]
> - Attualmente il sito Django non usa HTTPS. È consigliabile [abilitare TLS con i propri certificati](../../aks/ingress-own-tls.md).
> - È possibile abilitare il [routing HTTP](../../aks/http-application-routing.md) per il cluster. Quando è abilitato, il routing HTTP configura un controller in ingresso nel cluster del servizio Azure Kubernetes. Man mano che le applicazioni vengono distribuite, la soluzione crea anche nomi DNS pubblicamente accessibili per gli endpoint applicazione.

## <a name="run-database-migrations"></a>Eseguire le migrazioni del database

Per qualsiasi applicazione Django, è necessario eseguire la migrazione del database o raccogliere file statici. È possibile eseguire questi comandi della shell Django usando ```$ kubectl exec <pod-name> -- [COMMAND]```.  Prima di eseguire il comando è necessario trovare il nome del pod usando ```kubectl get pods```. 

```bash
$ kubectl get pods
```

Verrà visualizzato un output simile al seguente:
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Una volta trovato il nome del pod, è possibile eseguire le migrazioni di database Django con il comando ```$ kubectl exec <pod-name> -- [COMMAND]```. Si noti che ```/code/``` è la directory di lavoro per il progetto definito nel ```Dockerfile``` precedente.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

L'output sarà simile al seguente: 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Se si verificano problemi, eseguire ```kubectl logs <pod-name>``` per vedere quale eccezione viene generata dall'applicazione. Se l'applicazione funziona correttamente, verrà visualizzato un output simile al seguente quando si esegue ```kubectl logs```.

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Pulire le risorse

Per evitare addebiti per Azure, è necessario eliminare le risorse non necessarie.  Quando il cluster non è più necessario, usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione](../../aks/kubernetes-service-principal.md#additional-considerations). Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [accedere al dashboard Web di Kubernetes](../../aks/kubernetes-dashboard.md) per il cluster del servizio Azure Kubernetes
- Informazioni su come [abilitare la distribuzione continua](../../aks/deployment-center-launcher.md)
- Informazioni su come [dimensionare il cluster](../../aks/tutorial-kubernetes-scale.md)
- Informazioni su come gestire il [server flessibile Postgres](./quickstart-create-server-cli.md)
- Informazioni su come [configurare i parametri del server ](./howto-configure-server-parameters-using-cli.md) di database.
