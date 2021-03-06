---
title: Usare il controller di ingresso con IP statico
titleSuffix: Azure Kubernetes Service
description: Informazioni su come installare e configurare un controller di ingresso NGINX con un indirizzo IP pubblico statico in un cluster del servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779832"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso con un indirizzo IP pubblico statico nel servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Azure Kubernetes. Il controller di ingresso è configurato con un indirizzo IP pubblico statico. Il progetto [cert-manager][cert-manager] viene usato per generare e configurare automaticamente certificati [Let's Encrypt][lets-encrypt]. Infine, due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS con un indirizzo IP pubblico dinamico][aks-ingress-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Questo articolo usa [Helm 3 per][helm] installare il controller di ingresso NGINX e cert-manager. Assicurarsi di usare la versione più recente di Helm e di avere accesso ai repository *Helm ingress-nginx* e *jetstack.* Per istruzioni sull'aggiornamento, vedere [la documentazione sull'installazione di Helm.][helm-install] Per altre informazioni sulla configurazione e sull'uso di Helm, vedere Installare applicazioni [con Helm in servizio Azure Kubernetes (AKS).][use-helm]

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per impostazione predefinita, un controller di ingresso NGINX viene creato con l'assegnazione di un nuovo indirizzo IP pubblico. Questo indirizzo IP pubblico è solo statico per la durata del controller di ingresso e viene perso se il controller viene eliminato e ricreato. Un requisito di configurazione comune consiste nel fornire al controller di ingresso NGINX un indirizzo IP pubblico statico esistente. L'indirizzo IP pubblico statico rimane quando il controller di ingresso viene eliminato. Questo approccio consente di usare i record DNS e le configurazioni di rete esistenti in modo coerente per tutto il ciclo di vita delle applicazioni.

Se è necessario creare un indirizzo IP pubblico statico, è necessario ottenere prima il nome del gruppo di risorse del cluster servizio Azure Kubernetes con il comando [az servizio Azure Kubernetes show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Successivamente, creare un indirizzo IP pubblico con il metodo di allocazione *statico* usando il comando [az network public-ip create][az-network-public-ip-create]. Nell'esempio seguente viene creato un indirizzo IP pubblico denominato *myservizio Azure KubernetesPublicIP* nel gruppo di risorse cluster servizio Azure Kubernetes ottenuto nel passaggio precedente:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> I comandi precedenti creano un indirizzo IP che verrà eliminato se si elimina il cluster del servizio Web Diaks. In alternativa, è possibile creare un indirizzo IP in un gruppo di risorse diverso che può essere gestito separatamente dal cluster del servizio Web Diaks. Se si crea un indirizzo IP in un gruppo di risorse diverso, assicurarsi che l'identità del cluster usata dal cluster del servizio Web Disarticciato abbia autorizzazioni delegate per l'altro gruppo di risorse, ad esempio Collaboratore *Rete.* Per altre informazioni, vedere Usare un indirizzo IP pubblico statico e un'etichetta DNS con il servizio di bilanciamento del carico del servizio [AzureKs.][aks-static-ip]

A questo punto distribuire il grafico *ingress nginx* con Helm. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

È necessario passare due parametri aggiuntivi alla versione Helm in modo che il controller di ingresso sia in grado di riconoscere sia l'indirizzo IP statico del servizio di bilanciamento del carico da allocare al servizio controller di ingresso sia l'etichetta del nome DNS applicata alla risorsa indirizzo IP pubblico. Per il corretto funzionamento dei certificati HTTPS, viene usata un'etichetta del nome DNS per configurare un fqdn per l'indirizzo IP del controller di ingresso.

1. Aggiungere il `--set controller.service.loadBalancerIP` parametro . Specificare il proprio indirizzo IP pubblico creato nel passaggio precedente.
1. Aggiungere il `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parametro . Specificare un'etichetta del nome DNS da applicare all'indirizzo IP pubblico creato nel passaggio precedente.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-basic.* Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze. Se il cluster del servizio Kubernetes non è abilitato per il controllo degli accessi in base al ruolo di Kubernetes, `--set rbac.create=false` aggiungere ai comandi Helm.

> [!TIP]
> Se si vuole abilitare la conservazione [dell'IP di origine client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando di installazione Helm. L'indirizzo IP di origine del client viene archiviato nell'intestazione della richiesta *in X-Forwarded-For.* Quando si usa un controller di ingresso con la conservazione dell'IP di origine client abilitata, il pass-through TLS non funzionerà.

Aggiornare lo script seguente con **l'indirizzo** IP  del controller di ingresso e un nome univoco da usare per il prefisso FQDN.

> [!IMPORTANT]
> È necessario aggiornare *sostituire* STATIC_IP e *DNS_LABEL* con il proprio indirizzo IP e il nome univoco quando si esegue il comando.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name&quot;=&quot;DNS_LABEL"
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato l'indirizzo IP statico, come illustrato nell'output dell'esempio seguente:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Non sono ancora state create regole di ingresso, quindi se si passa all'indirizzo IP pubblico si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX. Le regole di ingresso sono configurate nei passaggi seguenti.

È possibile verificare che l'etichetta del nome DNS sia stata applicata tramite una query sul nome FQDN sull'indirizzo IP pubblico come indicato di seguito:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Il controller di ingresso è ora accessibile tramite l'indirizzo IP o il nome di dominio completo.

## <a name="install-cert-manager"></a>Installare cert-manager

Il controller di ingresso NGINX supporta la terminazione TLS. Il recupero e la configurazione di certificati per HTTPS possono essere eseguiti in modi diversi. Questo articolo descrive l'uso di [cert-manager][cert-manager], che consente di generare automaticamente il certificato [Let's Encrypt][lets-encrypt] e che dispone di funzionalità di gestione.

> [!NOTE]
> Nell'articolo viene usato l'ambiente `staging` per Let's Encrypt. Nelle distribuzioni di produzione usare `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nella definizione delle risorse e durante l'installazione del grafico Helm.

Per installare il controller cert-manager in un cluster abilitato per il controllo degli accessi in base al ruolo di Kubernetes, usare il comando `helm install` seguente:

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Per altre informazioni sulla configurazione di cert-manager, vedere [progetto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Creare un'autorità di certificazione (CA) cluster

Per poter emettere i certificati, cert-manager richiede un [autorità di certificazione][cert-manager-issuer] o una risorsa [ClusterIssuer][cert-manager-cluster-issuer]. Queste risorse Kubernetes hanno funzionalità identiche, ma `Issuer` funziona in uno spazio dei nomi singolo, mentre `ClusterIssuer` funziona in tutti gli spazi dei nomi. Per altre informazioni, vedere la documentazione relativa all'[autorità di certificazione cert-manager][cert-manager-issuer].

Creare un'autorità di certificazione del cluster, ad esempio `cluster-issuer.yaml`, tramite il manifesto di esempio seguente. Aggiornare l'indirizzo di posta elettronica con un indirizzo valido della propria organizzazione:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Per creare l'autorità di certificazione, usare il comando `kubectl apply`.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

L'output dovrebbe essere simile all'esempio seguente:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Sono stati configurati un controller di ingresso e una soluzione di gestione dei certificati. A questo punto è possibile eseguire due applicazioni demo nel cluster servizio Azure Kubernetes. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione "Hello world".

Per vedere il controller di ingresso in azione, eseguire due applicazioni demo nel cluster del servizio Web Diaks. In questo esempio si usa `kubectl apply` per distribuire due istanze di una semplice applicazione *Hello* World.

Creare un file *aks-helloworld.yaml* e copiarlo nell'esempio YAML seguente:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Creare un file *ingress-demo.yaml* e copiarlo nell'esempio YAML seguente:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Eseguire le due applicazioni demo usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono ora in esecuzione nel cluster Kubernetes, anche se sono configurate con un servizio di tipo `ClusterIP`. Di conseguenza, le applicazioni non sono accessibili da Internet. Per renderle disponibili pubblicamente, creare una risorsa di ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` viene instradato al servizio `ingress-demo`. Aggiornare gli elementi *hosts* e *host* in base al nome DNS creato in un passaggio precedente.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply`.

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

L'output dovrebbe essere simile all'esempio seguente:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Creare un oggetto certificato

Successivamente, è necessario creare una risorsa certificato. La risorsa certificato definisce il certificato X.509 desiderato. Per altre informazioni, vedere [Certificati cert-manager.][cert-manager-certificates]

Cert-manager ha probabilmente creato automaticamente un oggetto certificato per l'uso in ingress-shim, che viene distribuito automaticamente con cert-manager dalla versione 0.2.2. Per altre informazioni, consultare la [documentazione su ingress-shim][ingress-shim].

Per verificare che il certificato sia stato creato correttamente, usare il comando `kubectl describe certificate tls-secret --namespace ingress-basic`.

Se il certificato è stato rilasciato, l'output sarà simile al seguente:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se è necessario creare una risorsa certificato aggiuntiva, è possibile farlo con il manifesto di esempio seguente. Aggiornare gli elementi *dnsNames* e *domains* in base al nome DNS creato in un passaggio precedente. Se si usa solo un controller di ingresso interno specificare il nome DNS interno per il servizio.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Per creare la risorsa certificato, usare il comando `kubectl apply`.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testare la configurazione di ingresso

Aprire un Web browser per l'FQDN del controller di ingresso Kubernetes, ad esempio *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Poiché questi esempi usano `letsencrypt-staging` , il certificato TLS/SSL emesso non è considerato attendibile dal browser. Accettare l'avviso per continuare l'applicazione. Le informazioni sul certificato indicano che il certificato *Fake LE Intermediate X1* è emesso da Let's Encrypt. Questo certificato fittizio indica che `cert-manager` ha elaborato la richiesta in modo corretto e ha ricevuto un certificato dal provider:

![Certificato temporaneo Let's Encrypt](media/ingress/staging-certificate.png)

Quando si modifica Let's Encrypt per usare `prod` anziché `staging`, viene usato un certificato attendibile emesso da Let's Encrypt, come illustrato nell'esempio seguente:

![Certificato Let's Encrypt](media/ingress/certificate.png)

L'applicazione demo viene visualizzata nel Web browser:

![Esempio di applicazione 1](media/ingress/app-one.png)

A questo punto aggiungere il percorso */hello-world-two* al nome FQDN, ad esempio *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*. Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Esempio di applicazione 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso, i certificati e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare `kubectl delete` il comando e specificare il nome dello spazio dei nomi. Tutte le risorse nello spazio dei nomi vengono eliminate.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminare le risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Rimuovere prima di tutto le risorse del certificato:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Elencare ora le versioni di Helm con il comando `helm list`. Cercare i grafici *denominati nginx-ingress* e *cert-manager,* come illustrato nell'output di esempio seguente:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Disinstallare le versioni con il `helm uninstall` comando . L'esempio seguente disinstalla la distribuzione in ingresso NGINX e le distribuzioni di Gestione certificati.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Rimuovere quindi le due applicazioni di esempio:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Eliminare lo spazio dei nomi stesso. Usare il comando `kubectl delete` e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

Rimuovere infine l'indirizzo IP pubblico statico creato per il controller di ingresso. Specificare il nome del gruppo di risorse cluster *MC_* ottenuto nel primo passaggio di questo articolo, ad esempio *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]
- [cert-manager][cert-manager]

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
