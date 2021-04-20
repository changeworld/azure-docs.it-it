---
title: Distribuire un cluster tra zone di disponibilità
description: Informazioni su come creare un cluster azure Service Fabric tra zone di disponibilità.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727763"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuire un cluster azure Service Fabric tra zone di disponibilità
zone di disponibilità in Azure è un'offerta a disponibilità elevata che protegge le applicazioni e i dati da errori del data center. Una zona di disponibilità è una posizione fisica univoca dotata di alimentazione, raffreddamento e rete indipendenti all'interno di un'area di Azure.

Service Fabric i cluster che si estendono tra zone di disponibilità distribuendo tipi di nodo aggiunti a zone specifiche. In questo modo si garantirà la disponibilità elevata delle applicazioni. zone di disponibilità di Azure sono disponibili solo in aree selezionate. Per altre informazioni, vedere zone di disponibilità di Azure [Panoramica di](../availability-zones/az-overview.md).

Sono disponibili modelli di esempio: Service Fabric [di zona di disponibilità incrociata](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia consigliata per il tipo di nodo primario dei cluster Service Fabric Azure che si estendono tra zone di disponibilità
Un Service Fabric cluster distribuito tra zone di disponibilità garantisce la disponibilità elevata dello stato del cluster. Per estendersi a Service Fabric cluster tra zone, è necessario creare un tipo di nodo primario in ogni zona di disponibilità supportata dall'area. In questo modo i nodi di seed vengono distribuiti in modo uniforme tra ogni tipo di nodo primario.

La topologia consigliata per il tipo di nodo primario richiede le risorse descritte di seguito:

* Livello di affidabilità del cluster impostato su Platinum.
* Tre tipi di nodo contrassegnati come primari.
    * Ogni tipo di nodo deve essere mappato al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (Durabilità Silver).
* Una singola risorsa IP pubblico che usa lo SKU Standard.
* Una risorsa Load Balancer singola che usa lo SKU Standard.
* Gruppo di scalabilità di rete a cui fa riferimento la subnet in cui si distribuiscono i set di scalabilità di macchine virtuali.

>[!NOTE]
> La proprietà gruppo di posizionamento singolo del set di scalabilità di macchine virtuali deve essere impostata su true.

Diagramma che illustra l'architettura della zona Service Fabric disponibilità di Azure che mostra ![ l'architettura della zona Service Fabric disponibilità di Azure.][sf-architecture]

Elenco di nodi di esempio che illustra i formati FD/UD in un set di scalabilità di macchine virtuali che si estende su più zone

 ![Elenco di nodi di esempio che illustra i formati FD/UD in un set di scalabilità di macchine virtuali che si estende su più zone.][sf-multi-az-nodes]

**Distribuzione di repliche del** servizio tra zone: quando un servizio viene distribuito nei nodeType che si estendono su più zone, le repliche vengono posizionate per assicurarsi che si atterrino in zone separate. Ciò è garantito dal fatto che il dominio di errore nei nodi presenti in ognuno di questi nodeType è configurato con le informazioni sulla zona ,ad esempio FD = fd:/zone1/1 e così via). Ad esempio: per 5 repliche o istanze di un servizio, la distribuzione sarà 2-2-1 e il runtime tenterà di garantire la stessa distribuzione tra le AZ.

**Configurazione della replica del** servizio utente: i servizi utente con stato distribuiti nella zona di disponibilità incrociata nodeTypes devono essere configurati con questa configurazione: numero di repliche con destinazione = 9, min = 5. Questa configurazione consente al servizio di funzionare anche quando una zona diventa inattesa perché 6 repliche saranno ancora in servizio nelle altre due zone. Verrà anche eseguito un aggiornamento dell'applicazione in uno scenario di questo tipo.

**ReliabilityLevel del** cluster: definisce il numero di nodi di seeding nel cluster e anche le dimensioni di replica dei servizi di sistema. Poiché una configurazione tra zone di disponibilità ha un numero maggiore di nodi distribuiti tra zone per consentire la resilienza della zona, un valore di affidabilità più elevato garantisce la disponibilità di nodi con più nodi di seeding e repliche del servizio di sistema e una distribuzione uniforme tra le zone, in modo che in caso di errore di zona il cluster e i servizi di sistema rimangano invariati. "ReliabilityLevel = Platinum" garantisce che siano presenti 9 nodi di semi distribuiti tra le zone del cluster con 3 semi in ogni zona, quindi è consigliabile per la configurazione tra zone di disponibilità.

**Scenario di zona verso** il basso: quando una zona diventa in calo, tutti i nodi in tale zona verranno visualizzati come in basso. Anche le repliche del servizio in questi nodi saranno inattese. Poiché sono presenti repliche nelle altre zone, il servizio continua a rispondere con il failover delle repliche primarie alle zone che funzionano. I servizi verranno visualizzati in stato di avviso perché il numero di repliche di destinazione non è ancora raggiunto e poiché il numero di macchine virtuali è ancora superiore alle dimensioni della replica di destinazione minima. Successivamente, Service Fabric servizio di bilanciamento del carico inserirà le repliche nelle zone di lavoro in modo che corrispondano al numero di repliche di destinazione configurato. A questo punto i servizi saranno integri. Quando la zona in cui si è verificata l'insoddizioni viene ripartita, il bilanciamento del carico ripartirà nuovamente tutte le repliche del servizio in modo uniforme in tutte le zone.

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>Ip pubblico e Load Balancer risorsa
Per abilitare la proprietà zones in una risorsa del set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento tale set di scalabilità di macchine virtuali devono entrambi usare uno SKU *Standard.* La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU Basic, che non supporta zone di disponibilità. Un servizio di bilanciamento del carico SKU Standard blocca tutto il traffico dall'esterno per impostazione predefinita. per consentire il traffico esterno, è necessario distribuire un gruppo di sicurezza di rete nella subnet.

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
> Non è possibile eseguire una modifica sul posto dello SKU nell'indirizzo IP pubblico e nelle risorse di bilanciamento del carico. Se si esegue la migrazione da risorse esistenti con uno SKU Basic, vedere la sezione relativa alla migrazione di questo articolo.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>SKU standard Load Balancer in uscita
Load Balancer Standard ip pubblico standard introducono nuove capacità e comportamenti diversi per la connettività in uscita rispetto all'uso di SKU Basic. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [Connessioni in uscita e](../load-balancer/load-balancer-outbound-connections.md) Azure [Load Balancer Standard](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Il modello standard fa riferimento a un gruppo di sicurezza di rete che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte necessarie per le Service Fabric di gestione. Le regole del gruppo di sicurezza di rete possono essere modificate per soddisfare i requisiti.

>[!NOTE]
> Qualsiasi cluster Service Fabric che usa un bilanciamento del carico SKU Standard deve assicurarsi che ogni tipo di nodo abbia una regola che consenta il traffico in uscita sulla porta 443. Questa operazione è necessaria per completare l'installazione del cluster e qualsiasi distribuzione senza tale regola avrà esito negativo.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Abilitazione di zone in un set di scalabilità di macchine virtuali
Per abilitare una zona, in un set di scalabilità di macchine virtuali è necessario includere i tre valori seguenti nella risorsa del set di scalabilità di macchine virtuali.

* Il primo valore è la proprietà **zones,** che specifica la zona di disponibilità in cui verrà distribuito il set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true.
* Il terzo valore è la proprietà "faultDomainOverride" nell'estensione Service Fabric set di scalabilità di macchine virtuali. Il valore di questa proprietà deve includere solo la zona in cui verrà inserito il set di scalabilità di macchine virtuali. Esempio: "faultDomainOverride": "az1" Tutte le risorse del set di scalabilità di macchine virtuali devono essere inserite nella stessa area perché i cluster di Azure Service Fabric non hanno il supporto per più aree.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Abilitazione di più tipi di nodo primari nella Service Fabric cluster
Per impostare uno o più tipi di nodo come primario in una risorsa cluster, impostare la proprietà "isPrimary" su "true". Quando si distribuisce un cluster Service Fabric tra zone di disponibilità, è necessario avere tre tipi di nodo in zone distinte.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Eseguire la migrazione all'zone di disponibilità da un cluster usando uno SKU Basic Load Balancer e un indirizzo IP dello SKU Basic
Per eseguire la migrazione di un cluster, che usava un Load Balancer e un indirizzo IP con uno SKU di base, è prima necessario creare una risorsa Load Balancer e IP completamente nuova usando lo SKU standard. Non è possibile aggiornare queste risorse sul posto.

È necessario fare riferimento ai nuovi tipi di nodo LB e IP nei nuovi tipi di nodo della zona di disponibilità che si vuole usare. Nell'esempio precedente sono state aggiunte tre nuove risorse del set di scalabilità di macchine virtuali nelle zone 1,2 e 3. Questi set di scalabilità di macchine virtuali fanno riferimento al bilanciamento del carico locale e all'indirizzo IP appena creati e sono contrassegnati come tipi di nodo primario nella risorsa Service Fabric cluster.

Per iniziare, è necessario aggiungere le nuove risorse al modello di Resource Manager esistente. Tali risorse includono:
* Una risorsa IP pubblico che usa lo SKU Standard.
* Una Load Balancer risorsa con SKU Standard.
* Gruppo di scalabilità di rete a cui fa riferimento la subnet in cui si distribuiscono i set di scalabilità di macchine virtuali.
* Tre tipi di nodo contrassegnati come primari.
    * Ogni tipo di nodo deve essere mappato al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (Durabilità Silver).

Un esempio di queste risorse è disponibile nel modello [di esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Al termine della distribuzione delle risorse, è possibile iniziare a disabilitare i nodi nel tipo di nodo primario dal cluster originale. Quando i nodi sono disabilitati, i servizi di sistema eseguiranno la migrazione al nuovo tipo di nodo primario distribuito nel passaggio precedente.

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

Dopo aver disabilitato tutti i nodi, i servizi di sistema verranno eseguiti nel tipo di nodo primario, che viene distribuito tra zone. È quindi possibile rimuovere i nodi disabilitati dal cluster. Dopo aver rimosso i nodi, è possibile rimuovere le risorse del set di scalabilità di macchine virtuali, Load Balancer ip e del set di scalabilità di macchine virtuali originali.

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

È quindi necessario rimuovere i riferimenti a queste risorse dal modello Resource Manager distribuito.

Il passaggio finale comporta l'aggiornamento del nome DNS e dell'indirizzo IP pubblico.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Anteprima) Abilitare più zone di disponibilità in un singolo set di scalabilità di macchine virtuali

La soluzione indicata in precedenza usa un nodeType per ogni AZ. La soluzione seguente consentirà agli utenti di distribuire 3 di AZ nello stesso nodeType.

**Poiché questa funzionalità è attualmente in anteprima, non è attualmente supportata per gli scenari di produzione.**

Il modello di esempio completo è [presente qui.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Architettura della zona Service Fabric disponibilità di Azure][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Configurazione delle zone in un set di scalabilità di macchine virtuali
Per abilitare le zone in un set di scalabilità di macchine virtuali, è necessario includere i tre valori seguenti nella risorsa del set di scalabilità di macchine virtuali.

* Il primo valore è la **proprietà zones,** che specifica le zone di disponibilità presenti nel set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true. **Il set di scalabilità in 3Z può essere ridimensionato fino a 300 macchine virtuali anche con "singlePlacementGroup = true".**
* Il terzo valore è "zoneBalance", che garantisce un rigido bilanciamento della zona. Deve essere "true". In questo modo si garantisce che le distribuzioni delle macchine virtuali tra le zone non siano sbilanciate, assicurando che quando una delle zone si interrompe, le altre due zone dispongano di macchine virtuali sufficienti per garantire che il cluster continui a essere in esecuzione senza interruzioni. Un cluster con una distribuzione di macchine virtuali non bilanciata potrebbe non essere in grado di superare uno scenario di down zone, in quanto tale zona potrebbe avere la maggior parte delle macchine virtuali. La distribuzione sbilanciata delle macchine virtuali tra le zone può anche causare problemi correlati al posizionamento dei servizi & gli aggiornamenti dell'infrastruttura si bloccano. Informazioni su [zoneBalancing.](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)
* Non è necessario configurare le sostituzioni FaultDomain e UpgradeDomain.

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
> * **Service Fabric cluster devono avere almeno un nodeType primario. DurabilityLevel di Primary nodeTypes deve essere Silver o superiore.**
> * Il set di scalabilità di macchine virtuali con spanning AZ deve essere configurato con almeno 3 zone di disponibilità indipendentemente da durabilityLevel.
> * AZ spanning virtual machine scale set with Silver durability (or above), should have atleast 15 VMs.
> * AZ spanning virtual machine scale set with Bronze durability (AZ spanning virtual machine scale set with Bronze durability, should have atleast 6 VMs).

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Abilitazione del supporto per più zone nell'Service Fabric nodeType
Il Service Fabric nodeType deve essere abilitato per supportare più zone di disponibilità.

* Il primo valore è **multipleAvailabilityZones** che deve essere impostato su true per nodeType.
* Il secondo valore è **sfZonalUpgradeMode** ed è facoltativo. Questa proprietà non può essere modificata se un tipo di nodo con più AZ è già presente nel cluster.
  La proprietà controlla il raggruppamento logico delle macchine virtuali nei domini di aggiornamento.
  **Se il valore è impostato su "Parallel":** Le macchine virtuali sotto il tipo di nodo verranno raggruppate in UD ignorando le informazioni sulla zona in 5 UD. Ciò comporta l'aggiornamento di UD0 in tutte le zone contemporaneamente. Questa modalità di distribuzione è più veloce per gli aggiornamenti, ma non è consigliabile perché non è in linea con le linee guida SDP, che specificano che gli aggiornamenti devono essere applicati solo una zona alla volta.
  **Se value viene omesso o impostato su "Hierarchical":** Le macchine virtuali verranno raggruppate in modo da riflettere la distribuzione di zona in un massimo di 15 ID. Ognuna delle 3 zone avrà 5 ID. In questo modo gli aggiornamenti passano alla zona successiva solo dopo aver completato 5 UD all'interno della prima zona, lentamente attraverso 15 UD (3 zone, 5 UD), che è più sicuro dal punto di vista del cluster e dell'applicazione utente.
  Questa proprietà definisce solo il comportamento di aggiornamento per gli aggiornamenti dell'applicazione ServiceFabric e del codice. Gli aggiornamenti del set di scalabilità di macchine virtuali sottostanti saranno comunque paralleli in tutti gli AZ.
  Questa proprietà non avrà alcun impatto sulla distribuzione UD per i tipi di nodo in cui non sono abilitate più zone.
* Il terzo valore è **vmssZonalUpgradeMode = Parallel**. Si tratta di *una* proprietà obbligatoria da configurare nel cluster, se viene aggiunto un nodeType con più AZ. Questa proprietà definisce la modalità di aggiornamento per gli aggiornamenti del set di scalabilità di macchine virtuali che si verificano in parallelo in tutte le AZ contemporaneamente.
  Al momento questa proprietà può essere impostata solo su parallelo.
* Il Service Fabric apiVersion della risorsa cluster deve essere "2020-12-01-preview" o versione successiva.
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
        "reliabilityLevel": "Platinum",
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
> * L'indirizzo IP Load Balancer le risorse devono usare lo SKU Standard, come descritto in precedenza nell'articolo.
> * La proprietà "multipleAvailabilityZones" in nodeType può essere definita solo al momento della creazione di nodeType e non può essere modificata in un secondo momento. Di conseguenza, i nodeType esistenti non possono essere configurati con questa proprietà.
> * Quando "sfZonealUpgradeMode" viene omesso o impostato su "Hierarchical", le distribuzioni del cluster e dell'applicazione saranno più lente perché sono presenti più domini di aggiornamento nel cluster. È importante modificare correttamente i timeout dei criteri di aggiornamento da incorporare per la durata dell'aggiornamento per 15 domini di aggiornamento. I criteri di aggiornamento per l'app e il cluster devono essere aggiornati per garantire che la distribuzione non superi i timeout di 12 ore per la distribuzione delle risorse di Azure. Ciò significa che la distribuzione non deve richiedere più di 12 ore per 15 DUD, ad esempio non deve richiedere più di 40 min/UD.
> * Impostare **reliabilityLevel = Platinum per** assicurarsi che il cluster non sia più in grado di far parte dello scenario di un'unica zona in cui si è insediato.

>[!NOTE]
> Per la procedura consigliata, è consigliabile impostare sfZonealUpgradeMode su Gerarchico o omettersi. La distribuzione seguirà la distribuzione di zona delle macchine virtuali che influiscono su una quantità minore di repliche e/o istanze, rendendole più sicure.
> Usare sfZonealUpgradeMode impostato su Parallel se la velocità di distribuzione è una priorità o solo il carico di lavoro senza stato viene eseguito nel tipo di nodo con più AZ. In questo modo la procedura definita dall'utente verrà illustrata in parallelo in tutte le AZ.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrazione al tipo di nodo con più zone di disponibilità
Per tutti gli scenari di migrazione, è necessario aggiungere un nuovo nodeType che avrà più zone di disponibilità supportate. Non è possibile eseguire la migrazione di un nodeType esistente per supportare più zone.
[L'articolo qui](./service-fabric-scale-up-primary-node-type.md) acquisisce i passaggi dettagliati dell'aggiunta di un nuovo nodeType e anche delle altre risorse necessarie per il nuovo nodeType, ad esempio le risorse IP e LB. Lo stesso articolo descrive anche ora come ritirare il nodeType esistente dopo che nodeType con più zone di disponibilità è stato aggiunto al cluster.

* Migrazione da un nodeType che usa risorse LB e [](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) IP di base: questa operazione è già descritta qui per la soluzione con un tipo di nodo per ogni AZ. 
    Per il nuovo tipo di nodo, l'unica differenza è che esistono solo 1 set di scalabilità di macchine virtuali e 1 tipo di nodo per tutte le AZ anziché 1 per ogni AZ.
* Migrazione da un nodeType che usa le risorse LB e IP SKU Standard con NSG: seguire la stessa procedura descritta in precedenza, ad eccezione del fatto che non è necessario aggiungere nuove risorse LB, IP e NSG e le stesse risorse possono essere riutilizzate nel nuovo nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
