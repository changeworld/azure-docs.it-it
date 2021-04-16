---
title: Distribuire un'applicazione in Azure Red Hat OpenShift usando OpenShift Serverless
description: Informazioni su come distribuire un'applicazione in Azure Red Hat OpenShift usando OpenShift Serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverless
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532836"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Distribuire un'applicazione in Azure Red Hat OpenShift usando OpenShift Serverless

In questo articolo si distribuisce un'applicazione in un cluster Azure Red Hat OpenShift usando [OpenShift Serverless.](https://www.openshift.com/learn/topics/serverless) OpenShift Serverless consente agli sviluppatori di distribuire ed eseguire applicazioni che verranno ridimensionate a zero su richiesta, eliminando il consumo di risorse quando non sono in uso.

Il codice dell'applicazione può essere in pacchetto in un contenitore insieme ai runtime appropriati e la funzionalità serverless avvia i contenitori dell'applicazione quando vengono attivati da un evento. Le applicazioni possono essere attivate da varie origini eventi, ad esempio eventi dalle proprie applicazioni, servizi cloud di più provider, sistemi SaaS (Software as a Service) e altri servizi.

La gestione di tutti gli aspetti della distribuzione di qualsiasi contenitore in modalità serverless è incorporata direttamente nell'interfaccia OpenShift. Gli sviluppatori possono identificare visivamente gli eventi che stanno guidando il lancio delle applicazioni in contenitori, con diversi modi per modificare i parametri degli eventi. Le applicazioni serverless OpenShift possono essere integrate con altri servizi OpenShift, ad esempio OpenShift Pipelines, Service Mesh e Monitoring, offrendo un'esperienza completa di sviluppo e distribuzione di applicazioni serverless.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Installare lo strumento da riga di comando per lo strumento da riga di comando (tool)

È possibile scaricare la versione più recente dell'interfaccia della riga di comando appropriata per il computer da <https://github.com/knative/client/releases/>

Se si eseguono i comandi nel Azure Cloud Shell, scaricare la versione più recente dell'interfaccia della riga di comando diLateative per Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Avviare la console Web

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

## <a name="install-the-openshift-serverless-operator"></a>Installare l'operatore serverless OpenShift

Dopo aver eseguito l'accesso alla console Web OpenShift, assicurarsi di avere la prospettiva *di amministratore.* Aprire *l'hub operatore* e cercare l'operatore **OpenShift Serverless** e selezionarlo.

![Cercare l'operatore Serverless OpenShift](media/serverless/serverless-operatorhub.png)

Aprire quindi la pagina di installazione dell'operatore facendo clic su **Installa**.

![Fare clic su Installa per installare l'operatore](media/serverless/serverless-clickinstall.png)

Scegliere il canale *di aggiornamento appropriato* per la versione del cluster Azure Red Hat OpenShift e installare l'operatore nello spazio dei nomi `openshift-serverless` . Scorrere verso il basso e fare clic **su Installa**.

![Pagina di installazione dell'operatore](media/serverless/serverless-installpage.png)

In pochi minuti, la pagina di stato dovrebbe riflettere che l'operatore è installato ed è pronto per l'uso. Fare clic sul **pulsante Visualizza operatore** per continuare.

![L'operatore è installato ed è pronto per l'uso](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Installare Knative Serving

La possibilità di eseguire qualsiasi contenitore in modalità serverless in OpenShift Serverless è possibile usando Knative upstream. Knative estende Kubernetes per fornire un set di componenti per la distribuzione, l'esecuzione e la gestione di applicazioni moderne usando la metodologia serverless.

### <a name="create-an-instance-of-the-knative-serving"></a>Creare un'istanza di Knative Serving

Passare allo spazio dei nomi facendo clic sull'elenco a discesa del progetto in alto a sinistra, quindi in API fornite fare clic su Crea istanza nella `knative-serving` scheda Servizio *Knative.*  

![Fare clic per creare l'istanza del servizio Knative](media/serverless/serverless-createknativeserving.png)

Mantenere le impostazioni predefinite e scorrere verso il basso nella pagina *Crea servizio Knative* per fare clic sul **pulsante** Crea.

![Mantenere le impostazioni predefinite e fare clic su Crea](media/serverless/serverless-createknativeserving2.png)

Attendere che *nella colonna Stato* sia visualizzato **Pronto,** è necessario installare OpenShift Serverless e si è pronti per creare un progetto OpenShift Serverless.

![Pronto per la servita coltivativa](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Creare un progetto serverless

Per creare un nuovo progetto denominato `demoserverless` , eseguire il comando :

```azurecli-interactive
oc new-project demoserverless
```

Verrà visualizzato un output simile al seguente:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Passare alla prospettiva *Sviluppatore* anziché alla prospettiva *Amministratore* nel menu a sinistra e selezionare `demoserverless` nell'elenco dei progetti. Si dovrebbe quindi essere nella *pagina Topologia* per il progetto.

![Azure Red Hat OpenShift topologia del progetto](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Distribuzione tramite la console Web

Nelle opzioni presentate per la distribuzione di un'applicazione selezionare *Da Git.* Verrà visualizzata la pagina *Importa da* Git. Usare `https://github.com/sclorg/django-ex.git` come URL del repository **Git.** L'applicazione Web di esempio viene implementata usando il linguaggio di programmazione Python.

![Azure Red Hat OpenShift progetto da Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift rileva che si tratta di un progetto Python e seleziona l'immagine del generatore appropriata.

Scorrere verso il *basso fino a Risorse* e assicurarsi che **l'opzione Servizio** di avvio e manutenzione sia selezionata come tipo di risorsa da generare. Questa azione creerà un servizio di pianificazione, un tipo di distribuzione che abilita il ridimensionamento serverless di OpenShift a zero quando è inattivo.

![Azure Red Hat OpenShift progetto - Arsiativo](media/serverless/serverless-knative.png)


Quando si è pronti, nella parte inferiore della pagina fare clic su **Crea.** Verranno create risorse per gestire la compilazione e la distribuzione dell'applicazione. Si verrà quindi reindirizzati alla panoramica della topologia per il progetto.

La panoramica della topologia offre una rappresentazione visiva dell'applicazione distribuita. Con questa visualizzazione è possibile visualizzare la struttura complessiva dell'applicazione.

Attendere il completamento del processo di compilazione. L'operazione potrebbe richiedere alcuni minuti. Al termine della compilazione, nell'angolo inferiore sinistro del servizio viene visualizzato un segno di spunta verde.

![Azure Red Hat OpenShift topologia del progetto - Compilazione riuscita](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Visualizzare la scalabilità dell'applicazione

*Nell'elenco Opzioni* di visualizzazione nella parte superiore della visualizzazione Topologia fare clic su *Numero di pod.* Attendere che il numero di pod si ridimensiona a zero. La riduzione può richiedere alcuni minuti.

![Azure Red Hat OpenShift topologia del progetto - Ridimensionata a zero](media/serverless/serverless-scaledtozero.png)

Fare clic *sull'icona Open URL (Apri URL)* nell'angolo in alto a destra del pannello Disagi. L'applicazione viene aperta in una nuova scheda. Chiudere la nuova scheda del browser e tornare alla visualizzazione Topologia. Nella visualizzazione Topologia è possibile vedere che l'applicazione è stata ridimensionata fino a un pod per soddisfare la richiesta. Dopo alcuni minuti, l'applicazione torna a zero pod.

![Azure Red Hat OpenShift topologia del progetto - Ridimensionata a uno](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Forzare una nuova revisione e impostare la distribuzione del traffico

A ogni aggiornamento della configurazione di un servizio, viene creata una nuova revisione per il servizio. Per impostazione predefinita, l'itinerario del servizio punta tutto il traffico alla revisione pronta più recente. È possibile modificare questo comportamento definendo quale revisione ottiene una parte del traffico. I servizi di supporto consentono il mapping del traffico, il che significa che è possibile eseguire il mapping delle revisioni di un servizio a una parte allocata del traffico. Il mapping del traffico offre anche un'opzione per creare URL univoci per revisioni specifiche.

In *Topologia fare clic* sulla revisione all'interno del servizio per visualizzarne i dettagli. Le notifiche sotto l'anello Pod e nella parte superiore del pannello dei dettagli devono essere `(REV)` . Nel pannello laterale, all'interno *della scheda Risorse,* scorrere verso il basso e fare clic sulla configurazione associata al servizio.

![Azure Red Hat OpenShift topologia del progetto - Revisione](media/serverless/serverless-revdetails.png)

Forzare un aggiornamento della configurazione passando alla *scheda YAML* e scorrendo verso il basso per modificare il valore `timeoutSeconds` di in modo che sia `301` . Fare clic **su Salva** per salvare la configurazione aggiornata. In uno scenario reale, questo aggiornamento della configurazione può essere attivato anche aggiornando il tag dell'immagine del contenitore.

![Forzare una nuova revisione aggiornando la configurazione](media/serverless/serverless-confupdate.png)

Tornare alla visualizzazione *Topologia.* Si noti che è stata distribuita una nuova revisione. Fare clic sul servizio che termina con la notifica e fare clic sul pulsante Set Traffic Distribution (Imposta distribuzione traffico). A questo punto dovrebbe essere possibile dividere il traffico tra le `(KSVC)` diverse revisioni del servizio. 

![Impostare la distribuzione del traffico](media/serverless/serverless-trafficdist.png)

La *visualizzazione Topologia* mostra ora come viene distribuito il traffico tra le due revisioni.

![Esaminare la distribuzione del traffico](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Uso dello strumento da riga di comando per lo strumento da riga di comando (stile)

Nei passaggi precedenti è stata usata la console Web OpenShift per creare e distribuire un'applicazione in OpenShift serverless. Dal momento che OpenShift Serverless esegue Sotto, è anche possibile usare lo strumento da riga di comando Perfative per creare i servizi per lo stile di vita.

> [!NOTE]
> Se l'interfaccia della riga di comando non è già stata installata, assicurarsi di seguire la procedura descritta `kn` nella sezione dei prerequisiti di questo articolo. Assicurarsi anche di aver eseguito l'accesso con lo strumento da riga di comando OpenShift `oc` .

Si userà un'immagine del contenitore già compilata in `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Distribuire un servizio

Per distribuire il servizio, eseguire il comando seguente:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Verrà visualizzato un output simile al seguente.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

È possibile recuperare un elenco di route nel progetto eseguendo:

```azurecli-interactive
kn route list
```

Si otterrà un elenco di route nello spazio dei nomi . Aprire l'URL in un browser per visualizzare il servizio distribuito.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Distribuire una nuova versione del servizio

Distribuire una nuova versione dell'applicazione eseguendo il comando seguente, passando il `:latest` tag image e una variabile di ambiente `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Si otterrà una conferma che è stata distribuita una `greeter-v2` nuova revisione.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Per visualizzare un elenco di tutte le revisioni e la relativa distribuzione del traffico, eseguire il comando seguente:

```azurecli-interactive
kn revision list
```

Si otterrà un elenco simile al seguente. Si noti che la nuova revisione riceve il 100% del traffico.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Distribuzioni blu/verde e canary

Per impostazione predefinita, quando viene distribuita una nuova revisione, ottiene il 100% del traffico. Si vuole implementare una strategia di distribuzione blu/verde in cui è possibile eseguire rapidamente il rollback alla versione precedente dell'applicazione. Knative semplifica questa operazione.

Aggiornare il servizio per creare tre tag di traffico inviando il 100% del traffico al

- **current:** punta alla versione distribuita corrente
- **prev**: punta alla versione precedente
- **latest**: punta sempre alla versione più recente

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Si otterrà una conferma simile alla seguente.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Elencare le route usando il comando seguente:

```azurecli-interactive
kn route describe greeter
```

Si otterrà un output che mostra gli URL per ognuno dei tag insieme alla distribuzione del traffico.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Si vuole eseguire rapidamente il rollback alla versione precedente. È possibile aggiornare la distribuzione del traffico per inviare il 100% del traffico al tag precedente:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Controllare di nuovo elencando le route usando il comando seguente:

```azurecli-interactive
kn route describe greeter
```

Si otterrà un output che mostra che il 100% della distribuzione del traffico è in corso alla versione precedente.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

È possibile spostarsi con la distribuzione del traffico durante l'aggiornamento della route principale ( `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` in questo caso) nel browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'applicazione, è possibile eseguire il comando seguente per eliminare il progetto:

```azurecli-interactive
oc delete project demoserverless
```

È anche possibile eliminare il cluster seguendo le istruzioni in [Esercitazione: Eliminare un cluster Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come:
> [!div class="checklist"]
>
> * Installare l'operatore serverless OpenShift e il servizio di gestione dei servizi
> * Distribuire un progetto serverless usando la console Web
> * Distribuire un progetto serverless usando l'interfaccia della riga di comando di Testativo (ace)
> * Configurare le distribuzioni blu/verde e le distribuzioni canary usando l'interfaccia della riga di comando Diastolate

Per altre informazioni su come compilare e distribuire applicazioni serverless e guidate da eventi in Azure Red Hat OpenShift usando [OpenShift Serverless,](https://www.openshift.com/learn/topics/serverless)vedere la documentazione Introduzione a [OpenShift serverless](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) e la documentazione Creazione e gestione di applicazioni [serverless.](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html)
