---
title: Apri mesh servizi (anteprima)
description: Aprire il servizio mesh (OSM) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106714"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Apri il componente aggiuntivo di Service mesh AKS (anteprima)

## <a name="overview"></a>Panoramica

[Open Service mesh (OSM)](https://docs.openservicemesh.io/) è una rete di servizi cloud nativa leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità predefinite per ambienti di microservizi altamente dinamici.

OSM esegue un piano di controllo basato su invio in Kubernetes, può essere configurato con le API [SMI](https://smi-spec.io/) e funziona inserendo un proxy inviato come contenitore sidecar accanto a ogni istanza dell'applicazione. Il proxy inviato contiene ed esegue le regole relative ai criteri di controllo di accesso, implementa la configurazione del routing e acquisisce le metriche. Il piano di controllo Configura continuamente i proxy per garantire che i criteri e le regole di routing siano aggiornati e che i proxy siano integri.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Funzionalità e funzionalità

OSM fornisce il seguente set di funzionalità e funzionalità per fornire una mesh del servizio nativa cloud per i cluster del servizio Kubernetes di Azure (AKS):

- Proteggere la comunicazione da servizio a servizio abilitando mTLS

- Caricare facilmente le applicazioni nella rete abilitando l'inserimento automatico sidecar del proxy inviato

- Configurazioni facili e trasparenti per lo spostamento del traffico nelle distribuzioni

- Possibilità di definire ed eseguire criteri di controllo degli accessi con granularità fine per i servizi

- Osservabilità e informazioni dettagliate sulle metriche dell'applicazione per il debug e il monitoraggio dei servizi

- Integrazione con servizi/soluzioni di gestione certificati esterni con un'interfaccia collegabile

## <a name="scenarios"></a>Scenari

OSM può supportare le distribuzioni AKS con gli scenari seguenti:

- Fornire comunicazioni crittografate tra gli endpoint di servizio distribuiti nel cluster

- Autorizzazione del traffico per il traffico HTTP/HTTPS e TCP nella rete

- Configurazione dei controlli di traffico ponderati tra due o più servizi per le distribuzioni A/B o Canarie

- Raccolta e visualizzazione degli indicatori KPI dal traffico delle applicazioni

## <a name="osm-service-quotas-and-limits-preview"></a>Quote e limiti del servizio OSM (anteprima)

Le limitazioni dell'anteprima di OSM per le quote e i limiti del servizio sono disponibili nella [pagina delle quote e dei limiti a livello](https://docs.microsoft.com/azure/aks/quotas-skus-regions)di area AKS.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Non tentare di installare OSM dal file binario usando `osm install` . Questo comporterà un'installazione di OSM che non è integrato come componente aggiuntivo per AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrare la `AKS-OpenServiceMesh` funzionalità di anteprima

Per creare un cluster AKS che può usare il componente aggiuntivo Open Service mesh, è necessario abilitare il `AKS-OpenServiceMesh` flag funzionalità per la sottoscrizione.

Registrare il `AKS-OpenServiceMesh` flag funzionalità usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Sono necessari alcuni minuti per visualizzare lo stato _Registered_. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse _Microsoft. servizio contenitore_ usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installare il componente aggiuntivo di Azure Kubernetes Service mesh (OSM) per un nuovo cluster del servizio contenitore di Azure (AKS)

Per un nuovo scenario di distribuzione del cluster AKS, si inizierà con una nuova distribuzione di un cluster AKS che Abilita il componente aggiuntivo OSM nell'operazione di creazione del cluster.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato _myOsmAksGroup_ nella località _eastus2_ (Region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Distribuire un cluster AKS con il componente aggiuntivo OSM abilitato

Verrà ora distribuito un nuovo cluster AKS con il componente aggiuntivo OSM abilitato.

> [!NOTE]
> Tenere presente che il comando di distribuzione AKS seguente usa dischi temporanei del sistema operativo. Altre informazioni sono disponibili qui informazioni sui [dischi del sistema operativo temporaneo per AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Ottenere le credenziali di accesso del cluster AKS

Ottenere le credenziali di accesso per il nuovo cluster Kubernetes gestito.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Abilita il componente aggiuntivo Azure Kubernetes Service mesh (OSM) per un cluster AKS esistente

Per uno scenario esistente del cluster AKS, sarà possibile abilitare il componente aggiuntivo OSM a un cluster AKS esistente che è già stato distribuito.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Abilitare il componente aggiuntivo OSM per il cluster AKS esistente

Per abilitare il componente aggiuntivo di AKS OSM, sarà necessario eseguire il `az aks enable-addons --addons` comando passando il parametro `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Verrà visualizzato un output simile all'output riportato di seguito per confermare che il componente aggiuntivo di AKS OSM è stato installato.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Convalidare l'installazione del componente aggiuntivo di AKS OSM

Sono disponibili diversi comandi da eseguire per verificare che tutti i componenti del componente aggiuntivo di AKS OSM siano abilitati e in esecuzione:

Per prima cosa è possibile eseguire una query sui profili componente aggiuntivo del cluster per verificare lo stato di abilitazione dei componenti aggiuntivi installati. Il comando seguente deve restituire "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

I comandi seguenti segnaleranno `kubectl` lo stato del controller OSM.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Accesso al componente aggiuntivo di AKS OSM

Attualmente è possibile accedere e configurare la configurazione del controller OSM tramite il configMap. Per visualizzare le impostazioni di configurazione del controller OSM, eseguire una query su OSM-config configMap via `kubectl` per visualizzare le impostazioni di configurazione.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output di OSM configMap dovrebbe essere simile al seguente:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Si noti che la **permissive_traffic_policy_mode** è configurata su **true**. La modalità di criteri di traffico permissiva in OSM è una modalità in cui l'applicazione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte delle regole di criteri del traffico del servizio mesh e programmi in ogni sidecar del proxy inviato per poter comunicare con questi servizi.

> [!WARNING]
> Prima di procedere, verificare che la modalità di criteri di traffico permissiva sia impostata su true **. in caso** contrario, modificarla con il comando seguente

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Consente di distribuire una nuova applicazione che verrà gestita dal componente aggiuntivo Azure Kubernetes Service mesh (OSM) del servizio Azure

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questa procedura dettagliata presuppongono che sia stato creato un cluster AKS (Kubernetes `1.19+` e versioni successive, con KUBERNETES RBAC abilitato), che sia stata stabilita una `kubectl` connessione con il cluster. se è necessaria assistenza per uno di questi elementi, vedere la [Guida introduttiva di AKS](./kubernetes-walkthrough.md)ed è stato installato il componente aggiuntivo OSM di AKS.

Devono essere installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.20.0 o successiva
- La `aks-preview` versione dell'estensione 0.5.5 o versioni successive
- OSM versione v 0.8.0 o successiva
- apt-get install JQ

### <a name="create-namespaces-for-the-application"></a>Creazione di spazi dei nomi per l'applicazione

In questa procedura dettagliata verrà usata l'applicazione libreria OSM con i servizi Kubernetes seguenti:

- bookbuyer
- bookthief
- libreria
- bookwarehouse

Creare gli spazi dei nomi per ognuno di questi componenti dell'applicazione.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Dovrebbe venire visualizzato l'output seguente:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboarding degli spazi dei nomi da gestire con OSM

Quando si aggiungono gli spazi dei nomi alla rete OSM, questo consentirà al controller OSM di inserire automaticamente i contenitori proxy sidecar di invio con l'applicazione. Eseguire il comando seguente per caricare gli spazi dei nomi dell'applicazione della libreria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione Bookstore nel cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Tutti gli output della distribuzione sono riepilogati di seguito.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Checkpoint: cosa è stato installato?

L'applicazione Bookstore di esempio è un'app a più livelli costituita da quattro servizi, ovvero bookbuyer, bookthief, Bookstore e bookwarehouse. Entrambi i servizi bookbuyer e bookthief comunicano con il servizio Bookstore per recuperare libri dal servizio Bookstore. Il servizio libreria recupera i libri dal servizio bookwarehouse per fornire bookbuyer e bookthief. Si tratta di una semplice applicazione a più livelli che funziona correttamente per illustrare il modo in cui è possibile usare una mesh di servizi per proteggere e autorizzare le comunicazioni tra i servizi delle applicazioni. Continuando con la procedura dettagliata, verranno abilitati e disabilitati i criteri SMI (Service mesh Interface) per consentire e impedire ai servizi di comunicare tramite OSM. Di seguito è riportato un diagramma dell'architettura di ciò che è stato installato per l'applicazione Bookstore.

![Architettura dell'app bookbuyer OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione Bookstore in esecuzione all'interno del cluster AKS

A questo punto è stata distribuita l'applicazione multi-container della libreria, ma è possibile accedervi solo dall'interno del cluster AKS. Le esercitazioni successive forniranno assistenza per esporre l'applicazione all'esterno del cluster tramite un controller di ingresso. Per il momento verrà usato il Port porting per accedere all'applicazione bookbuyer all'interno del cluster AKS per verificare che acquisti libri dal servizio Bookstore.

Per verificare che l'applicazione sia in esecuzione all'interno del cluster, verrà usata la porta avanti per visualizzare l'interfaccia utente dei componenti bookbuyer e bookthief.

Prima di tutto, ottenere il nome del Pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al Pod bookbuyer sarà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una volta ottenuto il nome del Pod, ora è possibile usare il comando Port-inoltr per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster AKS. Eseguire il comando seguente per configurare la porta in avanti per la porta di sistema locale 8080. Usare di nuovo il nome del Pod bookbuyer specificato.

> [!NOTE]
> Per tutti i comandi di Port porting è preferibile usare un terminale aggiuntivo, in modo che sia possibile continuare a lavorare con questa procedura dettagliata e non disconnettere il tunnel. È anche preferibile stabilire il tunnel di avanzamento della porta al di fuori della Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Verrà visualizzato un output simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Durante la sessione di invio della porta, passare all'URL seguente da un browser `http://localhost:8080` . A questo punto dovrebbe essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![Immagine dell'interfaccia utente dell'app bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Si noterà anche che il numero totale di libri acquistati continua a essere incrementato al servizio Bookstore V1. Il servizio Bookstore V2 non è stato ancora distribuito. Il servizio Bookstore v2 viene distribuito quando vengono illustrati i criteri di suddivisione del traffico SMI.

È anche possibile controllare lo stesso per il servizio bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

L'output dovrebbe essere simile al seguente. Al Pod bookthief sarà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Porta in poi al Pod bookthief.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Passare all'URL seguente da un browser `http://localhost:8080` . Si noterà che bookthief sta attualmente rubando libri dal servizio Bookstore. Successivamente, si implementeranno i criteri di traffico per arrestare il bookthief.

![Immagine dell'interfaccia utente dell'app bookthief OSM](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Disabilitare la modalità traffico permissivo OSM per la mesh

Come indicato in precedenza quando si visualizza la configurazione del cluster OSM, per impostazione predefinita la configurazione OSM Abilita i criteri di modalità traffico permissivo. In questa modalità, l'imposizione dei criteri di traffico viene ignorata e OSM individua automaticamente i servizi che fanno parte delle regole relative ai criteri di traffico del servizio mesh e programmi in ogni proxy inviato sidecar per poter comunicare con questi servizi.

A questo punto, verranno disabilitati i criteri di modalità traffico permissivo e per OSM verranno necessari criteri [SMI](https://smi-spec.io/) espliciti distribuiti nel cluster per consentire le comunicazioni nella rete da ogni servizio. Per disabilitare la modalità di traffico permissivo, eseguire il comando seguente per aggiornare la proprietà configMap modificando il valore da `true` a `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

L'output dovrebbe essere simile al seguente. Al Pod bookthief sarà aggiunto un nome univoco.

```Output
configmap/osm-config patched
```

Per verificare che la modalità di traffico permissiva sia stata disabilitata, portarlo di nuovo in bookbuyer o bookthief pod per visualizzare l'interfaccia utente nel browser e verificare se i libri acquistati o i libri rubati non vengono più incrementati. Assicurarsi di aggiornare il browser. Se l'incremento è stato interrotto, il criterio è stato applicato correttamente. Il bookthief è stato arrestato da un furto di libri, ma nessuno dei bookbuyer può acquistarlo dalla libreria, né la libreria può recuperare libri dalla bookwarehouse. Successivamente, si implementeranno i criteri [SMI](https://smi-spec.io/) per consentire solo i servizi nella rete a cui si vuole comunicare.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Applicare i criteri di accesso al traffico SMI (Service mesh Interface)

Ora che sono state disabilitate tutte le comunicazioni nella rete, è possibile consentire al servizio bookbuyer di comunicare con il servizio Bookstore per l'acquisto di libri e consentire al servizio Bookstore di comunicare con il servizio bookwarehouse per il recupero di libri da vendere.

Distribuire i criteri [SMI](https://smi-spec.io/) seguenti.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

L'output dovrebbe essere simile al seguente.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

È ora possibile configurare una sessione di Port porting nei Pod bookbuyer o Bookstore e verificare che i libri acquistati e i libri venduti siano indietro. È anche possibile eseguire la stessa operazione per il Pod bookthief per verificare che non sia più in grado di rubare libri.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Applicare i criteri di suddivisione del traffico SMI (Service mesh Interface)

Per la dimostrazione finale, si creerà un criterio di suddivisione del traffico [SMI](https://smi-spec.io/) per configurare il peso delle comunicazioni da un servizio a più servizi come back-end. La funzionalità di suddivisione del traffico consente di spostare progressivamente le connessioni a un servizio rispetto a un altro, ponderando il traffico su una scala da 0 a 100.

Il grafico seguente è un diagramma del criterio di suddivisione del traffico [SMI](https://smi-spec.io/) da distribuire. Si distribuirà una libreria aggiuntiva versione 2 e si suddividerà il traffico in ingresso da bookbuyer, ponderando il 25% del traffico al servizio Bookstore V1 e 75% al servizio Bookstore V2.

![Diagramma di suddivisione del traffico OSM bookbuyer](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Distribuire il servizio Bookstore V2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Viene visualizzato l'output seguente.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Distribuire ora i criteri di suddivisione del traffico per suddividere il traffico bookbuyer tra i due servizi Bookstore V1 e V2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Viene visualizzato l'output seguente.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Configurare un tunnel di trasmissione in diretta per il Pod bookbuyer. a questo punto dovrebbero essere visualizzati i libri acquistati dal servizio Bookstore V2. Se si continua a osservare l'incremento degli acquisti, è necessario notare un incremento più rapido degli acquisti effettuati tramite il servizio Bookstore V2.

![Interfaccia utente di OSM bookbuyer Books boough](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Consente di gestire le applicazioni distribuite esistenti per la gestione mediante il componente aggiuntivo Azure Kubernetes Service mesh (OSM) del servizio Azure

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questa procedura dettagliata presuppongono che in precedenza sia stato abilitato il componente aggiuntivo OSM AKS per il cluster AKS. In caso contrario, vedere la sezione [abilitare il componente aggiuntivo Azure Kubernetes Service mesh (OSM) per un cluster AKS esistente prima di](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) procedere. Inoltre, il cluster AKS deve essere Kubernetes e versioni successive, avere abilitato il controllo degli accessi in base al ruolo di `1.19+` Kubernetes e avere stabilito una `kubectl` connessione con il cluster. se è necessaria assistenza per uno di questi elementi, vedere la [Guida introduttiva di AKS](./kubernetes-walkthrough.md)ed è stato installato il componente aggiuntivo AKS OSM.

Devono essere installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.20.0 o successiva
- La `aks-preview` versione dell'estensione 0.5.5 o versioni successive
- OSM versione v 0.8.0 o successiva
- apt-get install JQ

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Verificare i criteri della modalità traffico permissivo Open Service mesh (OSM)

La modalità dei criteri di traffico permissivi OSM è una modalità in cui l'applicazione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte delle regole di criteri del traffico del servizio mesh e programmi in ogni sidecar del proxy inviato per poter comunicare con questi servizi.

Per verificare la modalità di traffico permissivo corrente di OSM per il cluster, eseguire il comando seguente:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output di OSM configMap dovrebbe essere simile al seguente:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Se la **permissive_traffic_policy_mode** è configurata su **true**, è possibile caricare in modo sicuro gli spazi dei nomi senza alcuna distorsione per le comunicazioni da servizio a servizio. Se la **permissive_traffic_policy_mode** è configurata su **false**, sarà necessario assicurarsi che siano stati distribuiti i manifesti dei criteri di accesso al traffico [SMI](https://smi-spec.io/) corretti, oltre a garantire che un account del servizio rappresenti ogni servizio distribuito nello spazio dei nomi. Seguire le istruzioni per l' [onboarding delle applicazioni distribuite esistenti con i criteri di traffico permissivo Open Service mesh (OSM) configurati come false](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Onboarding di applicazioni distribuite esistenti con criteri di traffico permissivi Open Service mesh (OSM) configurati come true

La prima cosa da fare è aggiungere gli spazi dei nomi delle applicazioni distribuite a OSM da gestire.

```azurecli-interactive
osm namespace add bookstore
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Verrà ora esaminata la distribuzione di Pod corrente nello spazio dei nomi. Eseguire il comando seguente per visualizzare i pod nello spazio dei nomi designato.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Verrà visualizzato un output simile al seguente:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Si noti la colonna **Ready** che mostra **1/1**, che indica che il Pod dell'applicazione contiene un solo contenitore. Successivamente, sarà necessario riavviare le distribuzioni dell'applicazione in modo che OSM possa inserire il contenitore proxy sidecar di invio con il Pod dell'applicazione. Viene ora ottenuto un elenco di distribuzioni nello spazio dei nomi.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Dovrebbe venire visualizzato l'output seguente:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

A questo punto, verrà riavviata la distribuzione per inserire il contenitore proxy sidecar inviato con il Pod dell'applicazione. Eseguire il comando seguente.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Dovrebbe venire visualizzato l'output seguente:

```Output
deployment.apps/bookbuyer restarted
```

Se si esaminano di nuovo i pod nello spazio dei nomi:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Si noterà ora che la colonna **Ready** Mostra ora i contenitori **2/2** pronti per il pod. Il secondo contenitore è il proxy sidecar di invio.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

È possibile esaminare ulteriormente il pod per visualizzare il proxy inviato eseguendo il comando Descrivi per visualizzare la configurazione.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Verificare che l'applicazione sia ancora funzionante dopo l'inserimento del proxy sidecar di invio.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboarding di applicazioni distribuite esistenti con criteri di traffico permissivi Open Service mesh (OSM) configurati come false

Quando la configurazione OSM per i criteri di traffico permissivi è impostata su `false` , OSM richiederà criteri di accesso al traffico [SMI](https://smi-spec.io/) espliciti distribuiti per la comunicazione da servizio a servizio all'interno del cluster. Attualmente, OSM usa anche gli account del servizio Kubernetes come parte dell'autorizzazione delle comunicazioni da servizio a servizio. Per assicurarsi che le applicazioni distribuite esistenti comunicheranno quando vengono gestite dalla rete OSM, è necessario verificare l'esistenza di un account del servizio da usare, aggiornare la distribuzione dell'applicazione con le informazioni sull'account del servizio, applicare i criteri di accesso al traffico [SMI](https://smi-spec.io/) .

#### <a name="verify-kubernetes-service-accounts"></a>Verificare gli account del servizio Kubernetes

Verificare se si dispone di un account del servizio kubernetes nello spazio dei nomi in cui è distribuita l'applicazione.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Nell'esempio seguente è presente un account del servizio denominato `bookbuyer` nello spazio dei nomi bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Se non si dispone di un account del servizio elencato oltre all'account predefinito, sarà necessario crearne uno per l'applicazione. Usare il comando seguente come esempio per creare un account del servizio nello spazio dei nomi distribuito dell'applicazione.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Visualizza la specifica di distribuzione corrente dell'applicazione

Se è necessario creare un account del servizio dalla sezione precedente, è probabile che la distribuzione dell'applicazione non sia configurata con una specifica `serviceAccountName` nella specifica della distribuzione. È possibile visualizzare la specifica della distribuzione dell'applicazione con i comandi seguenti:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Nell'output verrà elencato un elenco delle distribuzioni.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Verrà ora descritta la distribuzione come verifica per verificare se è presente un account del servizio elencato nella sezione del modello Pod.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

In questa particolare distribuzione è possibile vedere che è presente un account del servizio associato alla distribuzione elencata nella sezione del modello Pod. Questa distribuzione utilizza l'account del servizio bookbuyer. Se non viene visualizzato l' **account servizio:** Property, la distribuzione non è configurata per l'utilizzo di un account del servizio.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Sono disponibili diverse tecniche per aggiornare la distribuzione per aggiungere un account del servizio kubernetes. Esaminare la documentazione di Kubernetes sull' [aggiornamento di una distribuzione](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) inline o [configurare gli account del servizio per i pod](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Dopo aver aggiornato la specifica di distribuzione con l'account del servizio, ridistribuire (kubectl Apply-f Your-Deployment. YAML) la distribuzione nel cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Distribuire i criteri SMI (Service mesh Interface) necessari

L'ultimo passaggio per consentire il flusso del traffico autorizzato nella rete consiste nel distribuire i criteri di accesso al traffico [SMI](https://smi-spec.io/) necessari per l'applicazione. La quantità di configurazione che è possibile ottenere con i criteri di accesso al traffico [SMI](https://smi-spec.io/) esula dall'ambito di questa procedura dettagliata, ma verrà illustrato in dettaglio alcuni dei componenti comuni della specifica e verrà illustrato come configurare un semplice criterio TrafficTarget e HTTPRouteGroup per abilitare la comunicazione da servizio a servizio per l'applicazione.

La specifica del [**controllo di accesso al traffico**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) [SMI](https://smi-spec.io/) consente agli utenti di definire i criteri di controllo degli accessi per le applicazioni. Ci si concentrerà sulle risorse dell'API **TrafficTarget** e **HTTPRoutGroup** .

La risorsa TrafficTarget è costituita da tre impostazioni di configurazione principali: destinazione, regole e origini. Di seguito è riportato un esempio di TrafficTarget.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

Nella specifica TrafficTarget precedente, `destination` indica l'account del servizio configurato per il servizio di origine di destinazione. Tenere presente che l'account del servizio che è stato aggiunto alla distribuzione precedente verrà usato per autorizzare l'accesso alla distribuzione a cui è collegato. `rules`Nella sezione, in questo particolare esempio, viene definito il tipo di traffico HTTP consentito sulla connessione. È possibile configurare i modelli Regex a granularità fine affinché le intestazioni HTTP siano specifiche sul traffico consentito tramite HTTP. La `sources` sezione rappresenta le comunicazioni di origine del servizio. Questa specifica legge bookbuyer deve comunicare con la libreria.

La risorsa HTTPRouteGroup è costituita da una o una matrice di corrispondenze delle informazioni di intestazione HTTP ed è un requisito per la specifica TrafficTarget. Nell'esempio seguente si noterà che HTTPRouteGroup sta autorizzando tre azioni HTTP, due GET e un POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Se non si ha familiarità con il tipo di traffico HTTP che l'applicazione front-end apporta ad altri livelli dell'applicazione, poiché la specifica TrafficTarget richiede una regola, è possibile creare l'equivalente di una regola Allow all usando la specifica seguente per HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Una volta configurate le specifiche TrafficTarget e HTTPRouteGroup, è possibile inserirle come un'unica YAML e distribuire. Di seguito è riportata la configurazione di esempio della libreria.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Visitare il sito [SMI](https://smi-spec.io/) per informazioni più dettagliate sulla specifica.

### <a name="manage-the-applications-namespace-with-osm"></a>Gestire lo spazio dei nomi dell'applicazione con OSM

Si configurerà quindi OSM per gestire lo spazio dei nomi e riavviare le distribuzioni per far inserire il proxy sidecar inviato con l'applicazione.

Eseguire il comando seguente per configurare lo `azure-vote` spazio dei nomi in modo che sia gestito da OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Riavviare quindi entrambe le `azure-vote-front` `azure-vote-back` distribuzioni e con i comandi seguenti.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Se si visualizzano i pod per lo `azure-vote` spazio dei nomi, viene visualizzata la fase **pronta** di `azure-vote-front` e `azure-vote-back` come 2/2, ovvero il proxy sidecar inviato è stato inserito insieme all'applicazione.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Esercitazione: distribuire un'applicazione gestita da Open Service mesh (OSM) con l'ingresso NGINX

Open Service mesh (OSM) è una rete di servizi cloud nativa leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità predefinite per ambienti di microservizi altamente dinamici.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Visualizzare la configurazione corrente del cluster OSM
> - Creare gli spazi dei nomi per OSM per gestire le applicazioni distribuite negli spazi dei nomi
> - Eseguire l'onboarding degli spazi dei nomi da gestire con OSM
> - Distribuire l'applicazione di esempio
> - Verificare l'applicazione in esecuzione nel cluster AKS
> - Creare un controller di ingresso NGINX usato per applicazione
> - Esporre un servizio tramite il gateway applicazione Azure in ingresso a Internet

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.19+` e versioni successive, con KUBERNETES RBAC abilitato), che sia stata stabilita una `kubectl` connessione con il cluster. se è necessaria assistenza per uno di questi elementi, vedere la [Guida introduttiva di AKS](./kubernetes-walkthrough.md)ed è stato installato il componente aggiuntivo OSM di AKS.

Devono essere installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.20.0 o successiva
- La `aks-preview` versione dell'estensione 0.5.5 o versioni successive
- OSM versione v 0.8.0 o successiva
- apt-get install JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualizzare e verificare la configurazione corrente del cluster OSM

Quando il componente aggiuntivo OSM per AKS è stato abilitato nel cluster AKS, è possibile visualizzare i parametri di configurazione correnti in OSM-config Kubernetes ConfigMap. Eseguire il comando seguente per visualizzare le proprietà di ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output Mostra la configurazione OSM corrente per il cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Si noti che la **permissive_traffic_policy_mode** è configurata su **true**. La modalità di criteri di traffico permissiva in OSM è una modalità in cui l'applicazione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte delle regole di criteri del traffico del servizio mesh e programmi in ogni sidecar del proxy inviato per poter comunicare con questi servizi.

### <a name="create-namespaces-for-the-application"></a>Creazione di spazi dei nomi per l'applicazione

In questa esercitazione verrà usata l'applicazione libreria OSM con i componenti dell'applicazione seguenti:

- bookbuyer
- bookthief
- libreria
- bookwarehouse

Creare gli spazi dei nomi per ognuno di questi componenti dell'applicazione.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Dovrebbe venire visualizzato l'output seguente:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboarding degli spazi dei nomi da gestire con OSM

L'aggiunta degli spazi dei nomi alla rete OSM consentirà al controller OSM di inserire automaticamente i contenitori proxy sidecar di invio con l'applicazione. Eseguire il comando seguente per caricare gli spazi dei nomi dell'applicazione della libreria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione Bookstore nel cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Tutti gli output della distribuzione sono riepilogati di seguito.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aggiornare il servizio Bookbuyer

Aggiornare il servizio bookbuyer alla configurazione della porta in ingresso corretta con il seguente manifesto del servizio.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione Bookstore in esecuzione all'interno del cluster AKS

A questo punto è stata distribuita l'applicazione multi-container della libreria, ma è possibile accedervi solo dall'interno del cluster AKS. Successivamente, si aggiungerà il controller di ingresso applicazione Azure gateway per esporre l'applicazione all'esterno del cluster AKS. Per verificare che l'applicazione sia in esecuzione all'interno del cluster, verrà usata la porta avanti per visualizzare l'interfaccia utente del componente bookbuyer.

Prima di tutto, ottenere il nome del Pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al Pod bookbuyer sarà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una volta ottenuto il nome del Pod, ora è possibile usare il comando Port-inoltr per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster AKS. Eseguire il comando seguente per configurare la porta in avanti per la porta di sistema locale 8080. Usare di nuovo il nome del Pod bookbuyer specificato.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Verrà visualizzato un output simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Durante la sessione di invio della porta, passare all'URL seguente da un browser `http://localhost:8080` . A questo punto dovrebbe essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![App OSM bookbuyer per l'immagine dell'interfaccia utente NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso NGINX in Azure Kubernetes Service (AKS)

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Si utilizzerà il controller di ingresso per esporre l'applicazione gestita da OSM a Internet. Per creare il controller di ingresso, usare Helm per installare nginx-ingress. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate _ingress-Basic_. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato un indirizzo IP dinamico, come illustrato nell'output dell'esempio seguente:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Non sono state create regole di ingresso, pertanto si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Esporre il servizio bookbuyer a Internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Visualizzare i log NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

L'output Mostra lo stato del controller di ingresso NGINX quando la regola in ingresso è stata applicata correttamente:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Visualizzare i servizi NGINX e il servizio bookbuyer esternamente

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Poiché il nome host nel manifesto in ingresso è un nome pseudoclasse usato per il test, il nome DNS non sarà disponibile su Internet. In alternativa, è possibile usare il programma curl e incollare l'intestazione del nome host all'indirizzo IP pubblico NGINX e ricevere un codice 200 correttamente collegato al servizio bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Dovrebbe venire visualizzato l'output seguente:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Esercitazione: distribuire un'applicazione gestita da Open Service mesh (OSM) con applicazione Azure componente aggiuntivo di ingresso del gateway AKS

Open Service mesh (OSM) è una rete di servizi cloud nativa leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità predefinite per ambienti di microservizi altamente dinamici.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Visualizzare la configurazione corrente del cluster OSM
> - Creare gli spazi dei nomi per OSM per gestire le applicazioni distribuite negli spazi dei nomi
> - Eseguire l'onboarding degli spazi dei nomi da gestire con OSM
> - Distribuire l'applicazione di esempio
> - Verificare l'applicazione in esecuzione nel cluster AKS
> - Creare un gateway applicazione Azure da usare come controller di ingresso per il applicazione
> - Esporre un servizio tramite il gateway applicazione Azure in ingresso a Internet

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.19+` e versioni successive, con KUBERNETES RBAC abilitato), che sia stata stabilita una `kubectl` connessione con il cluster. se è necessaria assistenza per uno di questi elementi, vedere la [Guida introduttiva di AKS](./kubernetes-walkthrough.md), avere installato il componente aggiuntivo OSM di AKS e creare un nuovo gateway applicazione Azure per l'ingresso.

Devono essere installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.20.0 o successiva
- La `aks-preview` versione dell'estensione 0.5.5 o versioni successive
- Cluster AKS versione 1.19 + uso della rete CNI di Azure (collegato a una VNET di Azure)
- OSM versione v 0.8.0 o successiva
- apt-get install JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualizzare e verificare la configurazione corrente del cluster OSM

Quando il componente aggiuntivo OSM per AKS è stato abilitato nel cluster AKS, è possibile visualizzare i parametri di configurazione correnti in OSM-config Kubernetes ConfigMap. Eseguire il comando seguente per visualizzare le proprietà di ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output Mostra la configurazione OSM corrente per il cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Si noti che la **permissive_traffic_policy_mode** è configurata su **true**. La modalità di criteri di traffico permissiva in OSM è una modalità in cui l'applicazione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte delle regole di criteri del traffico del servizio mesh e programmi in ogni sidecar del proxy inviato per poter comunicare con questi servizi.

### <a name="create-namespaces-for-the-application"></a>Creazione di spazi dei nomi per l'applicazione

In questa esercitazione verrà usata l'applicazione libreria OSM con i componenti dell'applicazione seguenti:

- bookbuyer
- bookthief
- libreria
- bookwarehouse

Creare gli spazi dei nomi per ognuno di questi componenti dell'applicazione.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Dovrebbe venire visualizzato l'output seguente:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboarding degli spazi dei nomi da gestire con OSM

Quando si aggiungono gli spazi dei nomi alla rete OSM, questo consentirà al controller OSM di inserire automaticamente i contenitori proxy sidecar di invio con l'applicazione. Eseguire il comando seguente per caricare gli spazi dei nomi dell'applicazione della libreria OSM.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione Bookstore nel cluster AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Tutti gli output della distribuzione sono riepilogati di seguito.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aggiornare il servizio Bookbuyer

Aggiornare il servizio bookbuyer alla configurazione della porta in ingresso corretta con il seguente manifesto del servizio.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione Bookstore in esecuzione all'interno del cluster AKS

A questo punto è stata distribuita l'applicazione libreria multicontenitore, ma è possibile accedervi solo dall'interno del cluster AKS. Successivamente, si aggiungerà il controller di ingresso applicazione Azure gateway per esporre l'applicazione all'esterno del cluster AKS. Per verificare che l'applicazione sia in esecuzione all'interno del cluster, verrà usata la porta avanti per visualizzare l'interfaccia utente del componente bookbuyer.

Prima di tutto, ottenere il nome del Pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al Pod bookbuyer sarà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Una volta ottenuto il nome del Pod, ora è possibile usare il comando Port-inoltr per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster AKS. Eseguire il comando seguente per configurare la porta in avanti per la porta di sistema locale 8080. Usare di nuovo il nome del Pod bookbuyer specifico.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Verrà visualizzato un output simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Durante la sessione di invio della porta, passare all'URL seguente da un browser `http://localhost:8080` . A questo punto dovrebbe essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![App OSM bookbuyer per l'immagine dell'interfaccia utente del gateway app](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Creare un gateway applicazione Azure per esporre l'applicazione bookbuyer all'esterno del cluster AKS

> [!NOTE]
> Nelle direzioni seguenti verrà creata una nuova istanza del gateway applicazione Azure da usare per il traffico in ingresso. Se si dispone di un gateway applicazione Azure esistente che si vuole usare, passare alla sezione per abilitare il componente aggiuntivo del controller di ingresso del gateway applicazione.

#### <a name="deploy-a-new-application-gateway"></a>Distribuire un nuovo gateway applicazione

> [!NOTE]
> Si fa riferimento alla documentazione esistente per abilitare il componente aggiuntivo del controller di ingresso del gateway applicazione per un cluster AKS esistente. Sono state apportate alcune modifiche per soddisfare i materiali OSM. La documentazione più dettagliata sul soggetto è disponibile [qui](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

Verrà ora distribuito un nuovo gateway applicazione per simulare la presenza di un gateway applicazione esistente da usare per bilanciare il carico del traffico indirizzato al cluster del servizio Azure Kubernetes, ovvero _myCluster_. Il nome del gateway applicazione sarà _myApplicationGateway_, ma è prima necessario creare una risorsa IP pubblico denominata _myPublicIp_ e una nuova rete virtuale denominata _myVnet_ con spazio indirizzi 11.0.0.0/8, nonché una subnet con spazio indirizzi 11.1.0.0/16 denominata _mySubnet_ e quindi distribuire il gateway applicazione in _mySubnet_ usando _myPublicIp_.

Quando si usa un cluster del servizio Azure Kubernetes e un gateway applicazione in reti virtuali separate, gli spazi indirizzi delle due reti virtuali non devono sovrapporsi. Lo spazio indirizzi predefinito distribuito da un cluster del servizio Azure Kubernetes è 10.0.0.0/8, di conseguenza il prefisso dell'indirizzo della rete virtuale del gateway applicazione è impostato su 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Il componente aggiuntivo Controller in ingresso del gateway applicazione supporta **solo** SKU del gateway applicazione V2 (Standard e WAF) e **non** SKU del gateway applicazione V1.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Abilitare il componente aggiuntivo AGIC per un cluster AKS esistente tramite l'interfaccia della riga di comando di Azure

Se si vuole continuare a usare l'interfaccia della riga di comando di Azure, è possibile continuare ad abilitare il componente aggiuntivo AGIC nel cluster AKS creato, in _cluster_ e specificare il componente aggiuntivo AGIC per usare il gateway applicazione esistente creato, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

È possibile verificare che il componente aggiuntivo di applicazione Azure gateway AKS sia stato abilitato con il comando seguente.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Questo comando dovrebbe visualizzare l'output come `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Eseguire il peering combinato delle due reti virtuali

Dal momento che il cluster del servizio Azure Kubernetes è stato distribuito nella propria rete virtuale e il gateway applicazione è stato distribuito in un'altra rete virtuale, sarà necessario eseguire il peering delle due reti virtuali per consentire il flusso del traffico dal gateway applicazione ai pod nel cluster. Per eseguire il peering delle due reti virtuali, è necessario eseguire per due volte il comando dell'interfaccia della riga di comando di Azure per garantire che la connessione sia bidirezionale. Il primo comando creerà una connessione di peering dalla rete virtuale del gateway applicazione alla rete virtuale del servizio Azure Kubernetes; il secondo comando creerà una connessione di peering nell'altra direzione.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Esporre il servizio bookbuyer a Internet

Applicare il manifesto di ingresso seguente al cluster AKS per esporre il servizio bookbuyer a Internet tramite il gateway applicazione Azure.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Verrà visualizzato l'output seguente

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Poiché il nome host nel manifesto in ingresso è uno pseudo nome usato per il test, il nome DNS non sarà disponibile su Internet. In alternativa, è possibile usare il programma curl e incollare l'intestazione del nome host all'indirizzo IP pubblico del gateway applicazione Azure e ricevere un codice 200 correttamente collegato al servizio bookbuyer.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Verrà visualizzato l'output seguente

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Risoluzione dei problemi

- [Documentazione sulla risoluzione dei problemi di AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Nel repository GitHub di AGIC sono disponibili altri strumenti per la risoluzione dei problemi](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Aprire il monitoraggio e l'osservabilità del servizio mesh (OSM) con monitoraggio di Azure e Application Insights

Monitoraggio di Azure e applicazione Azure Insights consentono di ottimizzare la disponibilità e le prestazioni delle applicazioni e dei servizi fornendo una soluzione completa per la raccolta, l'analisi e la gestione dei dati di telemetria dagli ambienti cloud e locali.

Il componente aggiuntivo OSM AKS avrà integrazioni approfondite in entrambi i servizi di Azure e offrirà un'esperienza di Azure apparentemente per la visualizzazione e la risposta agli indicatori KPI critici forniti dalle metriche OSM. Per altre informazioni su come abilitare e configurare questi servizi per il componente aggiuntivo OSM AKS, vedere la pagina [monitoraggio di Azure per OSM](https://aka.ms/azmon/osmpreview) .

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Esercitazione: distribuire manualmente Prometeo, Grafana e Jaeger per visualizzare le metriche di Open Service mesh (OSM) per l'osservabilità

> [!WARNING]
> L'installazione di Prometeo, Grafana e Jaeger viene fornita come guida generale per illustrare il modo in cui questi strumenti possono essere utilizzati per visualizzare i dati delle metriche OSM. Le linee guida per l'installazione non devono essere utilizzate per la configurazione di produzione. Consultare la documentazione di ogni strumento per informazioni sul modo migliore per soddisfare le proprie esigenze. La più importante è la mancanza di archiviazione persistente, ovvero tutti i dati andranno perduti una volta che un Grafana di Prometeo e/o i pod Jaeger vengono terminati.

Open Service mesh (OSM) genera metriche dettagliate correlate a tutto il traffico all'interno della rete. Queste metriche forniscono informazioni approfondite sul comportamento delle applicazioni nella rete per aiutare gli utenti a risolvere, gestire e analizzare le applicazioni.

Attualmente OSM raccoglie le metriche direttamente dai proxy sidecar (inviato). OSM fornisce metriche avanzate per il traffico in ingresso e in uscita per tutti i servizi nella rete. Con queste metriche, l'utente può ottenere informazioni sul volume di traffico complessivo, sugli errori nel traffico e sul tempo di risposta per le richieste.

OSM USA Prometeo per raccogliere e archiviare metriche e statistiche di traffico coerenti per tutte le applicazioni in esecuzione nella rete. Prometeo è un toolkit open source di monitoraggio e avviso, comunemente usato in ambienti Kubernetes e mesh di servizi.

Ogni applicazione che fa parte della mesh viene eseguita in un pod che contiene un sidecar dell'inviato che espone le metriche (metriche proxy) nel formato Prometeo. Inoltre, ogni pod che fa parte della mesh ha annotazioni Prometeo, che consente al server Prometeo di rimuovere l'applicazione in modo dinamico. Questo meccanismo abilita automaticamente il frammento di metriche ogni volta che viene aggiunto un nuovo spazio dei nomi/Pod/servizio alla rete mesh.

Le metriche OSM possono essere visualizzate con Grafana, un software di visualizzazione e analisi open source. Consente di eseguire query, visualizzare, avvisare ed esplorare le metriche.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare e distribuire un'istanza di Prometeo
> - Configurare OSM per consentire la raschiatura Prometeo
> - Aggiornare il configMap di Prometeo
> - Creare e distribuire un'istanza di Grafana
> - Configurare Grafana con l'origine dati Prometheus
> - Importazione del dashboard OSM per Grafana
> - Creare e distribuire un'istanza di Jaeger
> - Configurare la traccia Jaeger per OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Distribuire e configurare un'istanza di Prometeo per OSM

Per distribuire l'istanza di Prometeo viene usato Helm. Eseguire i comandi seguenti per installare Prometeo tramite Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Se l'installazione ha avuto esito positivo, verrà visualizzato un output simile. Prendere nota della porta del server Prometeo e del nome DNS del cluster. Queste informazioni verranno usate in un secondo momento per configurare Prometeo come origine dati per Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Configurare OSM per consentire la raschiatura Prometeo

Per assicurarsi che i componenti OSM siano configurati per i frammenti di Prometeo, è opportuno controllare la configurazione **prometheus_scraping** disponibile nel file di configurazione OSM-config. Visualizzare la configurazione con il comando seguente:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

L'output del comando precedente deve restituire `true` se OSM è configurato per la raschiatura Prometeo. Se il valore restituito è `false` , sarà necessario aggiornare la configurazione in modo che sia `true` . Eseguire il comando seguente **per attivare il frammento di OSM** Prometeo:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Viene visualizzato l'output seguente.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Aggiornare il configMap di Prometeo

L'installazione predefinita di Prometeo conterrà due configmaps Kubernetes. È possibile visualizzare l'elenco di configmaps di Prometeo con il comando seguente.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

È necessario sostituire la configurazione di Prometeo. yml situata nel configMap **stable-Prometheus-server** con la configurazione OSM seguente. Per eseguire questa attività sono disponibili diverse tecniche di modifica di file. Un modo semplice e sicuro consiste nell'esportare il configMap, crearne una copia per il backup e quindi modificarlo con un editor, ad esempio Visual Studio Code.

> [!NOTE]
> Se non si dispone di Visual Studio Code installato, è possibile scaricarlo e installarlo [qui](https://code.visualstudio.com/Download).

Si esporterà prima di tutto il configMap **stable-Prometheus-server** e quindi si eseguirà una copia per il backup.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Successivamente, aprire il file utilizzando Visual Studio Code per modificare.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Una volta aperto il configMap nell'editor Visual Studio Code, sostituire il file Prometeo. yml con la configurazione OSM riportata di seguito e salvare il file.

> [!WARNING]
> È estremamente importante assicurarsi di mantenere la struttura dei rientri del file YAML. Eventuali modifiche apportate alla struttura dei file YAML potrebbero comportare la mancata riapplicazione del configMap.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Applicare il file YAML configMap aggiornato con il comando seguente.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> È possibile che venga visualizzato un messaggio relativo a un'annotazione kubernetes mancante necessaria. Questa operazione può essere ignorata per il momento.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Verificare che Prometeo sia configurato per rimuovere gli endpoint dell'API e della rete OSM

Per verificare che Prometeo sia configurato correttamente per rimuovere gli endpoint dell'API e della rete OSM, si passerà al Pod Prometheus e si visualizza la configurazione di destinazione. Eseguire i comandi seguenti.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Aprire un browser fino a `http://localhost:9090/targets`

Scorrendo verso il basso si noterà che lo stato di endpoint della metrica SMI **è in corso e** altre metriche OSM definite come illustrato di seguito.

![Immagine dell'interfaccia utente metrica di destinazione del Prometeo OSM](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Distribuire e configurare un'istanza di Grafana per OSM

Si userà Helm per distribuire l'istanza di Grafana. Eseguire i comandi seguenti per installare Grafana tramite Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Si recupererà quindi la password Grafana predefinita per accedere al sito Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Prendere nota della password di Grafana.

Si recupererà quindi il Pod Grafana per la porta in avanti nel dashboard di Grafana per l'accesso.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Aprire un browser fino a `http://localhost:3000`

Nella schermata di accesso illustrata di seguito immettere **admin** come nome utente e usare la password Grafana acquisita in precedenza.

![Immagine dell'interfaccia utente della pagina di accesso Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Configurare l'origine dati Grafana Prometheus

Dopo aver eseguito l'accesso a Grafana, il passaggio successivo consiste nell'aggiungere Prometheus come origini dati per Grafana. A tale scopo, passare all'icona di configurazione nel menu a sinistra e selezionare origini dati, come illustrato di seguito.

![Immagine dell'interfaccia utente della pagina delle origini dati Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Fare clic sul pulsante **Aggiungi origine dati** e selezionare Prometeo nei database di serie temporali.

![Immagine dell'interfaccia utente della pagina di selezione delle origini dati OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Nella pagina **configurare l'origine dati Prometeo seguente** immettere il nome di dominio completo del cluster Kubernetes per il servizio Prometheus per l'impostazione URL http. Il nome di dominio completo predefinito deve essere `stable-prometheus-server.default.svc.cluster.local` . Dopo aver immesso l'endpoint del servizio Prometheus, scorrere fino alla fine della pagina e selezionare **salva & test**. Verrà visualizzata una casella di controllo verde che indica che l'origine dati è funzionante.

#### <a name="importing-osm-dashboards"></a>Importazione di dashboard OSM

I dashboard OSM sono disponibili tramite:

- Il [repository](/charts/osm/grafana)e possono essere importati come BLOB JSON tramite il portale di amministrazione Web
- o [online all'Grafana.com](https://grafana.com/grafana/dashboards/14145)

Per importare un dashboard, cercare il `+` segno nel menu a sinistra e selezionare `import` .
È possibile importare direttamente il dashboard in base al relativo ID `Grafana.com` . Ad esempio, il `OSM Mesh Details` Dashboard USA `14145` l'ID, è possibile usare l'ID direttamente nel modulo e selezionare `import` :

![Immagine dell'interfaccia utente della pagina di importazione del dashboard Grafana OSM](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Non appena si seleziona importa, questo porta automaticamente il dashboard importato.

![Immagine dell'interfaccia utente della pagina Dettagli mesh del dashboard OSM Grafana](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Distribuire e configurare un operatore Jaeger in Kubernetes per OSM

[Jaeger](https://www.jaegertracing.io/) è un sistema di traccia open source usato per il monitoraggio e la risoluzione dei problemi dei sistemi distribuiti. Può essere distribuito con OSM come nuova istanza oppure è possibile usare un'istanza personalizzata. Le istruzioni seguenti consentono di distribuire una nuova istanza di Jaeger nello `jaeger` spazio dei nomi nel cluster AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Distribuire Jaeger nel cluster AKS

Applicare il manifesto seguente per installare Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Abilitare la traccia per il componente aggiuntivo OSM

Successivamente, è necessario abilitare la traccia per il componente aggiuntivo OSM.

> [!NOTE]
> A questo punto le proprietà di traccia non sono visibile in configMap OSM-config. Questa operazione verrà resa visibile in una nuova versione del componente aggiuntivo OSM AKS.

Eseguire il comando seguente per abilitare la traccia per il componente aggiuntivo OSM:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Visualizzare l'interfaccia utente di Jaeger con Port porting

L'interfaccia utente di Jaeger è in esecuzione sulla porta 16686. Per visualizzare l'interfaccia utente Web, è possibile usare kubectl Port-inoltr:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Nel browser dovrebbe essere visualizzato un elenco a discesa servizio, che consente di effettuare una selezione tra le varie applicazioni distribuite dalla demo della libreria. Selezionare un servizio per visualizzare tutti gli intervalli. Se ad esempio si seleziona bookbuyer con un lookback di un'ora, è possibile visualizzarne le interazioni con Bookstore-V1 e Bookstore-V2 ordinate in base all'ora.

![Immagine dell'interfaccia utente della pagina di traccia OSM Jaeger](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Selezionare un elemento per visualizzarlo in modo più dettagliato. Selezionare più elementi per confrontare le tracce. Ad esempio, è possibile confrontare le interazioni di bookbuyer con Bookstore e Bookstore-V2 in un momento specifico.

È anche possibile selezionare la scheda architettura di sistema per visualizzare un grafico del modo in cui le varie applicazioni sono state interagiscono/comunicanti. Questo fornisce un'idea del flusso del traffico tra le applicazioni.

![Immagine dell'interfaccia utente dell'architettura di sistema OSM Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Guide introduttive per la risoluzione dei problemi del componente aggiuntivo per il servizio mesh (OSM) AKS

Quando si distribuisce il componente aggiuntivo OSM AKS, è possibile che si verifichi occasionalmente un problema. Le guide seguenti forniscono assistenza per la risoluzione di errori e la risoluzione di problemi comuni.

### <a name="verifying-and-troubleshooting-osm-components"></a>Verifica e risoluzione dei problemi relativi ai componenti OSM

#### <a name="check-osm-controller-deployment"></a>Controllare la distribuzione del controller OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Un controller OSM integro sarà simile al seguente:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Controllare il pod del controller OSM

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Un pod OSM integro sarà simile al seguente:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Anche se in un certo momento è stato rimosso un controller, ne è disponibile un altro che è pronto 1/1 e in esecuzione con 0 riavvii. Se la colonna è pronta per un valore diverso da 1/1, la mesh del servizio si trova in uno stato di interruzione.
La colonna READY con 0/1 indica che il contenitore del piano di controllo si arresta in modo anomalo. è necessario ottenere i log. Vedere la sezione ottenere i log del controller OSM dalla sezione del supporto tecnico di Azure. La colonna pronta con un numero maggiore di 1 dopo/indicherà la presenza di sidecar installati. Il controller OSM probabilmente non funzionerà con i sidecar collegati.

> [!NOTE]
> A partire dalla versione v 0.8.2, il controller OSM non è in modalità a disponibilità elevata e verrà eseguito in una distribuzione con numero di repliche pari a 1-singolo POD. Il Pod dispone di probe di integrità e verrà riavviato da kubelet, se necessario.

#### <a name="check-osm-controller-service"></a>Controllare il servizio del controller OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Un servizio di controller OSM integro sarà simile al seguente:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> L'IP del CLUSTER sarebbe diverso. Il nome e le porte del servizio devono corrispondere a quelli dell'esempio precedente.

#### <a name="check-osm-controller-endpoints"></a>Controllare gli endpoint del controller OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Un endpoint del controller OSM integro sarà simile al seguente:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Controllare la distribuzione degli iniettori OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

La distribuzione di un iniettore OSM integro sarà simile alla seguente:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Verificare il Pod iniettore OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un pod iniettore OSM integro sarà simile al seguente:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Controllare il servizio di iniettore OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Un servizio di iniettore OSM integro sarà simile al seguente:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Controllare gli endpoint OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Un endpoint di OSM integro sarà simile al seguente:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Controllare i webhook di convalida e mutazione

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Un webhook di convalida OSM integro sarà simile al seguente:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Un webhook di mutazione di OSM integro sarà simile al seguente:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Verificare la presenza del servizio e del bundle CA del webhook di convalida

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configurazione di Webhook di convalida ben configurata avrà un aspetto analogo al seguente:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Verificare la presenza del servizio e del bundle CA del webhook di mutazione

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configurazione di Webhook di modifica ben configurata avrà un aspetto analogo al seguente:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Controllare se il controller OSM ha dato il webhook di convalida (o mutazione) a un bundle di CA

> [!NOTE]
> A partire da v 0.8.2 è importante tenere presente che AKS RP installa il webhook di convalida. il riconciliatore AKS garantisce che esista, ma il controller OSM è quello che riempie il bundle della CA.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Questo numero indica il numero di byte o la dimensione del bundle della CA. Se è vuoto, 0 o un numero inferiore a 1000 indicherebbe che il provisioning del bundle CA non è stato eseguito correttamente. Senza un bundle di autorità di certificazione corretto, il webhook di convalida potrebbe avere errori e impedire all'utente di apportare modifiche a OSM-config ConfigMap nello spazio dei nomi Kube-System.

Errore di esempio quando il bundle della CA non è corretto:

- Tentativo di modificare il ConfigMap OSM-config:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Errore:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Aggirare il problema quando la configurazione del webhook di **convalida** ha un certificato non valido:

- Opzione 1: riavviare il controller OSM. il controller OSM verrà riavviato. Al primo avvio, sovrascriverà il bundle CA dei webhook di mutazione e di convalida.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Opzione 2-opzione 2. Eliminare il webhook di convalida: la rimozione del webhook di convalida rende `osm-config` non più convalidate le mutazioni del ConfigMap. Qualsiasi patch passerà. Il riconciliatore AKS assicurerà a un certo punto che il webhook di convalida esista e lo ricreerà. Potrebbe essere necessario riavviare il controller OSM per riscrivere rapidamente il bundle della CA.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Opzione 3-eliminazione e patch: il comando seguente eliminerà il webhook di convalida, consentendo di aggiungere qualsiasi valore e tenterà immediatamente di applicare una patch. È molto probabile che il riconciliatore AKS non disponga di tempo sufficiente per la riconciliazione e il ripristino del webhook di convalida che offre la possibilità di applicare una modifica come ultima risorsa:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Controllare `osm-config` **ConfigMap**

> [!NOTE]
> Il controller OSM non richiede `osm-config` che ConfigMap sia presente nello spazio dei nomi Kube-System. Il controller ha valori predefiniti ragionevoli per la configurazione e può funzionare senza di esso.

Verifica l'esistenza:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Verificare il contenuto di OSM-config ConfigMap

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` Valori di ConfigMap:

| Chiave                              | Tipo   | Valori consentiti                                          | Valore predefinito                          | Funzione                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| uscita                           | bool   | true, false                                             | `"false"`                              | Abilita l'uscita nella rete.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | true, false                                             | `"true"`                               | Consente a un endpoint di debug nel pod del controller OSM di elencare le informazioni relative alla rete, ad esempio connessioni proxy, certificati e criteri SMI.                                                                                    |
| enable_privileged_init_container | bool   | true, false                                             | `"false"`                              | Abilita i contenitori init con privilegi per i pod in mesh. Se è false, i contenitori init hanno solo NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | string | Trace, debug, info, Warning, Warn, Error, Critical, off | `"error"`                              | Imposta il livello di dettaglio della registrazione del sidecar del proxy inviato, applicabile solo ai pod appena creati aggiunti alla rete. Per aggiornare il livello di registrazione per i pod esistenti, riavviare la distribuzione con `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | string | elenco delimitato da virgole di intervalli IP nel formato a. b. c. d/x | `-`                                    | Elenco globale degli intervalli di indirizzi IP da escludere dall'intercettazione del traffico in uscita da parte del proxy sidecar.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | true, false                                             | `"false"`                              | Se si imposta su `true` , viene abilitata la modalità Allow-all nella rete, ovvero non viene applicata alcuna applicazione di criteri di traffico nella rete mesh. Se è impostato su `false` , Abilita i criteri di traffico Deny-All in mesh, ad esempio un `SMI Traffic Target` è necessario per la comunicazione dei servizi. |
| prometheus_scraping              | bool   | true, false                                             | `"true"`                               | Abilita la metrica Prometeo sui proxy sidecar.                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24h, 1h30m (qualsiasi durata)                          | `"24h"`                                | Imposta la durata di validità del certificato di servizio, rappresentata come una sequenza di numeri decimali ognuno con la frazione facoltativa e un suffisso di unità.                                                                                             |
| tracing_enable                   | bool   | true, false                                             | `"false"`                              | Abilita la traccia Jaeger per la mesh.                                                                                                                                                                                                    |
| tracing_address                  | string | Jaeger. mesh-Namespace. svc. cluster. local                 | `jaeger.kube-system.svc.cluster.local` | Indirizzo della distribuzione Jaeger, se la traccia è abilitata.                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/spans                                           | /api/v2/spans                          | Endpoint per i dati di traccia, se la traccia è abilitata.                                                                                                                                                                                          |
| tracing_port                     | INT    | qualsiasi valore integer diverso da zero                              | `"9411"`                               | Porta sulla quale è abilitata la traccia.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | true, false                                             | `"false"`                              | Abilita l'ingresso HTTPS nella rete.                                                                                                                                                                                                      |
| config_resync_interval           | string | in 1 minuto Disabilita                            | 0 (disabilitata)                           | Quando viene specificato un valore superiore a 1 milione (60s), il controller OSM invierà tutta la configurazione disponibile a ogni inviato connesso all'intervallo specificato                                                                                                    |

#### <a name="check-namespaces"></a>Controllare gli spazi dei nomi

> [!NOTE]
> Lo spazio dei nomi Kube-System non parteciperà mai a una mesh del servizio e non verrà mai etichettato e/o annotato con la chiave o i valori riportati di seguito.

Viene usato il `osm namespace add` comando per aggiungere spazi dei nomi a una mesh del servizio specificata.
Quando uno spazio dei nomi K8S fa parte della mesh (o perché faccia parte della mesh), devono essere soddisfatte le condizioni seguenti:

Visualizzare le annotazioni con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

È necessario che sia presente l'annotazione seguente:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Visualizzare le etichette con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

È necessario che sia presente l'etichetta seguente:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Se uno spazio dei nomi non è annotato con `"openservicemesh.io/sidecar-injection": "enabled"` o non è etichettato con `"openservicemesh.io/monitored-by": "osm"` l'INIETTOre OSM non aggiungerà sidecar di invio.

> Nota: dopo che `osm namespace add` è stato chiamato, solo i **nuovi** Pod verranno inseriti con un sidecar dell'inviato. I pod esistenti devono essere riavviati con `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Verificare il CRD SMI:

Controllare se il cluster ha il CRD necessario:

```azurecli-interactive
kubectl get crds
```

Nel cluster è necessario che siano installati i seguenti elementi:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Ottenere le versioni di CRD installate con questo comando:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Output previsto:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Il controller OSM v 0.8.2 richiede le versioni seguenti:

- traffictargets.access.smi-spec.io- [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io-non supportato
- trafficsplits.split.smi-spec.io- [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io- [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Se CRD non è presente, usare i comandi seguenti per installarli nel cluster:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Disabilitare il componente aggiuntivo Open Service mesh (OSM) per il cluster AKS

Per disabilitare il componente aggiuntivo OSM, eseguire il comando seguente:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register