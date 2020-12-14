---
title: Distribuire tipi di nodo solo senza stato in un cluster Service Fabric
description: Informazioni su come creare e distribuire tipi di nodo senza stato nel cluster di Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: d3ce6e888c937676027f2b71578c38b56f3bd6af
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388021"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>Distribuire un cluster di Azure Service Fabric con tipi di nodo solo senza stato (anteprima)
I tipi di nodo Service Fabric hanno presupposto intrinseco che in un determinato momento i servizi con stato possono essere inseriti nei nodi. I tipi di nodo senza stato rilassano questa ipotesi per un tipo di nodo, consentendo così al tipo di nodo di usare altre funzionalità, ad esempio le operazioni di scalabilità orizzontale più veloci, il supporto per aggiornamenti automatici del sistema operativo sulla durabilità Bronze e la scalabilità orizzontale a più di 100 nodi in un singolo set di scalabilità

* I tipi di nodo primari non possono essere configurati come senza stato
* I tipi di nodo senza stato sono supportati solo con i livelli di durabilità Bronze
* I tipi di nodo senza stato sono supportati solo in Service Fabric Runtime versione 7.1.409 o successiva.


Sono disponibili modelli di esempio: [Service Fabric modello di tipi di nodo](https://github.com/Azure-Samples/service-fabric-cluster-templates) senza stato

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Abilitazione di tipi di nodo senza stato nel cluster Service Fabric
Per impostare uno o più tipi di nodo come senza **stato** in una risorsa cluster, impostare la proprietà IsSynchronized su "true". Quando si distribuisce un cluster Service Fabric con tipi di nodo senza stato, ricordarsi di avere almeno un tipo di nodo primario nella risorsa cluster.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateles": false,
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
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Configurazione del set di scalabilità di macchine virtuali per i tipi di nodo senza stato
Per abilitare i tipi di nodo senza stato, è necessario configurare la risorsa del set di scalabilità di macchine virtuali sottostante nel modo seguente:

* Proprietà del valore  **singlePlacementGroup** , che deve essere impostata su true/false a seconda del requisito per la scalabilità a più di 100 VM.
* **UpgradeMode** del set di scalabilità che deve essere impostato su in sequenza.
* Per la modalità di aggiornamento in sequenza è richiesta l'estensione integrità dell'applicazione o i probe di integrità. Configurare il probe di integrità con la configurazione predefinita per i tipi di nodo senza stato come suggerito di seguito. Una volta distribuite le applicazioni nel nodo NodeType, è possibile modificare le porte dell'estensione Probe/integrità per monitorare l'integrità dell'applicazione.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
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
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>IP pubblico e risorsa Load Balancer
Per abilitare il ridimensionamento a più di 100 VM in una risorsa del set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento il set di scalabilità di macchine virtuali devono entrambi usare uno SKU *standard* . La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU Basic, che non supporta il ridimensionamento a più di 100 macchine virtuali. Per impostazione predefinita, un servizio di bilanciamento del carico con SKU standard bloccherà tutto il traffico dall'esterno. per consentire il traffico esterno, una NSG deve essere distribuita nella subnet.

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



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Eseguire la migrazione a usando i tipi di nodo senza stato da un cluster usando uno SKU di base Load Balancer e un IP dello SKU Basic
Per eseguire la migrazione di un cluster che stava usando un Load Balancer e un IP con uno SKU di base, è necessario creare prima di tutto una nuova Load Balancer e una risorsa IP usando lo SKU standard. Non è possibile aggiornare queste risorse sul posto.

È necessario fare riferimento ai nuovi LB e IP nei nuovi tipi di nodo senza stato che si vuole usare. Nell'esempio precedente, vengono aggiunte nuove risorse del set di scalabilità di macchine virtuali da usare per i tipi di nodo senza stato. Questi set di scalabilità di macchine virtuali fanno riferimento a LB e IP appena creati e sono contrassegnati come tipi di nodo senza stato nella risorsa del cluster Service Fabric.

Per iniziare, sarà necessario aggiungere le nuove risorse al modello di Gestione risorse esistente. Tali risorse includono:
* Una risorsa IP pubblico con SKU standard.
* Una risorsa Load Balancer usando lo SKU standard.
* Un NSG a cui fa riferimento la subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.


Un esempio di queste risorse è disponibile nel modello di [esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-2-NodeTypes-Windows-Stateless-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Una volta terminata la distribuzione delle risorse, è possibile iniziare a disabilitare i nodi del tipo di nodo che si vuole rimuovere dal cluster originale.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 
```

## <a name="next-steps"></a>Passaggi successivi 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Tipi di nodo e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)

