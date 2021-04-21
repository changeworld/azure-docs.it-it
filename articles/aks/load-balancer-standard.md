---
title: Usare un'istanza Load Balancer
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare un servizio di bilanciamento del carico pubblico con uno SKU Standard per esporre i servizi con servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783090"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un servizio Load Balancer Standard in servizio Azure Kubernetes (AKS)

Il Azure Load Balancer si trova sull'L4 del modello OSI (Open Systems Interconnection) che supporta scenari sia in ingresso che in uscita. Distribuisce i flussi in ingresso che arrivano al front-end del servizio di bilanciamento del carico alle istanze del pool back-end.

Un **servizio pubblico Load Balancer** integrato con il servizio Diaks ha due scopi:

1. Per fornire connessioni in uscita ai nodi del cluster all'interno della rete virtuale del servizio DinK. Questo obiettivo viene raggiunto traducendo l'indirizzo IP privato dei nodi in un indirizzo IP pubblico che fa parte del *pool in uscita*.
2. Per fornire l'accesso alle applicazioni tramite i servizi Kubernetes di tipo `LoadBalancer` . Con questa funzionalità è possibile ridimensionare facilmente le applicazioni e creare servizi a disponibilità elevata.

Viene **usato un servizio di bilanciamento del** carico interno (o privato) in cui solo gli indirizzi IP privati sono consentiti come front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È anche possibile accedere a un front-end di bilanciamento del carico da una rete locale in uno scenario ibrido.

Questo documento illustra l'integrazione con il servizio di bilanciamento del carico pubblico. Per l'integrazione Load Balancer, vedere la documentazione sul servizio di bilanciamento [del carico interno del servizio AzureKs.](internal-lb.md)

## <a name="before-you-begin"></a>Prima di iniziare

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, lo SKU *Standard* viene usato quando si crea un cluster del servizio Web Diaks. Usare lo SKU *Standard* per accedere a funzionalità aggiuntive, ad esempio un pool back-end più [**grande,**](use-multiple-node-pools.md)più pool di nodi [**e**](availability-zones.md)zone di disponibilità . Si tratta dello SKU Load Balancer per il Load Balancer AKS.

Per altre informazioni sugli SKU *Basic* e *Standard*, vedere [Confronto tra SKU di Azure Load Balancer][azure-lb-comparison].

Questo articolo presuppone la disponibilità di un cluster del servizio AzureKs con lo SKU *Standard* Azure Load Balancer e illustra come usare e configurare alcune funzionalità del servizio di bilanciamento del carico. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Se si preferisce non sfruttare il Azure Load Balancer per fornire una connessione in uscita e si dispone invece di un gateway, un firewall o un proxy personalizzato a tale scopo, è possibile ignorare la creazione del pool in uscita del servizio di bilanciamento del carico e del rispettivo IP front-end usando Il tipo in uscita [**come UserDefinedRouting (UDR).**](egress-outboundtype.md) Il tipo Outbound definisce il metodo in uscita per un cluster e il valore predefinito è load balancer.

## <a name="use-the-public-standard-load-balancer"></a>Usare il servizio di bilanciamento del carico standard pubblico

Dopo aver creato un cluster servizio AzureKs con tipo in uscita: Load Balancer (impostazione predefinita), il cluster è pronto per usare il servizio di bilanciamento del carico per esporre anche i servizi.

A tale fine è possibile creare un servizio pubblico di `LoadBalancer` tipo , come illustrato nell'esempio seguente. Per iniziare, creare un manifesto del servizio denominato `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Distribuire il manifesto del servizio pubblico usando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

La Azure Load Balancer verrà configurata con un nuovo indirizzo IP pubblico che verrà anteriore a questo nuovo servizio. Poiché il Azure Load Balancer può avere più indirizzi IP front-end, ogni nuovo servizio distribuito otterrà un nuovo INDIRIZZO IP front-end dedicato a cui accedere in modo univoco.

È possibile verificare che il servizio sia stato creato e che il servizio di bilanciamento del carico sia configurato eseguendo ad esempio:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Quando si visualizzano i dettagli del servizio, l'indirizzo IP pubblico creato per questo servizio nel servizio di bilanciamento del carico viene visualizzato nella *colonna EXTERNAL-IP.* L'indirizzo IP può richiedere uno o due minuti per passare da a un indirizzo IP pubblico effettivo, come *\<pending\>* illustrato nell'esempio precedente.

## <a name="configure-the-public-standard-load-balancer"></a>Configurare il servizio di bilanciamento del carico standard pubblico

Quando si usa il servizio di bilanciamento del carico pubblico SKU Standard, è presente un set di opzioni che possono essere personalizzate in fase di creazione o aggiornando il cluster. Queste opzioni consentono di personalizzare l'Load Balancer in base alle esigenze dei carichi di lavoro e devono essere esaminate di conseguenza. Con il servizio di bilanciamento del carico Standard è possibile:

* Impostare o ridimensionare il numero di indirizzi IP in uscita gestiti
* Porta gli indirizzi IP [in uscita personalizzati o il prefisso IP in uscita](#provide-your-own-outbound-public-ips-or-prefixes)
* Personalizzare il numero di porte in uscita allocate per ogni nodo del cluster
* Configurare l'impostazione di timeout per le connessioni inattive

> [!IMPORTANT]
> In un determinato momento è possibile usare solo un'opzione IP in uscita (INDIRIZZI IP gestiti, Bring Your Own Ip o Prefisso IP).

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Ridimensionare il numero di indirizzi IP pubblici in uscita gestiti

Azure Load Balancer offre connettività in uscita da una rete virtuale oltre alla connettività in ingresso. Le regole in uscita semplificano la configurazione della conversione degli indirizzi di rete in uscita di Load Balancer Standard pubblico.

Come tutte le regole di Load Balancer, le regole in uscita seguono la stessa sintassi familiare delle regole di bilanciamento del carico e delle regole NAT in ingresso:

***INDIRIZZI IP front-end + parametri + pool back-end***

Una regola in uscita configura NAT in uscita per tutte le macchine virtuali identificate dal pool back-end da convertire in front-end. E i parametri offrono ulteriore controllo dettagliato sull'algoritmo NAT in uscita.

Anche se una regola in uscita può essere usata con un singolo indirizzo IP pubblico, le regole in uscita semplificano l'impegno di configurazione per la scalabilità di NAT in uscita. È possibile usare più indirizzi IP per gestire scenari su larga scala e le regole in uscita per risolvere i problemi dei modelli soggetti a esaurimento delle porte SNAT. Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce 64.000 porte effimere per Load Balancer da usare come porte SNAT. 

Quando si usa un servizio di bilanciamento del carico con SKU *Standard* con indirizzi IP pubblici in uscita gestiti, creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il **`load-balancer-managed-ip-count`** parametro .

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster, per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

L'esempio precedente imposta il numero di indirizzi IP pubblici in uscita gestiti su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

È anche possibile usare il parametro per impostare il numero iniziale di indirizzi IP pubblici in uscita gestiti durante la creazione del cluster aggiungendo il parametro e impostandolo **`load-balancer-managed-ip-count`** **`--load-balancer-managed-outbound-ip-count`** sul valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Specificare gli indirizzi IP o i prefissi pubblici in uscita

Quando si usa un servizio di bilanciamento del carico con SKU *Standard,* per impostazione predefinita il cluster del servizio AzureKs crea automaticamente un indirizzo IP pubblico nel gruppo di risorse dell'infrastruttura gestita dal servizio AzureKs e lo assegna al pool in uscita del servizio di bilanciamento del carico.

Un indirizzo IP pubblico creato dal servizio Web Diaks viene considerato una risorsa gestita del servizio Web AKS. Ciò significa che il ciclo di vita di tale indirizzo IP pubblico è destinato a essere gestito dal servizio AKS e non richiede alcuna azione dell'utente direttamente nella risorsa IP pubblico. In alternativa, è possibile assegnare un indirizzo IP pubblico personalizzato o un prefisso IP pubblico al momento della creazione del cluster. Gli indirizzi IP personalizzati possono essere aggiornati anche nelle proprietà del servizio di bilanciamento del carico di un cluster esistente.

Requisiti per l'uso del proprio indirizzo IP pubblico o prefisso:

- Gli indirizzi IP pubblici personalizzati devono essere creati e di proprietà dell'utente. Gli indirizzi IP pubblici gestiti creati dal servizio Diaks non possono essere riutilizzati come indirizzi IP personalizzati bring your own perché possono causare conflitti di gestione.
- È necessario assicurarsi che l'identità del cluster del servizio Web Dia (entità servizio o identità gestita) abbia le autorizzazioni per accedere all'indirizzo IP in uscita. In base [all'elenco di autorizzazioni IP pubblico necessarie,](kubernetes-service-principal.md#networking)
- Assicurarsi di soddisfare i [prerequisiti e](../virtual-network/public-ip-address-prefix.md#constraints) i vincoli necessari per configurare gli indirizzi IP in uscita o i prefissi IP in uscita.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Aggiornare il cluster con il proprio indirizzo IP pubblico in uscita

Usare il comando [az network public-ip show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID dell'indirizzo IP pubblico *myPublicIP* nel gruppo di risorse *myResourceGroup*.

Usare il `az aks update` comando con il parametro per aggiornare il cluster con gli IP **`load-balancer-outbound-ips`** pubblici.

Nell'esempio seguente viene `load-balancer-outbound-ips` utilizzato il parametro con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Aggiornare il cluster con il prefisso IP pubblico in uscita

È anche possibile usare i prefissi degli indirizzi IP pubblici per l'uscita con l'istanza di bilanciamento del carico con SKU *Standard*. L'esempio seguente usa il comando [network public-ip prefix show][az-network-public-ip-prefix-show] per elencare gli ID dei prefissi degli indirizzi IP pubblici:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Il comando precedente mostra l'ID del prefisso dell'indirizzo IP pubblico *myPublicIPPrefix* nel gruppo di risorse *myResourceGroup*.

L'esempio seguente usa il parametro *load-balancer-outbound-ip-prefixes* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Creare il cluster con il proprio indirizzo IP pubblico o i prefissi

È possibile portare i propri indirizzi IP o prefissi IP per l'uscita in fase di creazione del cluster per supportare scenari come l'aggiunta di endpoint in uscita a un elenco di indirizzi consentiti. Aggiungere gli stessi parametri illustrati in precedenza al passaggio di creazione del cluster per definire gli indirizzi IP pubblici e i prefissi degli indirizzi IP all'inizio del ciclo di vita di un cluster.

Per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ips*.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Per creare un nuovo cluster con i prefissi degli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ip-prefixes*.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Configurare le porte in uscita allocate

> [!IMPORTANT]
> Se nel cluster sono presenti applicazioni che dovrebbero stabilire un numero elevato di connessioni a un piccolo set di destinazioni, ad esempio . molte istanze front-end che si connettono a un database SQL, si ha uno scenario molto soggetto all'esaurimento della porta SNAT (esaurimento delle porte da cui connettersi). Per questi scenari è consigliabile aumentare le porte in uscita allocate e gli INDIRIZZI IP front-end in uscita nel servizio di bilanciamento del carico. L'aumento deve considerare che un (1) indirizzo IP aggiuntivo aggiunge 64.000 porte aggiuntive da distribuire in tutti i nodi del cluster.


Se non diversamente specificato, il servizio AKS userà il valore predefinito Porte in uscita allocate che Load Balancer Standard definisce durante la configurazione. Questo valore è **Null nell'API** del servizio Gateway Gateway o **0** nell'API SLB, come illustrato dal comando seguente:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

I comandi precedenti elencano la regola in uscita per l'istanza di bilanciamento del carico, ad esempio:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Questo output non significa che si dispone di 0 porte, ma che si sta sfruttando l'assegnazione automatica delle porte in uscita in base alle dimensioni del [pool back-end,][azure-lb-outbound-preallocatedports]quindi, se un cluster ha 50 o meno nodi, vengono allocate 1024 porte per ogni nodo, aumentando il numero di nodi da qui si otterrà gradualmente un numero inferiore di porte per nodo.


Per definire o aumentare il numero di porte in uscita allocate, è possibile seguire l'esempio seguente:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

In questo esempio vengono assegnate 4000 porte in uscita allocate per ogni nodo del cluster e con 7 indirizzi IP si hanno *4000 porte per nodo * 100 nodi = 400.000 porte totali < = 448.000 porte totali = 7 INDIRIZZI * 64.000* porte per IP . In questo modo è possibile ridimensionare in modo sicuro fino a 100 nodi e avere un'operazione di aggiornamento predefinita. È fondamentale allocare porte sufficienti per i nodi aggiuntivi necessari per l'aggiornamento e altre operazioni. Per impostazione predefinita, il servizio AKS è un nodo buffer per l'aggiornamento. In questo esempio sono necessarie 4000 porte disponibili in qualsiasi momento. Se si [usano i valori maxSurge](upgrade-cluster.md#customize-node-surge-upgrade), moltiplicare le porte in uscita per ogni nodo per il valore maxSurge.

Per passare in modo sicuro oltre i 100 nodi, è necessario aggiungere altri IP.


> [!IMPORTANT]
> È necessario [calcolare la quota necessaria e verificare i requisiti prima][requirements] di personalizzare *allocatedOutboundPorts* per evitare problemi di connettività o ridimensionamento.

È anche possibile usare i parametri durante la creazione di un cluster, ma è necessario **`load-balancer-outbound-ports`** specificare anche , o **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** .  Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Configurare il timeout di inattività del servizio di bilanciamento del carico

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Load Balancer recupera le porte SNAT quando il flusso si chiude e il servizio di bilanciamento del carico configurato dal servizio Azure Load Balancer usa un timeout di inattività di 30 minuti per recuperare le porte SNAT dai flussi inattivi.
È anche possibile usare il trasporto (ad esempio , ) o per aggiornare un flusso **`TCP keepalives`** **`application-layer keepalives`** inattivo e reimpostare questo timeout di inattività, se necessario. È possibile configurare questo timeout seguendo l'esempio seguente: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Se si prevede di avere numerose connessioni di breve durata e nessuna connessione di lunga durata e che potrebbe avere lunghi periodi di inattività, ad esempio sfruttando o valutando l'uso di un valore di timeout basso, ad esempio `kubectl proxy` `kubectl port-forward` 4 minuti. Inoltre, quando si usano keep-alias TCP, è sufficiente abilitarli su un lato della connessione. Ad esempio, è sufficiente abilitarli sul lato server solo per reimpostare il timer di inattività del flusso e non è necessario che entrambi i lati inizino i keep-alias TCP. Si applicano concetti simili anche per il livello dell'applicazione, tra cui le configurazioni client-server di database. Verificare sul lato server quali opzioni esistono per i keep-alias specifici dell'applicazione.

> [!IMPORTANT]
> Il servizio AKS abilita la reimpostazione TCP in caso di inattività per impostazione predefinita e consiglia di mantenere attivata questa configurazione e di sfruttarla per un comportamento dell'applicazione più prevedibile negli scenari.
> L'RST TCP viene inviato solo durante la connessione TCP in stato ESTABLISHED. Per altre informazioni, leggere [qui](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Requisiti per la personalizzazione delle porte in uscita allocate e del timeout di inattività

- Il valore specificato per *allocatedOutboundPorts* deve essere anche un multiplo di 8.
- È necessario disporre di capacità IP in uscita sufficiente in base al numero di macchine virtuali del nodo e alle porte in uscita allocate necessarie. Per verificare di disporre di una capacità IP in uscita sufficiente, usare la formula seguente: 
 
*Indirizzi IP in uscita* \* 64.000 \> *macchine virtuali del nodo* \* *desiredAllocatedOutboundPorts*.
 
Se, ad esempio, si dispone di 3 *macchine virtuali del nodo* e di 50.000 *desiredAllocatedOutboundPorts*, è necessario disporre di almeno 3 *indirizzi IP in uscita*. Si consiglia di incorporare una capacità IP in uscita aggiuntiva oltre quella necessaria. Inoltre, è necessario tenere presente il ridimensionamento automatico del cluster e la possibilità di aggiornamenti del pool di nodi quando si calcola la capacità IP in uscita. Per il ridimensionamento automatico del cluster, esaminare il numero di nodi corrente e il numero massimo di nodi e usare il valore più elevato. Per l'aggiornamento, tenere conto di una macchina virtuale del nodo aggiuntiva per ogni pool di nodi che consente l'aggiornamento.

- Quando si imposta *IdleTimeoutInMinutes* su un valore diverso da quello predefinito di 30 minuti, considerare per quanto tempo i carichi di lavoro necessiteranno di una connessione in uscita. Tenere presente anche che il valore di timeout predefinito per un'istanza di bilanciamento del carico con SKU *Standard* usata al di fuori del servizio Azure Kubernetes è di 4 minuti. Un valore di *IdleTimeoutInMinutes* che riflette in modo più accurato il carico di lavoro del servizio Azure Kubernetes specifico può contribuire a ridurre l'esaurimento SNAT causato dal collegamento di connessioni non più utilizzate.

> [!WARNING]
> La modifica dei valori per *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* può modificare in modo significativo il comportamento della regola in uscita per il servizio di bilanciamento del carico e non deve essere eseguita con leggerezza, senza comprendere i compromessi e i modelli di connessione dell'applicazione, vedere la sezione risoluzione dei problemi [SNAT][troubleshoot-snat] riportata di seguito ed esaminare le regole in uscita di [Load Balancer][azure-lb-outbound-rules-overview] e le connessioni [in uscita in Azure][azure-lb-outbound-connections] prima di aggiornare questi valori per comprendere completamente l'impatto delle modifiche.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Limitare il traffico in ingresso a intervalli IP specifici

Il manifesto seguente usa *loadBalancerSourceRanges* per specificare un nuovo intervallo di indirizzi IP per il traffico esterno in ingresso:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Il traffico esterno in ingresso passa dal servizio di bilanciamento del carico alla rete virtuale per il cluster del servizio AzureKs. La rete virtuale ha un gruppo di sicurezza di rete (NSG) che consente tutto il traffico in ingresso dal servizio di bilanciamento del carico. Questo gruppo di sicurezza di rete usa un [tag di servizio][service-tags] di tipo *LoadBalancer* per consentire il traffico dal servizio di bilanciamento del carico.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Gestire l'indirizzo IP del client nelle connessioni in ingresso

Per impostazione predefinita, un servizio di tipo `LoadBalancer` [in Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) e nel servizio Kubernetes non rende persistente l'indirizzo IP del client nella connessione al pod. L'INDIRIZZO IP di origine nel pacchetto recapitato al pod sarà l'INDIRIZZO IP privato del nodo. Per mantenere l'indirizzo IP del client, è necessario impostare `service.spec.externalTrafficPolicy` su nella definizione del `local` servizio. Il manifesto seguente mostra un esempio:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Personalizzazioni aggiuntive tramite annotazioni Kubernetes

Di seguito è riportato un elenco di annotazioni supportate per i servizi Kubernetes con tipo , queste `LoadBalancer` annotazioni si applicano solo ai **flussi INBOUND:**

| Annotazione | Valore | Descrizione
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` o `false`                     | Specificare se il servizio di bilanciamento del carico deve essere interno. Se non è impostata, il valore predefinito è public.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nome della subnet                    | Specificare la subnet a cui deve essere associato il servizio di bilanciamento del carico interno. Se non è impostata, per impostazione predefinita viene impostata la subnet configurata nel file di configurazione cloud.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nome dell'etichetta DNS sugli indirizzi IP pubblici   | Specificare il nome dell'etichetta DNS per **il servizio** pubblico. Se è impostata su una stringa vuota, la voce DNS nell'indirizzo IP pubblico non verrà usata.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` o `false`                     | Specificare che il servizio deve essere esposto usando una regola di sicurezza di Azure che può essere condivisa con un altro servizio, scambiando la specificità delle regole per un aumento del numero di servizi che possono essere esposti. Questa annotazione si basa sulla funzionalità Regole [di sicurezza aumentata di](../virtual-network/network-security-groups-overview.md#augmented-security-rules) Azure dei gruppi di sicurezza di rete. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nome del gruppo di risorse            | Specificare il gruppo di risorse di indirizzi IP pubblici del servizio di bilanciamento del carico che non sono nello stesso gruppo di risorse dell'infrastruttura del cluster (gruppo di risorse del nodo).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Elenco dei tag di servizio consentiti          | Specificare un elenco di tag [di servizio consentiti][service-tags] separati da virgola.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Timeout di inattività TCP in minuti          | Specificare il tempo, in minuti, per il timeout di inattività della connessione TCP nel servizio di bilanciamento del carico. Il valore predefinito e il valore minimo sono 4. Il valore massimo è 30. Deve essere un valore Integer.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Disabilitare `enableTcpReset` per SLB


## <a name="troubleshooting-snat"></a>Risoluzione dei problemi relativi a SNAT

Se si è a sapere che si stanno avviando molte connessioni TCP o UDP in uscita allo stesso indirizzo IP e alla stessa porta di destinazione e si osservano connessioni in uscita non riuscite o si è avvisati dal supporto che si stanno esaurendo le porte SNAT (porte effimeri preallocate usate da PAT), sono disponibili diverse opzioni di mitigazione generali. Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico. È possibile che una o più opzioni risultino utili per gestire questo scenario. Per informazioni dettagliate, vedere la Guida alla risoluzione dei [problemi relativi alle connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md).

Spesso la causa radice dell'esaurimento SNAT è un anti-modello per il modo in cui la connettività in uscita viene stabilita e gestita oppure per come vengono cambiati i timer configurabili rispetto ai valori predefiniti. Leggere attentamente questa sezione.

### <a name="steps"></a>Passaggi
1. Controllare se le connessioni rimangono inattive per molto tempo e fare affidamento sul timeout di inattività predefinito per il rilascio di tale porta. In tal caso, potrebbe essere necessario ridurre il timeout predefinito di 30 minuti per lo scenario.
2. Esaminare il modo in cui l'applicazione crea la connettività in uscita, ad esempio, revisione del codice o acquisizione di pacchetti.
3. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente. Usare [metriche](../load-balancer/load-balancer-standard-diagnostics.md) [e log](../load-balancer/load-balancer-monitor-log.md) in Monitoraggio di Azure per creare un'istanza dei risultati. Ad esempio, usare la categoria "Non riuscito" per la metrica Connessioni SNAT.
4. Valutare se vengono [seguiti i](#design-patterns) modelli appropriati.
5. Valutare se l'esaurimento delle porte SNAT deve essere mitigato con indirizzi IP in uscita aggiuntivi + porte in uscita [allocate aggiuntive.](#configure-the-allocated-outbound-ports)

### <a name="design-patterns"></a>Modelli di progettazione
Quando possibile, sfruttare sempre i vantaggi del riutilizzo delle connessioni e del pool di connessioni. Questi modelli eviteranno problemi di esaurimento delle risorse e genereranno un comportamento prevedibile. Le primitive per questi modelli sono reperibili in molti framework e librerie di sviluppo.

- Le richieste atomiche (una richiesta per ogni connessione) in genere non sono una buona scelta di progettazione. Questo anti-modello limita la scalabilità, riduce le prestazioni e diminuisce l'affidabilità. Riutilizzare invece le connessioni HTTP/S per ridurre il numero di connessioni e delle porte SNAT associate. La scalabilità dell'applicazione aumenterà e le prestazioni miglioreranno a causa di handshake, sovraccarico e costi delle operazioni di crittografia ridotti quando si usa TLS.
- Se si usa un DNS esterno al cluster o personalizzato o server upstream personalizzati in coreDNS, DNS può introdurre molti singoli flussi nel volume quando il client non memorizza nella cache i risultati dei resolver DNS. Assicurarsi di personalizzare coreDNS prima di usare server DNS personalizzati e definire un valore di memorizzazione nella cache valido.
- I flussi UDP, ad esempio le ricerche DNS, allocano le porte SNAT per la durata del timeout di inattività. Più è lungo il timeout di inattività, maggiore è la pressione sulle porte SNAT. Usare un timeout di inattività breve (ad esempio 4 minuti).
Usare i pool di connessioni per definire il volume di connessioni.
- Non abbandonare mai un flusso TCP e non fare affidamento sui timer TCP per pulire il flusso. Se non si lascia che TCP chiuda in modo esplicito la connessione, lo stato rimane allocato in endpoint e sistemi intermedi e rende le porte SNAT non disponibili per altre connessioni. Questo schema può generare errori dell'applicazione e l'esaurimento SNAT.
- Non cambiare i valori del timer correlati alla chiusura TCP a livello di sistema operativo se non se ne conosce perfettamente l'impatto. Mentre lo stack TCP verrà ripristinato, le prestazioni dell'applicazione possono essere influenzate negativamente quando gli endpoint di una connessione hanno aspettative non corrispondenti. La modifica dei timer è in genere un segno di un problema di progettazione sottostante. Esaminare le raccomandazioni seguenti.


L'esempio precedente aggiorna la regola in modo da consentire solo il traffico esterno in ingresso dall'intervallo *MY_EXTERNAL_IP_RANGE*. Se si sostituisce *MY_EXTERNAL_IP_RANGE* con l'indirizzo IP della subnet interna, il traffico è limitato solo agli indirizzi IP interni del cluster. In questo modo i client esterni al cluster Kubernetes non potranno accedere al servizio di bilanciamento del carico.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da un servizio di bilanciamento del carico SKU di base a uno SKU standard

Se si dispone di un cluster esistente con il servizio Load Balancer con SKU Basic, quando si esegue la migrazione per l'uso di un cluster con il servizio Load Balancer con SKU Standard, è necessario tenere presenti alcune importanti differenze di comportamento.

Ad esempio, l'esecuzione di distribuzioni blu/verde per la migrazione dei cluster è una pratica comune, dato che il tipo di `load-balancer-sku` di un cluster può essere definito solo in fase di creazione del cluster. Tuttavia, *i servizi di bilanciamento* del carico SKU basici usano indirizzi IP *SKU* di base, che non sono compatibili con i servizi di bilanciamento del carico *SKU Standard* perché richiedono indirizzi IP *SKU Standard.* Quando si esegue la migrazione dei cluster per aggiornare gli SKU di Load Balancer, sarà necessario un nuovo indirizzo IP con uno SKU compatibile per l'indirizzo IP.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere la [documentazione sulle considerazioni relative alla migrazione](aks-migration.md), in modo da consultare un elenco di argomenti importanti da tenere presenti durante la migrazione. Le limitazioni seguenti includono differenze comportamentali importanti da tenere presenti quando si usano istanze di Load Balancer con SKU Standard nel servizio Azure Kubernetes.

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano un'istanza di bilanciamento del carico con SKU *Standard*, si applicano le limitazioni seguenti:

* È necessario almeno un indirizzo IP pubblico o un prefisso dell'indirizzo IP per consentire il traffico in uscita dal cluster del servizio Azure Kubernetes. L'indirizzo IP pubblico o il prefisso IP è necessario anche per mantenere la connettività tra il piano di controllo e i nodi dell'agente e per mantenere la compatibilità con le versioni precedenti del servizio AKS. Per specificare gli indirizzi IP pubblici o i prefissi degli indirizzi IP con un servizio di bilanciamento del carico con SKU *Standard*, sono disponibili le opzioni seguenti:
    * Specificare gli indirizzi IP pubblici personalizzati.
    * Specificare i prefissi degli indirizzi IP pubblici personalizzati.
    * Specificare un numero massimo di 100 per consentire al cluster del servizio Azure Kubernetes di creare una certa quantità di indirizzi IP pubblici dello SKU *Standard* nello stesso gruppo di risorse creato dal cluster del servizio Azure Kubernetes, che in genere viene denominato con *MC_* all'inizio. Il servizio Azure Kubernetes assegna l'indirizzo IP pubblico all'istanza di bilanciamento del carico con SKU *Standard*. Per impostazione predefinita, un indirizzo IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster del servizio Azure Kubernetes, se non è specificato alcun indirizzo IP pubblico, prefisso di indirizzo IP pubblico o numero di indirizzi IP. È necessario anche consentire gli indirizzi pubblici ed evitare di creare criteri di Azure che vietino la creazione di IP.
* Non è possibile riutilizzare un indirizzo IP pubblico creato dal servizio AKS come indirizzo IP pubblico personalizzato. Tutti gli indirizzi IP personalizzati devono essere creati e gestiti dall'utente.
* La definizione dello SKU per un servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster del servizio Azure Kubernetes. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo la creazione di un cluster del servizio AzureKs.
* È possibile usare un solo tipo di SKU dell'istanza di bilanciamento del carico (Basic o Standard) in un singolo cluster.
* Le istanze di bilanciamento del carico con SKU *Standard* supportano solo indirizzi IP con SKU *Standard*.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi Kubernetes sono disponibili nella [documentazione relativa ai servizi Kubernetes][kubernetes-services].

Per altre informazioni sull'uso dei Load Balancer per il traffico in ingresso, vedere la documentazione del Load Balancer del Load Balancer [AKS.](internal-lb.md)

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
