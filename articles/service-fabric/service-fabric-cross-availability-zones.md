---
title: Distribuire un cluster tra zone di disponibilità
description: Informazioni su come creare un cluster di Service Fabric di Azure tra zone di disponibilità.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 82161a8f66dd717a9dc448a743b818a9ab9938db
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250979"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuire un cluster di Azure Service Fabric tra zone di disponibilità
Zone di disponibilità in Azure è un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Una zona di disponibilità è una posizione fisica univoca dotata di alimentazione, raffreddamento e rete indipendenti in un'area di Azure.

Service Fabric supporta i cluster che si estendono tra zone di disponibilità distribuendo i tipi di nodo aggiunti a zone specifiche. In questo modo si garantisce la disponibilità elevata delle applicazioni. Zone di disponibilità di Azure sono disponibili solo in aree selezionate. Per ulteriori informazioni, vedere [zone di disponibilità di Azure Overview](../availability-zones/az-overview.md).

Sono disponibili modelli di esempio: [Service Fabric modello di zona a disponibilità incrociata](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia consigliata per il tipo di nodo primario dei cluster Service Fabric di Azure che si estendono tra zone di disponibilità
Un cluster Service Fabric distribuito tra zone di disponibilità garantisce la disponibilità elevata dello stato del cluster. Per estendere un cluster Service Fabric tra le zone, è necessario creare un tipo di nodo primario in ogni zona di disponibilità supportata dall'area. In questo modo i nodi di inizializzazione vengono distribuiti in modo uniforme in ogni tipo di nodo primario.

Per la topologia consigliata per il tipo di nodo primario sono necessarie le risorse descritte di seguito:

* Livello di affidabilità del cluster impostato su platino.
* Tre tipi di nodo contrassegnati come primari.
    * È necessario eseguire il mapping di ogni tipo di nodo al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (durabilità Silver).
* Una singola risorsa IP pubblico con SKU standard.
* Una singola risorsa Load Balancer usando lo SKU standard.
* Un NSG a cui fa riferimento la subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.

>[!NOTE]
> Il set di scalabilità di macchine virtuali proprietà del gruppo di posizionamento singolo deve essere impostato su true, poiché Service Fabric non supporta un singolo set di scalabilità di macchine virtuali che si estende su zone.

 ![Diagramma che illustra l'architettura della zona di disponibilità di Azure Service Fabric.][sf-architecture]

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>IP pubblico e risorsa Load Balancer
Per abilitare la proprietà zone in una risorsa del set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento il set di scalabilità di macchine virtuali devono entrambi usare uno SKU *standard* . La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU di base che non supporta zone di disponibilità. Per impostazione predefinita, un servizio di bilanciamento del carico con SKU standard bloccherà tutto il traffico dall'esterno. per consentire il traffico esterno, una NSG deve essere distribuita nella subnet.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Non è possibile eseguire una modifica sul posto dello SKU nell'IP pubblico e nelle risorse del servizio di bilanciamento del carico. Se si esegue la migrazione da risorse esistenti con SKU Basic, vedere la sezione relativa alla migrazione di questo articolo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regole NAT del set di scalabilità di macchine virtuali
Le regole NAT in ingresso del servizio di bilanciamento del carico devono corrispondere ai pool NAT del set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali deve avere un pool NAT in ingresso univoco.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>SKU standard Load Balancer regole in uscita
Load Balancer Standard e l'IP pubblico standard introducono nuove funzionalità e comportamenti diversi per la connettività in uscita rispetto all'uso di SKU di base. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md) e [Load Balancer standard di Azure](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Il modello standard fa riferimento a un NSG che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte richieste per Service Fabric operazioni di gestione. Le regole NSG possono essere modificate per soddisfare i requisiti.

>[!NOTE]
> Qualsiasi Service Fabric cluster che usa uno SKU standard SLB deve garantire che ogni tipo di nodo abbia una regola che consenta il traffico in uscita sulla porta 443. Questa operazione è necessaria per completare la configurazione del cluster e tutte le distribuzioni senza tale regola avranno esito negativo.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Abilitazione di zone in un set di scalabilità di macchine virtuali
Per abilitare una zona, in un set di scalabilità di macchine virtuali è necessario includere i tre valori seguenti nella risorsa del set di scalabilità di macchine virtuali.

* Il primo valore è la proprietà **Zones** , che specifica la zona di disponibilità in cui verrà distribuito il set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true.
* Il terzo valore è la proprietà "faultDomainOverride" nell'estensione del set di scalabilità di macchine virtuali Service Fabric. Il valore di questa proprietà deve includere solo l'area in cui verrà inserito il set di scalabilità di macchine virtuali. Esempio: "faultDomainOverride": "AZ1" tutte le risorse del set di scalabilità di macchine virtuali devono essere posizionate nella stessa area perché i cluster di Service Fabric di Azure non hanno supporto tra aree.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Abilitazione di più tipi di nodo primari nella risorsa cluster Service Fabric
Per impostare uno o più tipi di nodo come primari in una risorsa cluster, impostare la proprietà "Primary" su "true". Quando si distribuisce un cluster Service Fabric tra zone di disponibilità, è necessario avere tre tipi di nodo in zone distinte.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Eseguire la migrazione a usando zone di disponibilità da un cluster usando uno SKU di base Load Balancer e un IP dello SKU Basic
Per eseguire la migrazione di un cluster che stava usando un Load Balancer e un IP con uno SKU di base, è necessario creare prima di tutto una nuova Load Balancer e una risorsa IP usando lo SKU standard. Non è possibile aggiornare queste risorse sul posto.

È necessario fare riferimento ai nuovi LB e IP nei nuovi tipi di nodo della zona di disponibilità incrociata che si vuole usare. Nell'esempio precedente sono state aggiunte tre nuove risorse del set di scalabilità di macchine virtuali nelle zone 1, 2 e 3. Questi set di scalabilità di macchine virtuali fanno riferimento a LB e IP appena creati e sono contrassegnati come tipi di nodo primari nella risorsa del cluster Service Fabric.

Per iniziare, sarà necessario aggiungere le nuove risorse al modello di Gestione risorse esistente. Tali risorse includono:
* Una risorsa IP pubblico con SKU standard.
* Una risorsa Load Balancer usando lo SKU standard.
* Un NSG a cui fa riferimento la subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.
* Tre tipi di nodo contrassegnati come primari.
    * È necessario eseguire il mapping di ogni tipo di nodo al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (durabilità Silver).

Un esempio di queste risorse è disponibile nel modello di [esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Una volta terminata la distribuzione delle risorse, è possibile iniziare a disabilitare i nodi del tipo di nodo primario dal cluster originale. Quando i nodi sono disabilitati, viene eseguita la migrazione dei servizi di sistema al nuovo tipo di nodo primario distribuito nel passaggio precedente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Una volta disabilitati tutti i nodi, i servizi di sistema verranno eseguiti nel tipo di nodo primario, che viene distribuito tra le zone. È quindi possibile rimuovere i nodi disabilitati dal cluster. Una volta rimossi i nodi, è possibile rimuovere le risorse dell'IP originale, della Load Balancer e del set di scalabilità di macchine virtuali.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

È quindi necessario rimuovere i riferimenti a queste risorse dal modello di Gestione risorse distribuito.

Il passaggio finale prevede l'aggiornamento del nome DNS e dell'indirizzo IP pubblico.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Anteprima Abilitare più zone di disponibilità in un singolo set di scalabilità di macchine virtuali

La soluzione indicata in precedenza usa un nodeType per AZ. La soluzione seguente consente agli utenti di distribuire 3 AZ nello stesso nodeType.

Il modello di esempio completo è disponibile [qui](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure).

![Architettura della zona di disponibilità di Azure Service Fabric][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Configurazione di zone in un set di scalabilità di macchine virtuali
Per abilitare le zone in un set di scalabilità di macchine virtuali, è necessario includere i tre valori seguenti nella risorsa del set di scalabilità di macchine virtuali.

* Il primo valore è la proprietà **Zones** , che specifica il zone di disponibilità presente nel set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true. **Il set di scalabilità in 3 AZ può essere ridimensionato fino a 300 VM anche con "singlePlacementGroup = true".**
* Il terzo valore è "zoneBalance", che garantisce un rigido bilanciamento della zona se impostato su true. È consigliabile impostare questo valore su true per evitare la distribuzione sbilanciata delle macchine virtuali tra le zone. Vedere informazioni su [zoneBalancing](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing).
* Non è necessario configurare gli override di FaultDomain e UpgradeDomain.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **I cluster SF dovrebbero avere almeno un nodeType primario. Il durabilità della nodeTypes primaria deve essere Silver o superiore.**
> * Il set di scalabilità AZ spanning di macchine virtuali deve essere configurato con almeno 3 zone di disponibilità indipendentemente da durabilità.
> * AZ spanning set di scalabilità di macchine virtuali con durabilità Silver (o versione successiva) deve avere almeno 15 VM.
> * AZ spanning set di scalabilità di macchine virtuali con durabilità Bronze deve avere almeno 6 macchine virtuali.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Abilitazione del supporto per più zone nel Service Fabric nodeType
Per supportare più zone di disponibilità, è necessario abilitare il nodeType Service Fabric.

* Il primo valore è **multipleAvailabilityZones** che deve essere impostato su true per NodeType.
* Il secondo valore è **sfZonalUpgradeMode** ed è facoltativo. Questa proprietà non può essere modificata se nel cluster è già presente un oggetto NodeType con più AZ.
      La proprietà controlla il raggruppamento logico di macchine virtuali in domini di aggiornamento.
          Se il valore è impostato su false (modalità flat): le VM sotto il tipo di nodo verranno raggruppate in UD ignorando le informazioni sulla zona in 5 UDs.
          Se il valore viene omesso o è impostato su true (modalità gerarchica): le macchine virtuali verranno raggruppate per riflettere la distribuzione di zona in un massimo di 15 domini di UDs. Ognuna delle 3 zone avrà 5 domini di stato.
          Questa proprietà definisce solo il comportamento di aggiornamento per l'applicazione ServiceFabric e gli aggiornamenti del codice. Gli aggiornamenti del set di scalabilità di macchine virtuali sottostanti saranno comunque paralleli in tutte le AZ.
      Questa proprietà non avrà alcun effetto sulla distribuzione di UD per i tipi di nodo per i quali non sono abilitate più zone.
* Il terzo valore è **vmssZonalUpgradeMode = Parallel**. Si tratta di una proprietà *obbligatoria* da configurare nel cluster, se viene aggiunto un NodeType con più AZs. Questa proprietà definisce la modalità di aggiornamento per gli aggiornamenti del set di scalabilità di macchine virtuali che si verificheranno in parallelo in tutte le AZ a una sola volta.
      Al momento questa proprietà può essere impostata solo su Parallel.
* La risorsa cluster Service Fabric apiVersion deve essere "2020-12-01-Preview" o una versione successiva.
* La versione del codice del cluster deve essere "7.2.445" o successiva.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * L'IP pubblico e le risorse di Load Balancer devono usare lo SKU standard come descritto in precedenza in questo articolo.
> * la proprietà "multipleAvailabilityZones" in nodeType può essere definita solo al momento della creazione di nodeType e non può essere modificata in un secondo momento. Di conseguenza, non è possibile configurare nodeTypes esistenti con questa proprietà.
> * Quando "sfZonalUpgradeMode" viene omesso o è impostato su "gerarchico", le distribuzioni del cluster e dell'applicazione saranno più lente perché sono presenti altri domini di aggiornamento nel cluster. È importante regolare correttamente i timeout dei criteri di aggiornamento da incorporare per la durata dell'aggiornamento per 15 domini di aggiornamento.
> * È consigliabile impostare il livello di affidabilità del cluster su platino per garantire che il cluster superi lo scenario di una zona inattiva.

>[!NOTE]
> Per la procedura consigliata, è consigliabile impostare sfZonalUpgradeMode su gerarchico o su omesso. La distribuzione seguirà la distribuzione di zona delle macchine virtuali che incidono su una minore quantità di repliche e/o istanze che li rendono più sicure.
> Usare sfZonalUpgradeMode impostato su Parallel se la velocità di distribuzione è una priorità o solo il carico di lavoro senza stato viene eseguito sul tipo di nodo con più AZ. Questa operazione comporterà il raggiungimento del percorso di UD in parallelo in tutte le AZ.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrazione al tipo di nodo con più zone di disponibilità
Per tutti gli scenari di migrazione, è necessario aggiungere un nuovo nodeType che avrà più zone di disponibilità supportate. Non è possibile eseguire la migrazione di un nodeType esistente per supportare più zone.
[Questo articolo illustra](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-primary-node-type ) i passaggi dettagliati per aggiungere un nuovo NodeType e aggiungere anche le altre risorse necessarie per il nuovo NodeType, ad esempio le risorse IP e lb. Lo stesso articolo descrive anche ora di ritirare il nodeType esistente dopo che il nodeType con più zone di disponibilità viene aggiunto al cluster.

* Migrazione da un nodeType che usa le risorse LB e IP di base: questa operazione è già descritta [qui](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) per la soluzione con un tipo di nodo per AZ. 
    Per il nuovo tipo di nodo, l'unica differenza è costituita dal fatto che esiste un solo set di scalabilità di macchine virtuali e 1 NodeType per tutte le AZ, anziché 1 ogni per AZ.
* Migrazione da un oggetto nodeType che usa le risorse LB e IP dello SKU standard con NSG: seguire la stessa procedura descritta in precedenza, con l'eccezione che non è necessario aggiungere nuove risorse LB, IP e NSG e le stesse risorse possono essere riutilizzate nel nuovo nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
