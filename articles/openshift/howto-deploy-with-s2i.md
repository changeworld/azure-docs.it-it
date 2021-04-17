---
title: Distribuire un'applicazione dall'origine Azure Red Hat OpenShift
description: Informazioni su come distribuire un'applicazione Azure Red Hat OpenShift codice sorgente usando la strategia di compilazione Da origine a immagine (S2I)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, source to image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559122"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Distribuire un'applicazione dall'origine Azure Red Hat OpenShift

In questo articolo si distribuisce un'applicazione in un cluster Azure Red Hat OpenShift dal codice sorgente usando una compilazione da origine a immagine (S2I). [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) è un processo di compilazione per la compilazione di immagini di contenitori riproducibili dal codice sorgente. S2I produce immagini pronte per l'esecuzione tramite l'inserimento di codice sorgente in un'immagine del contenitore e consentendo al contenitore di preparare il codice sorgente per l'esecuzione. È possibile fare in modo che OpenShift compilare un'applicazione dall'origine per distribuirla, quindi non è necessario costruire un contenitore manualmente con ogni modifica. OpenShift può quindi compilare e distribuire automaticamente nuove versioni quando viene notificata la modifica del codice sorgente.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Creare un progetto

Per creare un nuovo progetto denominato `demoproject` , eseguire il comando :

```azurecli-interactive
oc new-project demoproject
```

Verrà visualizzato un output simile al seguente:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Avviare la console Web

Trovare l'URL della console Web del cluster eseguendo:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Si dovrebbe ottenere un URL simile a questo.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Avviare l'URL della console in un browser e accedere usando le credenziali `kubeadmin`.

:::image type="content" source="media/login.png" alt-text="Schermata di accesso ad Azure Red Hat OpenShift":::

Passare alla prospettiva *Sviluppatore* anziché alla prospettiva *Amministratore* nel menu a sinistra e selezionare `demoproject` nell'elenco dei progetti. Si dovrebbe quindi essere nella *pagina Topologia* per il progetto.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift topologia del progetto":::

Poiché il progetto è vuoto, non dovrebbero essere trovati carichi di lavoro e verranno presentate varie opzioni per la distribuzione di un'applicazione.

## <a name="deploying-using-the-web-console"></a>Distribuzione tramite la console Web

Dalle opzioni presentate per la distribuzione di un'applicazione selezionare *Da Git.* Verrà visualizzata la pagina *Importa da* Git. Usare `https://github.com/sclorg/django-ex.git` come URL del repository **Git.** L'applicazione Web di esempio viene implementata usando il linguaggio di programmazione Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Azure Red Hat OpenShift progetto da Git":::

> [!NOTE]
> OpenShift rileva che si tratta di un progetto Python e seleziona l'immagine del generatore appropriata.

Scorrere verso il *basso fino a Opzioni* avanzate e assicurarsi che **l'opzione Crea una route per l'applicazione** sia selezionata. Questa azione creerà una route OpenShift, un modo per esporre un servizio fornendo un nome host raggiungibile esternamente.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Azure Red Hat OpenShift progetto da Git - Configurazione della route":::

Quando si è pronti, nella parte inferiore della pagina fare clic su **Crea.** Verranno create risorse per gestire la compilazione e la distribuzione dell'applicazione. Si verrà quindi reindirizzati alla panoramica della topologia per il progetto.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Azure Red Hat OpenShift progetto da Git - Topologia":::

La panoramica della topologia offre una rappresentazione visiva dell'applicazione distribuita. Con questa visualizzazione è possibile visualizzare la struttura complessiva dell'applicazione.

È possibile fare clic sull'icona Git per accedere al repository Git da cui è stato compilato il codice sorgente per l'applicazione. L'icona visualizzata in basso a sinistra mostra lo stato di compilazione dell'applicazione. Facendo clic su questa icona verrà visualizzata la sezione dei dettagli della compilazione. Se l'applicazione ha esposto route, è possibile fare clic sull'icona in alto a destra per aprire l'URL per la route dell'applicazione creata.

Durante l'aumento o la riduzione delle dimensioni dell'applicazione, l'avvio delle implementazioni e la ricreazione dei pod, la rappresentazione dell'applicazione nella visualizzazione topologia verrà animata per offrire una visualizzazione in tempo reale delle operazioni in corso.

Facendo clic sull'icona dell'applicazione verranno visualizzati altri dettagli, come illustrato di seguito.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Azure Red Hat OpenShift progetto da Git - Dettagli":::

## <a name="viewing-the-builder-logs"></a>Visualizzazione dei log del generatore

Dopo aver avviato la compilazione, fare clic sul *collegamento Visualizza* log visualizzato nel *pannello* Risorse.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Azure Red Hat OpenShift progetto da Git - Log di compilazione":::

In questo modo sarà possibile monitorare lo stato di avanzamento della compilazione durante l'esecuzione. L'immagine del generatore, Python in questo caso, inserirà il codice sorgente dell'applicazione nell'immagine finale prima di eseguire il push nel registro immagini interno di OpenShift. La compilazione verrà completata correttamente quando viene visualizzato il messaggio finale "Push riuscito".

## <a name="accessing-the-application"></a>Accesso all'applicazione

Al termine della compilazione dell'immagine dell'applicazione, verrà distribuita.

Fare clic *su Topologia* nella barra dei menu a sinistra per tornare alla visualizzazione topologia per il progetto. Quando l'applicazione è stata creata  usando la console Web, è stata creata automaticamente una route per l'applicazione e verrà esposta all'esterno del cluster. L'URL che può essere usato per accedere all'applicazione da un Web browser era visibile nella *scheda* Risorse per l'applicazione visualizzata in precedenza.

Dalla visualizzazione topologia è possibile accedere all'URL dell'applicazione distribuita facendo clic sull'icona in alto a destra dell'anello. Al termine della distribuzione, fare clic sull'icona per visualizzare l'applicazione distribuita.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Azure Red Hat OpenShift progetto da Git - Esplorare l'app":::

## <a name="deploying-using-the-command-line"></a>Distribuzione tramite la riga di comando

Si è appreso come distribuire un'applicazione usando la console Web, ora consente di distribuire la stessa applicazione Web, ma questa volta usando lo strumento `oc` da riga di comando.

Eseguire il comando seguente per eliminare il progetto e ricominciare da qui:

```azurecli-interactive
oc delete project demoproject
```

Verrà visualizzato un messaggio di conferma che `demoproject` l'oggetto è stato eliminato.

```output
project.project.openshift.io "demoproject" deleted
```

Creare di `demoproject` nuovo eseguendo:

```azurecli-interactive
oc new-project demoproject
```

All'interno del progetto creare una nuova applicazione dall'origine in GitHub, specificando il generatore S2I per la versione più recente di Python fornita.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

L'output dovrebbe essere simile al seguente:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift userà il nome del repository Git come nome per l'applicazione. Esaminare lo stato della compilazione e della distribuzione eseguendo:

```azurecli-interactive
oc status
```

Al termine della compilazione e della distribuzione, verrà visualizzato un output simile a .

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Per esporre l'applicazione all'esterno del cluster OpenShift, è necessario creare una route eseguendo:

```azurecli-interactive
oc expose service/django-ex
```

Verrà visualizzata una conferma.

```output
route.route.openshift.io/django-ex exposed
```

Recuperare l'URL eseguendo:

```azurecli-interactive
oc get route django-ex
```

Si dovrebbe ottenere il nome host assegnato alla route che è possibile usare per passare all'applicazione distribuita.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Attivazione di una nuova compilazione binaria

Quando si lavora all'applicazione, è probabile che si voglia apportare modifiche e vederle distribuite. È possibile configurare facilmente un webhook che attiverà una nuova compilazione e una nuova distribuzione a ogni commit del codice. Tuttavia, questa operazione potrebbe non essere utile perché a volte si desidera visualizzare le modifiche senza dover eseguire il push di ogni modifica del codice nel repository.

Nei casi in cui si sta rapidamente iterando le modifiche, è possibile usare la cosiddetta compilazione binaria. Per illustrare questo scenario, clonare il repository Git per l'applicazione in locale eseguendo:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Verrà creata una sottodirectory contenente `django-ex` il codice sorgente per l'applicazione:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Passare alla sottodirectory:

```azurecli-interactive
cd django-ex
```

Aprire l'editor Azure Cloud Shell integrato:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Scorrere verso il basso e modificare la riga che `Welcome to your Django application on OpenShift` indica `Welcome to Azure Red Hat OpenShift` . Salvare il file e chiudere l'editor tramite `...` il menu in alto a destra.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift progetto da Git - Modificare l'applicazione nell Azure Cloud Shell editor":::

Avviare una nuova compilazione eseguendo il comando :

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Passando il flag , la riga di comando OpenShift carica il codice sorgente dalla directory specificata e quindi avvia `--from-dir=.` il processo di compilazione e distribuzione. Si dovrebbe ottenere un output simile al seguente e dopo alcuni minuti la compilazione dovrebbe essere completata.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Se si aggiorna il browser con l'applicazione, verrà visualizzato il titolo aggiornato.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Azure Red Hat OpenShift progetto da Git - Esplorare l'app aggiornata":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'applicazione, è possibile eseguire il comando seguente per eliminare il progetto:

```azurecli-interactive
oc delete project demoproject
```

È anche possibile eliminare il cluster seguendo le istruzioni in Esercitazione: Eliminare [un cluster Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come:
> [!div class="checklist"]
>
> * Creare un progetto
> * Distribuire un'applicazione dal codice sorgente usando la console Web
> * Distribuire un'applicazione dal codice sorgente usando la riga di comando OpenShift
> * Attivare una compilazione binaria usando la riga di comando OpenShift

Altre informazioni su come compilare e distribuire applicazioni usando da origine a immagine e [altre strategie di compilazione.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
