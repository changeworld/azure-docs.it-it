---
title: Aprire Service Mesh (anteprima)
description: Open Service Mesh (OSM) in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: bbc07a7ee3f996c778cfc1b9d1764f10a613c50b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782946"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Aprire il componente aggiuntivo Servizio AKS di Service Mesh (anteprima)

## <a name="overview"></a>Panoramica

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) è una mesh di servizi nativa cloud leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità avanzate per ambienti di microservizi altamente dinamici.

OSM esegue un piano di controllo basato su Envoy in Kubernetes, può essere configurato con le API [SMI](https://smi-spec.io/) e funziona inseriscendo un proxy Envoy come contenitore sidecar accanto a ogni istanza dell'applicazione. Il proxy Envoy contiene ed esegue regole relative ai criteri di controllo di accesso, implementa la configurazione del routing e acquisisce le metriche. Il piano di controllo configura continuamente i proxy per garantire che i criteri e le regole di routing siano aggiornati e garantisca che i proxy siano integri.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Funzionalità e funzionalità

OSM offre il set di funzionalità e le funzionalità seguenti per fornire una rete di servizi nativa del cloud per i cluster servizio Azure Kubernetes (AKS):

- Proteggere la comunicazione da servizio a servizio abilitando mTLS

- Eseguire facilmente l'onboarded delle applicazioni nella mesh abilitando l'inserimento automatico di sidecar del proxy Envoy

- Configurazioni facili e trasparenti per lo spostamento del traffico nelle distribuzioni

- Possibilità di definire ed eseguire criteri di controllo di accesso granulari per i servizi

- Osservabilità e informazioni dettagliate sulle metriche delle applicazioni per il debug e il monitoraggio dei servizi

- Integrazione con soluzioni/servizi di gestione certificati esterni con un'interfaccia collegabile

## <a name="scenarios"></a>Scenari

OSM può aiutare le distribuzioni del servizio AKS con gli scenari seguenti:

- Fornire comunicazioni crittografate tra gli endpoint di servizio distribuiti nel cluster

- Autorizzazione del traffico del traffico HTTP/HTTPS e TCP nella rete mesh

- Configurazione dei controlli del traffico ponderati tra due o più servizi per distribuzioni A/B o canary

- Raccolta e visualizzazione degli indicatori KPI dal traffico dell'applicazione

## <a name="osm-service-quotas-and-limits-preview"></a>Quote e limiti del servizio OSM (anteprima)

Le limitazioni dell'anteprima del servizio OSM per le quote e i limiti del servizio sono disponibili nella pagina Quote e limiti a [livello di area](https://docs.microsoft.com/azure/aks/quotas-skus-regions)del servizio AKS.

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
> Non tentare di installare OSM dal file binario usando `osm install` . Verrà così completata un'installazione di OSM che non è integrato come componente aggiuntivo per il servizio Web Diaks.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrare la `AKS-OpenServiceMesh` funzionalità di anteprima

Per creare un cluster del servizio AzureKs in grado di usare il componente aggiuntivo Open Service Mesh, è necessario abilitare il `AKS-OpenServiceMesh` flag di funzionalità nella sottoscrizione.

Registrare `AKS-OpenServiceMesh` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Sono necessari alcuni minuti per visualizzare lo stato _Registered_. Verificare lo stato della registrazione usando [il comando az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse _Microsoft.ContainerService_ usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installare il componente aggiuntivo OSM (Open Service Mesh) servizio Azure Kubernetes (AKS) per un nuovo cluster del servizio Microsoft Service Mesh

Per un nuovo scenario di distribuzione del cluster del servizio Web Diacks, si inizierà con una nuova distribuzione di un cluster del servizio Web Diaks che abilita il componente aggiuntivo OSM durante l'operazione di creazione del cluster.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. Creare un gruppo di risorse usando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse _denominato myOsmAksGroup_ nella località _eastus2_ (area):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Distribuire un cluster del servizio Web Disassoce con il componente aggiuntivo OSM abilitato

A questo punto si distribuirà un nuovo cluster del servizio AKS con il componente aggiuntivo OSM abilitato.

> [!NOTE]
> Tenere presente che il comando seguente per la distribuzione del servizio AKS usa dischi phemeral del sistema operativo. Altre informazioni sono disponibili qui sui dischi del sistema operativo [phemeral per](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) il supporto del supporto di AKS

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Ottenere le credenziali di accesso al cluster del servizio Web Disassociato di Microsoft

Ottenere le credenziali di accesso per il nuovo cluster Kubernetes gestito.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Abilitare il componente aggiuntivo OSM (Open Service Mesh) servizio Azure Kubernetes (AKS) per un cluster del servizio Web Disassoce esistente

Per uno scenario di cluster del servizio Web Diaks esistente, si abiliterà il componente aggiuntivo OSM a un cluster del servizio Web Disassoce esistente già distribuito.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Abilitare il componente aggiuntivo OSM al cluster del servizio Web Disasering esistente

Per abilitare il componente aggiuntivo OSM del servizio Web Del servizio Web Del servizio Web Di windows, è necessario eseguire il `az aks enable-addons --addons` comando passando il parametro `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Verrà visualizzato un output simile all'output illustrato di seguito per confermare l'installazione del componente aggiuntivo OSM del servizio App Sdk.

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

## <a name="validate-the-aks-osm-add-on-installation"></a>Convalidare l'installazione del componente aggiuntivo OSM del servizio Web Del servizio Di windows

Sono disponibili diversi comandi da eseguire per verificare che tutti i componenti del componente aggiuntivo OSM del servizio Web Del servizio Windows Siano abilitati e in esecuzione:

In primo luogo è possibile eseguire una query sui profili dei componenti aggiuntivi del cluster per controllare lo stato abilitato dei componenti aggiuntivi installati. Il comando seguente deve restituire "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

I comandi `kubectl` seguenti segnalano lo stato di osm-controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Accesso al componente aggiuntivo OSM del servizio Web Del servizio Web Del servizio Web del servizio Web di Microsoft Access

Attualmente è possibile accedere e configurare la configurazione del controller OSM tramite la mappa di configurazione. Per visualizzare le impostazioni di configurazione del controller OSM, eseguire una query su osm-config configmap tramite `kubectl` per visualizzarne le impostazioni di configurazione.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output della mappa di configurazione OSM dovrebbe essere simile al seguente:

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

Si noti **che permissive_traffic_policy_mode** è configurato su **true.** La modalità criteri di traffico permissiva in OSM è una modalità in cui l'imposizione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte della rete mesh dei servizi e programma le regole dei criteri di traffico in ogni sidecar proxy Envoy per poter comunicare con questi servizi.

> [!WARNING]
> Prima di procedere, verificare che la modalità dei criteri del traffico permissiva sia impostata su true. In caso contrario, impostarla su **true** usando il comando seguente

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Distribuire una nuova applicazione che deve essere gestita dal componente aggiuntivo Open Service Mesh (OSM) servizio Azure Kubernetes (AKS)

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questa procedura dettagliata presuppongono che sia stato creato un cluster del servizio Kubernetes (Kubernetes e versione successiva, con il controllo degli accessi in base al ruolo di Kubernetes abilitato), sia stata stabilita una connessione con il cluster .Se è necessaria assistenza per uno di questi elementi, vedere la guida introduttiva del servizio Kubernetes e aver installato il componente aggiuntivo OSM del servizio App `1.19+` Kubernetes. `kubectl` [](./kubernetes-walkthrough.md)

Devono essere installate le risorse seguenti:

- Interfaccia della riga di comando di Azure, versione 2.20.0 o successiva
- `aks-preview`L'estensione versione 0.5.5 o successiva
- OSM versione 0.8.0 o successiva
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Creare spazi dei nomi per l'applicazione

In questa procedura dettagliata verrà utilizzata l'applicazione della libreria OSM con i servizi Kubernetes seguenti:

- bookbuyer
- bookthief
- Libreria
- bookwarehouse

Creare spazi dei nomi per ognuno di questi componenti dell'applicazione.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboard degli spazi dei nomi che devono essere gestiti da OSM

Quando si aggiungono gli spazi dei nomi alla mesh OSM, questo consentirà al controller OSM di inserire automaticamente i contenitori del proxy sidecar Envoy con l'applicazione. Eseguire il comando seguente per eseguire l'onboard degli spazi dei nomi dell'applicazione della libreria OSM.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione bookstore nel cluster del servizio Web Disassoce

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

L'applicazione bookstore di esempio è un'app multilivello costituita da quattro servizi, tra cui bookbuyer, bookthief, bookstore e bookwarehouse. Sia il servizio bookbuyer che il servizio bookthief comunicano con il servizio della libreria per recuperare i libri dal servizio della libreria. Il servizio librerie recupera i libri dal servizio della libreria per fornire il bookbuyer e il bookthief. Si tratta di una semplice applicazione multilivello che funziona bene per illustrare come usare una rete mesh di servizi per proteggere e autorizzare le comunicazioni tra i servizi delle applicazioni. Mentre si continua con la procedura dettagliata, verranno abilitati e disabilitati i criteri di Service Mesh Interface (SMI) per consentire e non consentire ai servizi di comunicare tramite OSM. Di seguito è riportato un diagramma dell'architettura di ciò che è stato installato per l'applicazione libreria.

![Architettura dell'app bookbuyer OSM](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione Bookstore in esecuzione all'interno del cluster del servizio Web del servizio Web di Microsoft Office

Al momento è stata distribuita l'applicazione libreria mulit-container, ma è accessibile solo dall'interno del cluster del servizio AppKs. Le esercitazioni successive saranno utili per esporre l'applicazione all'esterno del cluster tramite un controller di ingresso. Per il momento verrà utilizzato il port forwarding per accedere all'applicazione bookbuyer all'interno del cluster del servizio AKS per verificare che acquisti libri dal servizio librerie.

Per verificare che l'applicazione sia in esecuzione all'interno del cluster, verrà utilizzata una porta forward per visualizzare l'interfaccia utente dei componenti bookbuyer e bookthief.

Prima di tutto, ottenere il nome del pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al pod bookbuyer verrà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Dopo aver creato il nome del pod, è ora possibile usare il comando port-forward per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster del servizio AKS. Eseguire il comando seguente per configurare il port forward per la porta del sistema locale 8080. Usare di nuovo il nome del pod bookbuyer specificato.

> [!NOTE]
> Per tutti port forwarding comandi è meglio usare un terminale aggiuntivo in modo da poter continuare a usare questa procedura dettagliata e non disconnettere il tunnel. È anche meglio stabilire il tunnel di port forward all'esterno del Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

L'output dovrebbe essere simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mentre la port forwarding è attiva, passare all'URL seguente da un `http://localhost:8080` browser. Dovrebbe ora essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![Immagine dell'interfaccia utente dell'app bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Si noterà anche che il numero totale di libri acquistati continua ad aumentare al servizio libreria v1. Il servizio libreria v2 non è ancora stato distribuito. Il servizio bookstore v2 verrà distribuito quando verranno illustrati i criteri di suddivisione del traffico SMI.

È anche possibile controllare lo stesso valore per il servizio bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

L'output dovrebbe essere simile al seguente. Al pod bookthief verrà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Eseguire il port forward al pod bookthief.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Passare all'URL seguente da un `http://localhost:8080` browser. Si dovrebbe vedere che il bookthief sta rubando libri dal servizio della libreria. Più avanti verrà implementato un criterio di traffico per arrestare il bookthief.

![Immagine dell'interfaccia utente dell'app OSM bookthief](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Disabilitare la modalità traffico permissivo OSM per la mesh

Come accennato in precedenza quando si visualizza la configurazione del cluster OSM, per impostazione predefinita la configurazione OSM abilita i criteri permissivi in modalità traffico. In questa modalità l'imposizione dei criteri di traffico viene ignorata e OSM individua automaticamente i servizi che fanno parte della rete mesh di servizi e programma le regole dei criteri di traffico in ogni sidecar proxy envoy per poter comunicare con questi servizi.

A questo punto verranno disabilitati i criteri permissivi in modalità traffico e OSM dovrà distribuire criteri [SMI](https://smi-spec.io/) espliciti nel cluster per consentire le comunicazioni nella rete da ogni servizio. Per disabilitare la modalità traffico permissiva, eseguire il comando seguente per aggiornare la proprietà configmap modificando il valore `true` da a `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

L'output dovrebbe essere simile al seguente. Al pod bookthief verrà aggiunto un nome univoco.

```Output
configmap/osm-config patched
```

Per verificare che la modalità di traffico permissiva sia stata disabilitata, tornare al pod bookbuyer o bookthief per visualizzare l'interfaccia utente nel browser e verificare se i libri acquistati o rubati non vengono più incrementati. Assicurarsi di aggiornare il browser. Se l'incremento è stato arrestato, i criteri sono stati applicati correttamente. È stato impedito al bookthief di sottrarre libri, ma né il bookbuyer può acquistare dalla libreria né recuperare i libri dalla libreria. Successivamente verranno implementati [i criteri SMI](https://smi-spec.io/) per consentire solo i servizi nella mesh per cui si desidera comunicare.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Applicare i criteri di accesso al traffico di Service Mesh Interface (SMI)

Ora che tutte le comunicazioni nella rete sono state disabilitate, è possibile consentire al servizio bookbuyer di comunicare al servizio librerie per l'acquisto di libri e consentire al servizio librerie di comunicare con il servizio bookwarehouse per recuperare i libri da vendere.

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

È ora possibile configurare una sessione port forwarding nei pod bookbuyer o bookstore e verificare che sia i libri acquistati che i libri venduti siano in aumento. È anche possibile eseguire la stessa operazione per il pod bookthief per verificare che non sia ancora in grado di rubare libri.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Applicare i criteri di suddivisione del traffico SMI (Service Mesh Interface)

Per la dimostrazione finale verranno creati criteri di suddivisione del traffico [SMI](https://smi-spec.io/) per configurare il peso delle comunicazioni da un servizio a più servizi come back-end. La funzionalità di suddivisione del traffico consente di spostare progressivamente le connessioni a un servizio rispetto a un altro ponderando il traffico su una scala da 0 a 100.

Il grafico seguente è un diagramma dei criteri di suddivisione del traffico [SMI](https://smi-spec.io/) da distribuire. Si distribuirà un'altra libreria versione 2 e quindi si dividerà il traffico in ingresso dal bookbuyer, ponderando il 25% del traffico verso il servizio libreria v1 e il 75% al servizio libreria v2.

![Diagramma di suddivisione del traffico del bookbuyer OSM](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Distribuire il servizio libreria v2.

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

Distribuire ora i criteri di suddivisione del traffico per suddividere il traffico bookbuyer tra le due librerie v1 e v2.

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

Configurare un tunnel di port forward per il pod bookbuyer e si dovrebbero visualizzare i libri acquistati dal servizio libreria v2. Se si continua a osservare l'incremento degli acquisti, si noterà un incremento più rapido degli acquisti tramite il servizio libreria v2.

![OSM bookbuyer books boough UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Gestire le applicazioni distribuite esistenti in modo che siano gestite dal componente aggiuntivo Open Service Mesh (OSM) servizio Azure Kubernetes (AKS)

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questa procedura dettagliata presuppongono che in precedenza sia stato abilitato il componente aggiuntivo servizio Web del servizio Web Del servizio Web OSM per il cluster del servizio Web Disaks. In caso contrario, vedere la sezione Abilitare il componente aggiuntivo [OSM (Open Service Mesh) servizio Azure Kubernetes (AKS)](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) per un cluster del servizio Web Diaks esistente prima di procedere. Inoltre, il cluster del servizio Kubernetes deve avere la versione Kubernetes e versioni successive, avere abilitato il controllo degli accessi in base al ruolo di Kubernetes e aver stabilito una connessione con il cluster . Se è necessaria assistenza per uno di questi elementi, vedere la guida introduttiva al servizio Kubernetes e aver installato il componente aggiuntivo `1.19+` `kubectl` AKS OSM. [](./kubernetes-walkthrough.md)

Devono essere installate le risorse seguenti:

- Interfaccia della riga di comando di Azure, versione 2.20.0 o successiva
- Versione `aks-preview` dell'estensione 0.5.5 o successiva
- OSM versione 0.8.0 o successiva
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Verificare i criteri della modalità traffico permissiva OSM (Open Service Mesh)

La modalità dei criteri di traffico permissivi OSM è una modalità in cui l'imposizione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte della rete mesh di servizi e programma le regole dei criteri di traffico in ogni sidecar proxy envoy per poter comunicare con questi servizi.

Per verificare la modalità di traffico permissiva corrente di OSM per il cluster, eseguire il comando seguente:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output della mappa di configurazione OSM dovrebbe essere simile al seguente:

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

Se la **permissive_traffic_policy_mode** è configurata su **true,** è possibile eseguire l'onboardmento sicuro degli spazi dei nomi senza interruzioni delle comunicazioni da servizio a servizio. Se il **permissive_traffic_policy_mode** è configurato su **false,** è necessario assicurarsi di avere distribuito i manifesti dei criteri di accesso al traffico [SMI](https://smi-spec.io/) corretti, nonché di disporre di un account del servizio che rappresenta ogni servizio distribuito nello spazio dei nomi . Seguire le indicazioni per eseguire l'onboard delle applicazioni distribuite esistenti con criteri di traffico [permissivo OSM (Open Service Mesh) configurati come False](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Onboard delle applicazioni distribuite esistenti con i criteri di traffico permissivi OSM (Open Service Mesh) configurati su True

La prima cosa da fare è aggiungere gli spazi dei nomi delle applicazioni distribuite a OSM per la gestione.

```azurecli-interactive
osm namespace add bookstore
```

Dovrebbe venire visualizzato l'output seguente:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Verrà ora osservata la distribuzione del pod corrente nello spazio dei nomi . Eseguire il comando seguente per visualizzare i pod nello spazio dei nomi designato.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Verrà visualizzato l'output simile seguente:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Si noti **che la** colonna READY **mostra 1/1,** ovvero il pod dell'applicazione ha un solo contenitore. Sarà quindi necessario riavviare le distribuzioni dell'applicazione in modo che OSM possa inserire il contenitore proxy sidecar Envoy con il pod dell'applicazione. Si otterrà un elenco delle distribuzioni nello spazio dei nomi .

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Dovrebbe venire visualizzato l'output seguente:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

A questo punto la distribuzione verrà riavviata per inserire il contenitore proxy sidecar Envoy con il pod dell'applicazione. Eseguire il comando indicato di seguito.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Dovrebbe venire visualizzato l'output seguente:

```Output
deployment.apps/bookbuyer restarted
```

Se si osservano di nuovo i pod nello spazio dei nomi:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Si noterà ora che la **colonna READY** mostra **ora 2/2** contenitori pronti per il pod. Il secondo contenitore è il proxy sidecar Envoy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

È possibile esaminare ulteriormente il pod per visualizzare il proxy Envoy eseguendo il comando describe per visualizzare la configurazione.

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

Verificare che l'applicazione sia ancora funzionante dopo l'inserimento del proxy sidecar envoy.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboard delle applicazioni distribuite esistenti con Criteri di traffico permissivi open service mesh (OSM) configurati come False

Quando la configurazione OSM per i criteri di traffico permissivo è impostata su , OSM richiederà criteri di accesso espliciti al traffico SMI distribuiti per la comunicazione da servizio a servizio all'interno `false` del cluster. [](https://smi-spec.io/) Attualmente, OSM usa anche gli account del servizio Kubernetes come parte dell'autorizzazione anche per le comunicazioni da servizio a servizio. Per garantire che le applicazioni distribuite esistenti comunicheranno quando vengono gestite dalla rete OSM, è necessario verificare l'esistenza di un account del servizio da utilizzare, aggiornare la distribuzione dell'applicazione con le informazioni sull'account del servizio, applicare i criteri di accesso al traffico [SMI.](https://smi-spec.io/)

#### <a name="verify-kubernetes-service-accounts"></a>Verificare gli account del servizio Kubernetes

Verificare se si dispone di un account del servizio kubernetes nello spazio dei nomi in cui è distribuita l'applicazione.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Di seguito è riportato un account del servizio denominato nello spazio dei nomi `bookbuyer` bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Se non si dispone di un account del servizio diverso da quello predefinito, sarà necessario crearne uno per l'applicazione. Usare il comando seguente come esempio per creare un account del servizio nello spazio dei nomi distribuito dell'applicazione.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Visualizzare la specifica di distribuzione corrente dell'applicazione

Se è stato necessario creare un account del servizio dalla sezione precedente, è probabile che la distribuzione dell'applicazione non sia configurata con uno `serviceAccountName` specifico nella specifica di distribuzione. È possibile visualizzare la specifica di distribuzione dell'applicazione con i comandi seguenti:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Nell'output verrà visualizzato un elenco di distribuzioni.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

La distribuzione verrà ora descritta come un controllo per verificare se è presente un account del servizio elencato nella sezione Modello di pod.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

In questa distribuzione specifica è possibile vedere che è presente un account del servizio associato alla distribuzione elencata nella sezione Modello di pod. Questa distribuzione usa il bookbuyer dell'account del servizio. Se la proprietà **Serivce Account:** non viene visualizzata, la distribuzione non è configurata per l'uso di un account del servizio.

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

Esistono diverse tecniche per aggiornare la distribuzione per aggiungere un account del servizio Kubernetes. Vedere la documentazione di Kubernetes relativa [all'aggiornamento](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) di una distribuzione inline o [configurare gli account di servizio per i pod.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) Dopo aver aggiornato la specifica di distribuzione con l'account del servizio, ridistribuire (kubectl apply -f your-deployment.yaml) la distribuzione nel cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Distribuire i criteri SMI (Service Mesh Interface) necessari

L'ultimo passaggio per consentire il flusso del traffico autorizzato nella rete consiste nel distribuire i criteri di accesso al traffico [SMI](https://smi-spec.io/) necessari per l'applicazione. La quantità di configurazione che è possibile ottenere con i criteri di accesso al traffico [SMI](https://smi-spec.io/) non è in ambito di questa procedura dettagliata, ma verranno dettagliati alcuni dei componenti comuni della specifica e verrà illustrato come configurare un semplice criterio TrafficTarget e HTTPRouteGroup per abilitare la comunicazione da servizio a servizio per l'applicazione.

La [specifica SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) consente agli utenti di definire i criteri di controllo di accesso per le applicazioni. Verranno incentrate sulle **risorse api TrafficTarget** **e HTTPRoutGroup.**

La risorsa TrafficTarget è costituita da tre principali destinazioni, regole e origini delle impostazioni di configurazione. Di seguito è riportato un esempio di TrafficTarget.

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

Nella specifica TrafficTarget precedente, indica l'account del servizio `destination` configurato per il servizio di origine di destinazione. Tenere presente che l'account del servizio aggiunto in precedenza alla distribuzione verrà usato per autorizzare l'accesso alla distribuzione a cui è collegata. La sezione , in questo particolare esempio, definisce il tipo di traffico `rules` HTTP consentito sulla connessione. È possibile configurare modelli regex granulari per le intestazioni HTTP in modo che siano specifici del traffico consentito tramite HTTP. La `sources` sezione è il servizio che ha origine le comunicazioni. Questa specifica legge il bookbuyer che deve comunicare con la libreria.

La risorsa HTTPRouteGroup è costituita da una o una matrice di corrispondenze di informazioni sull'intestazione HTTP ed è un requisito per la specifica TrafficTarget. Nell'esempio seguente è possibile vedere che HTTPRouteGroup autorizza tre azioni HTTP, due GET e un POST.

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

Se non si ha familiarità con il tipo di traffico HTTP che l'applicazione front-end effettua ad altri livelli dell'applicazione, poiché la specifica TrafficTarget richiede una regola, è possibile creare l'equivalente di una regola allow all usando la specifica seguente per HTTPRouteGroup.

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

Dopo aver configurato le specifiche TrafficTarget e HTTPRouteGroup, è possibile riunirle come un unico YAML e distribuirle. Di seguito è riportata la configurazione di esempio della libreria.

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

Visitare il [sito SMI](https://smi-spec.io/) per informazioni più dettagliate sulla specifica.

### <a name="manage-the-applications-namespace-with-osm"></a>Gestire lo spazio dei nomi dell'applicazione con OSM

Successivamente si configurerà OSM per gestire lo spazio dei nomi e riavviare le distribuzioni per ottenere il proxy sidecar Envoy inserito con l'applicazione.

Eseguire il comando seguente per configurare lo spazio `azure-vote` dei nomi per la gestione del sistema operativo.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Riavviare quindi `azure-vote-front` entrambe `azure-vote-back` le distribuzioni e con i comandi seguenti.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Se si visualizzano i pod per lo spazio dei nomi, verrà visualizzata la fase READY di e come `azure-vote`  `azure-vote-front` 2/2, ovvero il proxy sidecar Envoy è stato inserito insieme `azure-vote-back` all'applicazione.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Esercitazione: Distribuire un'applicazione gestita da Open Service Mesh (OSM) con ingresso NGINX

Open Service Mesh (OSM) è una mesh di servizi nativa del cloud leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità per ambienti di microservizi altamente dinamici.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Visualizzare la configurazione corrente del cluster OSM
> - Creare gli spazi dei nomi per OSM per gestire le applicazioni distribuite negli spazi dei nomi
> - Eseguire l'onboard degli spazi dei nomi che devono essere gestiti da OSM
> - Distribuire l'applicazione di esempio
> - Verificare l'applicazione in esecuzione all'interno del cluster del servizio Web Diaks
> - Creare un controller di ingresso NGINX usato per l'applicazione
> - Esporre un servizio tramite il gateway applicazione di Azure ingresso a Internet

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster del servizio Kubernetes (Kubernetes e versione successiva, con il controllo degli accessi in base al ruolo di Kubernetes abilitato), che sia stata stabilita una connessione con il cluster . Se è necessaria assistenza per uno di questi elementi, vedere la guida introduttiva al servizio Kubernetes e aver installato il componente aggiuntivo OSM del servizio `1.19+` Kubernetes. `kubectl` [](./kubernetes-walkthrough.md)

Devono essere installate le risorse seguenti:

- Interfaccia della riga di comando di Azure, versione 2.20.0 o successiva
- Versione `aks-preview` dell'estensione 0.5.5 o successiva
- OSM versione 0.8.0 o successiva
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualizzare e verificare la configurazione corrente del cluster OSM

Dopo aver abilitato il componente aggiuntivo OSM per il servizio Kubernetes nel cluster del servizio Kubernetes, è possibile visualizzare i parametri di configurazione correnti nella mappa di configurazione di Kubernetes osm-config. Eseguire il comando seguente per visualizzare le proprietà di ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output mostra la configurazione OSM corrente per il cluster.

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

Si noti **che permissive_traffic_policy_mode** è configurato su **true.** La modalità criteri di traffico permissiva in OSM è una modalità in cui l'imposizione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte della rete mesh dei servizi e programma le regole dei criteri di traffico in ogni sidecar proxy Envoy per poter comunicare con questi servizi.

### <a name="create-namespaces-for-the-application"></a>Creare spazi dei nomi per l'applicazione

In questa esercitazione verrà utilizzata l'applicazione libreria OSM con i componenti dell'applicazione seguenti:

- bookbuyer
- bookthief
- Libreria
- bookwarehouse

Creare spazi dei nomi per ognuno di questi componenti dell'applicazione.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboard degli spazi dei nomi da gestire da OSM

L'aggiunta degli spazi dei nomi alla mesh OSM consentirà al controller OSM di inserire automaticamente i contenitori proxy sidecar Envoy con l'applicazione. Eseguire il comando seguente per eseguire l'onboard degli spazi dei nomi delle applicazioni della libreria OSM.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione Bookstore nel cluster del servizio Web Del servizio Web di Microsoft Office

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

Tutti gli output di distribuzione sono riepilogati di seguito.

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

Aggiornare il servizio bookbuyer alla configurazione corretta della porta in ingresso con il manifesto del servizio seguente.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione Bookstore in esecuzione all'interno del cluster del servizio Web del servizio Web di Microsoft Office

Al momento è stata distribuita l'applicazione libreria mulit-container, ma è accessibile solo dall'interno del cluster del servizio AppKs. In un secondo momento si aggiungerà il controller gateway applicazione di Azure in ingresso per esporre l'applicazione all'esterno del cluster del servizio AKS. Per verificare che l'applicazione sia in esecuzione all'interno del cluster, si userà un port forward per visualizzare l'interfaccia utente del componente bookbuyer.

Prima di tutto, ottenere il nome del pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al pod bookbuyer verrà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Dopo aver creato il nome del pod, è possibile usare il comando port-forward per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster del servizio Contenitore di Microsoft. Eseguire il comando seguente per configurare il port forward per la porta di sistema locale 8080. Usare di nuovo il nome del pod bookbuyer specificato.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

L'output dovrebbe essere simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mentre la port forwarding è attiva, passare all'URL seguente da un `http://localhost:8080` browser. A questo punto dovrebbe essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![Immagine dell'app bookbuyer OSM per NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso NGINX in servizio Azure Kubernetes (AKS)

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Il controller di ingresso verrà utilizzato per esporre l'applicazione gestita da OSM a Internet. Per creare il controller di ingresso, usare Helm per installare nginx-ingress. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate _ingress-basic._ Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze.

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

L'output mostra lo stato del controller di ingresso NGINX quando la regola di ingresso è stata applicata correttamente:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Visualizzare esternamente i servizi NGINX e il servizio bookbuyer

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Poiché il nome host nel manifesto di ingresso è un nome psuedo usato per il test, il nome DNS non sarà disponibile in Internet. In alternativa, è possibile usare il programma curl e superare l'intestazione del nome host all'indirizzo IP pubblico NGINX e ricevere un codice 200 che ci connette al servizio bookbuyer.

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Esercitazione: Distribuire un'applicazione gestita da Open Service Mesh (OSM) usando gateway applicazione di Azure componente aggiuntivo servizio Web del servizio Web in ingresso

Open Service Mesh (OSM) è una mesh di servizi nativa cloud leggera, estendibile che consente agli utenti di gestire, proteggere e ottenere in modo uniforme funzionalità di osservabilità avanzate per ambienti di microservizi altamente dinamici.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Visualizzare la configurazione del cluster OSM corrente
> - Creare gli spazi dei nomi per OSM per gestire le applicazioni distribuite negli spazi dei nomi
> - Eseguire l'onboard degli spazi dei nomi da gestire da OSM
> - Distribuire l'applicazione di esempio
> - Verificare l'applicazione in esecuzione all'interno del cluster del servizio Web di Servizio Web Disatteso
> - Creare un gateway applicazione di Azure da usare come controller di ingresso per l'applicazione
> - Esporre un servizio tramite gateway applicazione di Azure ingresso verso Internet

### <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster del servizio Kubernetes (Kubernetes e versione successiva, con il controllo degli accessi in base al ruolo di Kubernetes abilitato), che abbia stabilito una connessione con il cluster . Se è necessaria assistenza per uno di questi elementi, vedere la guida introduttiva del servizio Kubernetes, aver installato il componente aggiuntivo OSM del servizio Kubernetes e verrà creato un nuovo gateway applicazione di Azure per `1.19+` l'ingresso. `kubectl` [](./kubernetes-walkthrough.md)

Devono essere installate le risorse seguenti:

- Interfaccia della riga di comando di Azure, versione 2.20.0 o successiva
- `aks-preview`L'estensione versione 0.5.5 o successiva
- Cluster servizio AzureKs versione 1.19+ con Azure CNI rete virtuale (collegata a una rete virtuale di Azure)
- OSM versione 0.8.0 o successiva
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visualizzare e verificare la configurazione corrente del cluster OSM

Dopo aver abilitato il componente aggiuntivo OSM per il servizio Kubernetes nel cluster del servizio Kubernetes, è possibile visualizzare i parametri di configurazione correnti in osm-config Kubernetes ConfigMap. Eseguire il comando seguente per visualizzare le proprietà di ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

L'output mostra la configurazione OSM corrente per il cluster.

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

Si noti **che permissive_traffic_policy_mode** è configurato su **true.** La modalità dei criteri di traffico permissiva in OSM è una modalità in cui l'imposizione dei criteri di traffico [SMI](https://smi-spec.io/) viene ignorata. In questa modalità, OSM individua automaticamente i servizi che fanno parte della rete mesh di servizi e programma le regole dei criteri di traffico in ogni sidecar proxy envoy per poter comunicare con questi servizi.

### <a name="create-namespaces-for-the-application"></a>Creare spazi dei nomi per l'applicazione

In questa esercitazione verrà utilizzata l'applicazione della libreria OSM con i componenti dell'applicazione seguenti:

- bookbuyer
- bookthief
- Libreria
- bookwarehouse

Creare spazi dei nomi per ognuno di questi componenti dell'applicazione.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Eseguire l'onboard degli spazi dei nomi che devono essere gestiti da OSM

Quando si aggiungono gli spazi dei nomi alla mesh OSM, questo consentirà al controller OSM di inserire automaticamente i contenitori proxy sidecar envoy con l'applicazione. Eseguire il comando seguente per eseguire l'onboard degli spazi dei nomi dell'applicazione della libreria OSM.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuire l'applicazione bookstore nel cluster del servizio Web Disassoce

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

Aggiornare il servizio bookbuyer alla configurazione corretta della porta in ingresso con il manifesto del servizio seguente.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verificare l'applicazione bookstore in esecuzione all'interno del cluster del servizio Web Disassoce

Al momento è stata distribuita l'applicazione multi-contenitore della libreria, ma è accessibile solo dall'interno del cluster del servizio Container. In seguito si aggiungerà il controller di gateway applicazione di Azure in ingresso per esporre l'applicazione all'esterno del cluster del servizio Web Diaks. Per verificare che l'applicazione sia in esecuzione all'interno del cluster, si userà un port forward per visualizzare l'interfaccia utente del componente bookbuyer.

Prima di tutto, ottenere il nome del pod bookbuyer

```azurecli-interactive
kubectl get pod -n bookbuyer
```

L'output dovrebbe essere simile al seguente. Al pod bookbuyer verrà aggiunto un nome univoco.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Dopo aver creato il nome del pod, è ora possibile usare il comando port-forward per configurare un tunnel dal sistema locale all'applicazione all'interno del cluster del servizio AKS. Eseguire il comando seguente per configurare il port forward per la porta del sistema locale 8080. Usare di nuovo il nome del pod bookbuyer specifico.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

L'output dovrebbe essere simile al seguente.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Mentre la port forwarding è attiva, passare all'URL seguente da un `http://localhost:8080` browser. Dovrebbe ora essere possibile visualizzare l'interfaccia utente dell'applicazione bookbuyer nel browser simile all'immagine seguente.

![Immagine dell'interfaccia utente dell'app bookbuyer OSM per Gateway app](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Creare un'gateway applicazione di Azure per esporre l'applicazione bookbuyer all'esterno del cluster del servizio Web Dicking

> [!NOTE]
> Le istruzioni seguenti creeranno una nuova istanza del gateway applicazione di Azure da usare per l'ingresso. Se si dispone di un gateway applicazione di Azure da usare, passare alla sezione per abilitare il componente aggiuntivo Controller in ingresso del gateway applicazione.

#### <a name="deploy-a-new-application-gateway"></a>Distribuire un nuovo gateway applicazione

> [!NOTE]
> Si fa riferimento alla documentazione esistente per l'abilitazione del componente aggiuntivo Controller in ingresso del gateway applicazione per un cluster del servizio Gateway Gateway esistente. Sono state apportate alcune modifiche per adattare i materiali OSM. La documentazione più dettagliata sull'argomento è disponibile [qui.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

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

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Abilitare il componente aggiuntivo AGIC per un cluster del servizio AzureKs esistente tramite l'interfaccia della riga di comando di Azure

Se si desidera continuare a usare l'interfaccia della riga di comando di Azure, è possibile continuare a abilitare il componente aggiuntivo AGIC nel cluster del servizio AzureKs creato, _myCluster,_ e specificare il componente aggiuntivo AGIC per usare il gateway applicazione esistente creato, _myApplicationGateway._

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

È possibile verificare che gateway applicazione di Azure componente aggiuntivo servizio Web del servizio Web di Microsoft Windows sia stato abilitato con il comando seguente.

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

Applicare il manifesto di ingresso seguente al cluster del servizio Diaks per esporre il servizio bookbuyer a Internet tramite il gateway applicazione di Azure.

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

Poiché il nome host nel manifesto di ingresso è uno pseudonome usato per il test, il nome DNS non sarà disponibile in Internet. In alternativa, è possibile usare il programma curl e superare l'intestazione del nome host all'indirizzo IP pubblico gateway applicazione di Azure e ricevere un codice 200 che ci connette al servizio bookbuyer.

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
- [Altri strumenti per la risoluzione dei problemi sono disponibili nel repository GitHub di AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Monitoraggio e osservabilità di Open Service Mesh (OSM) usando Monitoraggio di Azure e Applications Insights

Sia Monitoraggio di Azure che applicazione Azure Insights consentono di ottimizzare la disponibilità e le prestazioni delle applicazioni e dei servizi offrendo una soluzione completa per la raccolta, l'analisi e l'azione sui dati di telemetria dal cloud e dagli ambienti locali.

Il componente aggiuntivo OSM AKS avrà integrazioni approfondite in entrambi questi servizi di Azure e offrirà un'esperienza di Azure senza problemi per la visualizzazione e la risposta agli indicatori KPI critici forniti dalle metriche OSM. Per altre informazioni su come abilitare e configurare questi servizi per il componente aggiuntivo OSM AKS, visitare la pagina Monitoraggio di Azure [per OSM.](https://aka.ms/azmon/osmpreview)

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Esercitazione: Distribuire manualmente Prometheus, Grafana e Jaeger per visualizzare le metriche OSM (Open Service Mesh) per l'osservabilità

> [!WARNING]
> L'installazione di Prometheus, Grafana e Jaeger viene fornita come indicazioni generali per illustrare come questi strumenti possono essere utilizzati per visualizzare i dati delle metriche OSM. Le linee guida per l'installazione non devono essere utilizzate per una configurazione di produzione. Per informazioni su come adattare al meglio le proprie installazioni in base alle proprie esigenze, vedere la documentazione di ogni strumento. La cosa più importante è la mancanza di spazio di archiviazione permanente, vale a dire che tutti i dati vengono persi quando prometheus Grafana e/o i pod Jaeger vengono terminati.

Open Service Mesh (OSM) genera metriche dettagliate correlate a tutto il traffico all'interno della mesh. Queste metriche forniscono informazioni dettagliate sul comportamento delle applicazioni nella rete mesh che consentono agli utenti di risolvere i problemi, gestire e analizzare le applicazioni.

Al giorno d'oggi OSM raccoglie le metriche direttamente dai proxy sidecar (Envoy). Il servizio OSM offre metriche specifiche per il traffico in ingresso e in uscita per tutti i servizi nella rete mesh. Con queste metriche, l'utente può ottenere informazioni sul volume complessivo del traffico, sugli errori all'interno del traffico e sul tempo di risposta per le richieste.

OSM usa Prometheus per raccogliere e archiviare metriche e statistiche del traffico coerenti per tutte le applicazioni in esecuzione nella mesh. Prometheus è un toolkit di monitoraggio e avviso open source, comunemente usato negli ambienti Kubernetes e Service Mesh.

Ogni applicazione che fa parte della mesh viene eseguita in un pod che contiene un sidecar Envoy che espone le metriche (metriche proxy) nel formato Prometheus. Inoltre, ogni pod che fa parte della mesh ha annotazioni Prometheus, che consente al server Prometheus di raschiare l'applicazione in modo dinamico. Questo meccanismo abilita automaticamente lo scraping delle metriche ogni volta che un nuovo spazio dei nomi/pod/servizio viene aggiunto alla mesh.

Le metriche OSM possono essere visualizzate con Grafana, un software di visualizzazione e analisi open source. Consente di eseguire query, visualizzare, visualizzare avvisi ed esplorare le metriche.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> - Creare e distribuire un'istanza di Prometheus
> - Configurare OSM per consentire lo scraping di Prometheus
> - Aggiornare la mappa di configurazione di Prometheus
> - Creare e distribuire un'istanza di Grafana
> - Configurare Grafana con l'origine dati Prometheus
> - Importare il dashboard OSM per Grafana
> - Creare e distribuire un'istanza di Jaeger
> - Configurare la traccia Jaeger per OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Distribuire e configurare un'istanza di Prometheus per OSM

Si userà Helm per distribuire l'istanza di Prometheus. Eseguire i comandi seguenti per installare Prometheus tramite Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Se l'installazione ha avuto esito positivo, verrà visualizzato un output simile. Prendere nota della porta del server Prometheus e del nome DNS del cluster. Queste informazioni verranno usate in seguito per configurare Prometheus come origine dati per Grafana.

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

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Configurare OSM per consentire lo scraping di Prometheus

Per assicurarsi che i componenti OSM siano configurati per gli scarti  di Prometheus, è necessario controllare la configurazione prometheus_scraping disponibile nel file di configurazione osm-config. Visualizzare la configurazione con il comando seguente:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

L'output del comando precedente deve restituire `true` se OSM è configurato per lo scraping di Prometheus. Se il valore restituito `false` è , sarà necessario aggiornare la configurazione in modo che sia `true` . Eseguire il comando seguente per attivare **lo scraping** OSM Prometheus:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Viene visualizzato l'output seguente.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Aggiornare la mappa di configurazione di Prometheus

L'installazione predefinita di Prometheus conterrà due mappe di configurazione kubernetes. È possibile visualizzare l'elenco di configmap prometheus con il comando seguente.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Sarà necessario sostituire la configurazione prometheus.yml che si trova nella mappa di configurazione **stable-prometheus-server** con la configurazione OSM seguente. Esistono diverse tecniche di modifica dei file per eseguire questa attività. Un modo semplice e sicuro è esportare la mappa di configurazione, crearne una copia per il backup e quindi modificarla con un editor, ad esempio Visual Studio codice.

> [!NOTE]
> Se non è installato Visual Studio Code, è possibile scaricarlo e installarlo [qui](https://code.visualstudio.com/Download).

Esportare prima la mappa di configurazione **stable-prometheus-server** e quindi creare una copia per il backup.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Si aprirà quindi il file usando Visual Studio Code da modificare.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Dopo aver aperto configmap nell'editor Visual Studio Code, sostituire il file prometheus.yml con la configurazione del modulo di protezione hardware seguente e salvare il file.

> [!WARNING]
> È estremamente importante assicurarsi di mantenere la struttura di indention del file yaml. Qualsiasi modifica alla struttura del file yaml potrebbe causare la non possibilità di applicare nuovamente la mappa di configurazione.

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

Applicare il file yaml configmap aggiornato con il comando seguente.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> È possibile che venga visualizzato un messaggio che indica che è necessaria un'annotazione kubernetes mancante. Questa operazione può essere ignorata per il momento.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Verificare che Prometheus sia configurato per raschiare la mesh OSM e gli endpoint API

Per verificare che Prometheus sia configurato correttamente per raschiare la mesh OSM e gli endpoint API, verrà eseguito il port forward al pod Prometheus e verrà vista la configurazione di destinazione. Eseguire i comandi seguenti.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Aprire un browser fino a `http://localhost:9090/targets`

Se si scorre verso il basso, tutti gli endpoint delle metriche SMI dovrebbero essere **up** e altre metriche OSM definite come illustrato di seguito.

![Immagine dell'interfaccia utente delle metriche di destinazione di OSM Prometheus](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Distribuire e configurare un'istanza di Grafana per OSM

Si userà Helm per distribuire l'istanza di Grafana. Eseguire i comandi seguenti per installare Grafana tramite Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Si recupererà quindi la password predefinita di Grafana per accedere al sito di Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Prendere nota della password Grafana.

Successivamente si recupererà il pod Grafana per eseguire il port forward al dashboard di Grafana per l'accesso.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Aprire un browser fino a `http://localhost:3000`

Nella schermata di accesso illustrata di seguito immettere **admin** come nome utente e usare la password Grafana acquisita in precedenza.

![Immagine dell'interfaccia utente della pagina di accesso di OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Configurare l'origine dati Grafana Prometheus

Dopo aver eseguito l'accesso a Grafana, il passaggio successivo consiste nell'aggiungere Prometheus come origini dati per Grafana. A tale scopo, passare all'icona di configurazione nel menu a sinistra e selezionare Origini dati come illustrato di seguito.

![Immagine dell'interfaccia utente della pagina OSM Grafana Datasources](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Fare clic **sul pulsante Add data source** (Aggiungi origine dati) e selezionare Prometheus nei database time series.

![Immagine dell'interfaccia utente della pagina di selezione delle origini dati OSM Grafana](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Nella pagina **Configure your Prometheus data source** below (Configurare l'origine dati Prometheus riportata di seguito) immettere l'FQDN del cluster Kubernetes per l'impostazione Prometheus service for the HTTP URL (FQDN cluster Kubernetes per il servizio Prometheus per l'URL HTTP). Il nome di dominio completo predefinito deve essere `stable-prometheus-server.default.svc.cluster.local` . Dopo aver immesso l'endpoint del servizio Prometheus, scorrere fino alla fine della pagina e selezionare Salva & **test**. Si dovrebbe ricevere una casella di controllo verde che indica che l'origine dati funziona.

#### <a name="importing-osm-dashboards"></a>Importazione di dashboard OSM

I dashboard OSM sono disponibili tramite:

- [Il repository](https://github.com/grafana/grafana)e sono importabili come BLOB JSON tramite il portale di amministrazione Web
- o [online all'indirizzo Grafana.com](https://grafana.com/grafana/dashboards/14145)

Per importare un dashboard, cercare il `+` segno nel menu a sinistra e selezionare `import` .
È possibile importare direttamente il dashboard in base al relativo ID in `Grafana.com` . Ad esempio, il `OSM Mesh Details` dashboard usa l'ID `14145` , è possibile usare l'ID direttamente nel modulo e selezionare `import` :

![Immagine dell'interfaccia utente della pagina di importazione del dashboard OSM Grafana](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Non appena si seleziona Importa, si verrà automaticamente visualizzati nel dashboard importato.

![Immagine dell'interfaccia utente della pagina dei dettagli di OSM Grafana Dashboard Mesh](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Distribuire e configurare un operatore Jaeger in Kubernetes per OSM

[Jaeger è un](https://www.jaegertracing.io/) sistema di traccia open source usato per il monitoraggio e la risoluzione dei problemi dei sistemi distribuiti. Può essere distribuito con OSM come nuova istanza oppure è possibile usare la propria istanza. Le istruzioni seguenti distribuiscono una nuova istanza di Jaeger nello spazio `jaeger` dei nomi nel cluster del servizio Web Diaks.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Distribuire Jaeger nel cluster del servizio AKS

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

Sarà quindi necessario abilitare la traccia per il componente aggiuntivo OSM.

> [!NOTE]
> Al momento le proprietà di traccia non sono disponibili nella mappa di configurazione osm-config. Questo sarà reso fattibile in una nuova versione del componente aggiuntivo OSM AKS.

Eseguire il comando seguente per abilitare la traccia per il componente aggiuntivo OSM:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Visualizzare l'interfaccia utente di Jaeger con port forwarding

L'interfaccia utente di Jaeger è in esecuzione sulla porta 16686. Per visualizzare l'interfaccia utente Web, è possibile usare kubectl port-forward:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

Nel browser dovrebbe essere visualizzato un elenco a discesa Servizio che consente di selezionare tra le varie applicazioni distribuite dalla demo della libreria. Selezionare un servizio per visualizzare tutti gli intervalli da esso. Ad esempio, se si seleziona bookbuyer con lookback di un'ora, è possibile visualizzare le interazioni con bookstore-v1 e bookstore-v2 ordinate in base all'ora.

![Immagine dell'interfaccia utente della pagina di traccia OSM Jaeger](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Selezionare un elemento per visualizzarlo in modo più dettagliato. Selezionare più elementi per confrontare le tracce. Ad esempio, è possibile confrontare le interazioni del bookbuyer con la libreria e bookstore-v2 in un determinato momento.

È anche possibile selezionare la scheda Architettura di sistema per visualizzare un grafico dell'interazione/comunicazione tra le varie applicazioni. In questo modo si ha un'idea del flusso del traffico tra le applicazioni.

![Immagine dell'interfaccia utente dell'architettura del sistema OSM Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS add-on Troubleshooting Guides

Quando si distribuisce il componente aggiuntivo OSM AKS, è possibile che si verifichi occasionalmente un problema. Le guide seguenti illustrano come risolvere gli errori e risolvere i problemi comuni.

### <a name="verifying-and-troubleshooting-osm-components"></a>Verifica e risoluzione dei problemi dei componenti OSM

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

Anche se a un certo punto è stato sgomberato un controller, ne è disponibile un altro pronto 1/1 e in esecuzione con 0 riavvii. Se la colonna READY è diversa da 1/1, la mesh di servizi si trova in uno stato danneggiato.
La colonna READY con 0/1 indica che il contenitore del piano di controllo si arresta in modo anomalo. È necessario ottenere i log. Vedere la sezione Ottenere i log del controller OSM supporto di Azure Center più avanti. La colonna READY con un numero maggiore di 1 dopo / indicherà che sono installati sidecar. Il controller OSM probabilmente non funzionerà con i sidecar collegati.

> [!NOTE]
> A data la versione 0.8.2 il controller OSM non è in modalità a disponibilità singola e verrà eseguito in una distribuzione con numero di repliche di 1 - singolo pod. Il pod ha probe di integrità e verrà riavviato dal kubelet, se necessario.

#### <a name="check-osm-controller-service"></a>Controllare il servizio controller OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Un servizio controller OSM integro sarà simile al seguente:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> Cluster-IP sarebbe diverso. I nomi dei servizi e PORT(S) devono essere gli stessi dell'esempio precedente.

#### <a name="check-osm-controller-endpoints"></a>Controllare gli endpoint del controller OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Uno o più endpoint controller OSM integri hanno un aspetto simile al seguente:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Controllare la distribuzione dell'injector OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Una distribuzione dell'injector OSM integro sarà simile alla seguente:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Controllare il pod dell'injector OSM

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Un pod OSM Injector integro sarà simile al seguente:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Controllare il servizio injector OSM

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Un servizio OSM Injector integro sarà simile al seguente:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Controllare gli endpoint OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Un endpoint OSM integro sarà simile al seguente:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Controllare la convalida e la modifica dei webhook

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

Un webhook di mutamento OSM integro sarà simile al seguente:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Verificare il servizio e il bundle CA del webhook convalida

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configurazione del webhook di convalida ben configurata sarà simile alla seguente:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Verificare il servizio e il bundle CA del webhook Di modifica

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Una configurazione webhook di modifica ben configurata sarà simile alla seguente:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Controllare se il controller OSM ha fornito al webhook di convalida (o di modifica) un bundle ca

> [!NOTE]
> A oggi v0.8.2 è importante sapere che il componente del servizio Web Del servizio Web DinK installa il webhook di convalida, il riconciliatore del servizio Diconciso del servizio Diconciso del servizio Web DinK ne garantisce l'esistenza, ma il controller OSM è quello che riempie il bundle ca.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Questo numero indica il numero di byte o le dimensioni del bundle ca. Se il valore è vuoto, 0 o un numero inferiore a 1000, il provisioning del bundle della CA non viene eseguito correttamente. Senza un bundle CA corretto, il webhook di convalida restituirebbe un errore e impedirebbe all'utente di apportare modifiche a ConfigMap osm-config nello spazio dei nomi kube-system.

Un errore di esempio quando il bundle della CA non è corretto:

- Tentativo di modificare osm-config ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Errore:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Risolvere il problema quando la **convalida della configurazione** del webhook ha un certificato non valido:

- Opzione 1 : riavvia il controller OSM. Il controller OSM verrà riavviato. All'avvio sovrascriverà il bundle CA di entrambi i webhook Mutating e Validating.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Opzione 2 - Opzione 2. Eliminare il webhook di convalida: la rimozione del webhook di convalida non rende più convalidate le modifica `osm-config` di ConfigMap. Verrà passata qualsiasi patch. A un certo punto, riconciliatore del servizio Web Diconciliazione del servizio AKS garantisce l'esistenza del webhook di convalida e lo ricrea. Potrebbe essere necessario riavviare il controller OSM per riscrivere rapidamente il bundle ca.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Opzione 3 - Elimina e applica patch: il comando seguente elimina il webhook di convalida, consentendo di aggiungere qualsiasi valore e tenterà immediatamente di applicare una patch. Molto probabilmente il riconciliatore del servizio Web DinK non avrà tempo sufficiente per riconciliare e ripristinare il webhook di convalida, offrendo la possibilità di applicare una modifica come ultima risorsa:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Controllare `osm-config` **ConfigMap**

> [!NOTE]
> Il controller OSM non richiede che ConfigMap sia presente nello spazio dei nomi `osm-config` kube-system. Il controller ha valori predefiniti ragionevoli per la configurazione e può operare senza di esso.

Verificare l'esistenza:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Controllare il contenuto di ConfigMap osm-config

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

| Chiave                              | Type   | Valori consentiti                                          | Valore predefinito                          | Funzione                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Uscita                           | bool   | true, false                                             | `"false"`                              | Abilita l'uscita nella mesh.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | true, false                                             | `"true"`                               | Consente a un endpoint di debug nel pod osm-controller di elencare informazioni relative alla mesh, ad esempio connessioni proxy, certificati e criteri SMI.                                                                                    |
| enable_privileged_init_container | bool   | true, false                                             | `"false"`                              | Abilita i contenitori init con privilegi per i pod in mesh. Se false, i contenitori init hanno solo NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | string | trace, debug, info, warning, warn, error, critical, off | `"error"`                              | Imposta il livello di dettaglio di registrazione del sidecar proxy Envoy, applicabile solo ai pod appena creati che si uniscono alla mesh. Per aggiornare il livello di log per i pod esistenti, riavviare la distribuzione con `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | string | Elenco delimitato da virgole di intervalli IP nel formato a.b.c.d/x | `-`                                    | Elenco globale degli intervalli di indirizzi IP da escludere dall'intercettazione del traffico in uscita dal proxy sidecar.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | true, false                                             | `"false"`                              | L'impostazione su abilita la modalità allow-all nella mesh, ad esempio nessuna imposizione dei criteri di `true` traffico nella mesh. Se impostato su , abilita i criteri di negazione del traffico nella rete mesh, ad esempio un è `false` necessario per consentire ai servizi di `SMI Traffic Target` comunicare. |
| prometheus_scraping              | bool   | true, false                                             | `"true"`                               | Abilita lo scarto delle metriche di Prometheus nei proxy sidecar.                                                                                                                                                                                 |
| service_cert_validity_duration   | string | 24 ore, 1h30 minuti (qualsiasi durata)                          | `"24h"`                                | Imposta la durata di validità del certificato del servizio, rappresentata come sequenza di numeri decimali, ognuno con frazione facoltativa e suffisso di unità.                                                                                             |
| tracing_enable                   | bool   | true, false                                             | `"false"`                              | Abilita la traccia jaeger per la mesh.                                                                                                                                                                                                    |
| tracing_address                  | string | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Indirizzo della distribuzione Jaeger, se la traccia è abilitata.                                                                                                                                                                                |
| tracing_endpoint                 | string | /api/v2/spans                                           | /api/v2/spans                          | Endpoint per i dati di traccia, se la traccia è abilitata.                                                                                                                                                                                          |
| tracing_port                     | INT    | qualsiasi valore intero diverso da zero                              | `"9411"`                               | Porta su cui è abilitata la traccia.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | true, false                                             | `"false"`                              | Abilita l'ingresso HTTPS nella rete.                                                                                                                                                                                                      |
| config_resync_interval           | string | inferiore a 1 minuto disabilita questa opzione                            | 0 (disabilitata)                           | Quando viene specificato un valore superiore a 1 m (60 secondi), il controller OSM invierà tutte le impostazioni di configurazione disponibili a ogni envoy connesso all'intervallo specificato                                                                                                    |

#### <a name="check-namespaces"></a>Controllare gli spazi dei nomi

> [!NOTE]
> Lo spazio dei nomi kube-system non parteciperà mai a una rete mesh di servizi e non verrà mai etichettato e/o annotato con la chiave/valori riportata di seguito.

Il comando viene `osm namespace add` utilizzato per unire gli spazi dei nomi a una mesh di servizio specificata.
Quando uno spazio dei nomi k8s fa parte della mesh (o perché sia parte della mesh), deve essere vero quanto segue:

Visualizzare le annotazioni con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Deve essere presente l'annotazione seguente:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Visualizzare le etichette con

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Deve essere presente l'etichetta seguente:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Se uno spazio dei nomi non è annotato con o senza etichetta con `"openservicemesh.io/sidecar-injection": "enabled"` `"openservicemesh.io/monitored-by": "osm"` l'injector OSM non aggiungerà sidecar Envoy.

> Nota: dopo la chiamata, verranno inseriti solo nuovi `osm namespace add` pod con un sidecar Envoy.  I pod esistenti devono essere riavviati con `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Verificare i CRD SMI:

Controllare se il cluster ha i CRD necessari:

```azurecli-interactive
kubectl get crds
```

Nel cluster devono essere installati gli elementi seguenti:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Ottenere le versioni dei CRD installate con questo comando:

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

Il controller OSM v0.8.2 richiede le versioni seguenti:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io- Non supportato
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Se mancano i CRD, usare i comandi seguenti per installarlo nel cluster:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Disabilitare il componente aggiuntivo Open Service Mesh (OSM) per il cluster del servizio Microsoft Service Mesh

Per disabilitare il componente aggiuntivo OSM, eseguire il comando seguente:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
