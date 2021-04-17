---
title: Distribuire tipi di nodo solo senza stato in un cluster Service Fabric stato
description: Informazioni su come creare e distribuire tipi di nodo senza stato in un cluster Service Fabric Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 68c617b6e9345910bfd913e61e227a8e6c401bbc
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576041"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Distribuire un cluster Service Fabric Azure con tipi di nodo solo senza stato
Service Fabric tipi di nodo si presunzione intrinsecamente che, a un certo punto, i servizi con stato potrebbero essere inseriti nei nodi. I tipi di nodo senza stato rilascino questo presupposto per un tipo di nodo, consentendo così al tipo di nodo di usare altre funzionalità, ad esempio operazioni di scalabilità orizzontale più veloci, il supporto per gli aggiornamenti automatici del sistema operativo con durabilità Bronze e la scalabilità orizzontale a più di 100 nodi in un singolo set di scalabilità di macchine virtuali.

* I tipi di nodo primario non possono essere configurati come senza stato
* I tipi di nodo senza stato sono supportati solo con i livelli di durabilità Bronze
* I tipi di nodo senza stato sono supportati solo Service Fabric Runtime versione 7.1.409 o successiva.


Sono disponibili modelli di esempio: [Service Fabric di tipi di nodo senza stato](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Abilitazione dei tipi di nodo senza stato Service Fabric cluster
Per impostare uno o più tipi di nodo come senza stato in una risorsa cluster, impostare la **proprietà isStateless** su **true.** Quando si distribuisce un cluster Service Fabric cluster con tipi di nodo senza stato, ricordarsi di avere almeno un tipo di nodo primario nella risorsa cluster.

* Il Service Fabric apiVersion della risorsa cluster deve essere "2020-12-01-preview" o versione successiva.

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
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
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
Per abilitare i tipi di nodo senza stato, è necessario configurare la risorsa set di scalabilità di macchine virtuali sottostante nel modo seguente:

* Il valore  **della proprietà singlePlacementGroup,** che deve essere impostato su **false** se è necessario eseguire il ridimensionamento a più di 100 macchine virtuali.
* La proprietà **upgradeMode** del set di scalabilità deve essere impostata su **Rolling.**
* La modalità di aggiornamento in sequenza richiede la configurazione dell'estensione integrità dell'applicazione o dei probe di integrità. Configurare il probe di integrità con la configurazione predefinita per i tipi di nodo senza stato, come suggerito di seguito. Dopo la distribuzione delle applicazioni nel tipo di nodo, è possibile modificare le porte dell'estensione Probe di integrità/Integrità per monitorare l'integrità dell'applicazione.

>[!NOTE]
> Durante l'uso della scalabilità automatica con i tipi di nodo senza stato, dopo l'operazione di ridimensionamento, lo stato del nodo non viene pulito automaticamente. Per pulire nodeState dei nodi down durante la scalabilità automatica, è consigliabile usare Service Fabric [helper](https://github.com/Azure/service-fabric-autoscale-helper) di scalabilità automatica.

```json
{
    "apiVersion": "2019-03-01",
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
        },
        "platformFaultDomainCount": 5
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
            "durabilityLevel": "Bronze",
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

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Configurazione di tipi di nodo senza stato con più zone di disponibilità
Per configurare il tipo di nodo senza stato che si estende tra più zone di disponibilità, seguire la documentazione [qui,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)insieme alle poche modifiche seguenti:

* Imposta **singlePlacementGroup:**  **false se**  è necessario che siano abilitati più gruppi di posizionamento.
* Impostare  **upgradeMode** : **In sequenza e**   aggiungere probe di integrità/estensione integrità dell'applicazione come indicato in precedenza.
* Impostare **platformFaultDomainCount:** **5 per** il set di scalabilità di macchine virtuali.

>[!NOTE]
> Indipendentemente dalla vmSSZonalUpgradeMode configurata nel cluster, gli aggiornamenti del set di scalabilità di macchine virtuali vengono sempre esatti in sequenza una zona di disponibilità alla volta per il tipo di nodo senza stato che si estende su più zone, in quanto usa la modalità di aggiornamento in sequenza.

Per informazioni di riferimento, esaminare il [modello per](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) la configurazione dei tipi di nodo senza stato con più zone di disponibilità

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>Ip pubblico e Load Balancer risorsa
Per abilitare il ridimensionamento a più di 100 macchine virtuali in una risorsa del set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento tale set di scalabilità di macchine virtuali devono entrambi usare uno SKU *Standard.* La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU basico, che non supporta il ridimensionamento a più di 100 macchine virtuali. Un servizio di bilanciamento del carico SKU Standard blocca tutto il traffico dall'esterno per impostazione predefinita. per consentire il traffico esterno, è necessario distribuire un gruppo di sicurezza di rete nella subnet.

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
> Non è possibile eseguire una modifica sul posto dello SKU nell'indirizzo IP pubblico e nelle risorse di bilanciamento del carico. 

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>SKU Standard e Load Balancer in uscita
Load Balancer Standard ip pubblico standard introducono nuove capacità e comportamenti diversi per la connettività in uscita rispetto all'uso di SKU Basic. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [Connessioni in uscita e](../load-balancer/load-balancer-outbound-connections.md) Azure [Load Balancer Standard](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Il modello standard fa riferimento a un gruppo di sicurezza di rete che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte necessarie per le Service Fabric di gestione. Le regole del gruppo di sicurezza di rete possono essere modificate per soddisfare i requisiti.

>[!NOTE]
> Qualsiasi cluster Service Fabric che usa un bilanciamento del carico SKU Standard deve assicurarsi che ogni tipo di nodo abbia una regola che consenta il traffico in uscita sulla porta 443. Questa operazione è necessaria per completare l'installazione del cluster e qualsiasi distribuzione senza tale regola avrà esito negativo.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Eseguire la migrazione all'uso di tipi di nodo senza stato in un cluster
Per tutti gli scenari di migrazione, è necessario aggiungere un nuovo tipo di nodo solo senza stato. Non è possibile eseguire la migrazione del tipo di nodo esistente in modo che sia solo senza stato.

Per eseguire la migrazione di un cluster, che usava un Load Balancer e un indirizzo IP con uno SKU di base, è prima necessario creare una risorsa Load Balancer e IP completamente nuova usando lo SKU Standard. Non è possibile aggiornare queste risorse sul posto.

Nei nuovi tipi di nodo senza stato che si vuole usare è necessario fare riferimento ai nuovi tipi di nodo LB e IP. Nell'esempio precedente vengono aggiunte nuove risorse del set di scalabilità di macchine virtuali da usare per i tipi di nodo senza stato. Questi set di scalabilità di macchine virtuali fanno riferimento al bilanciamento del carico e all'indirizzo IP appena creati e sono contrassegnati come tipi di nodo senza stato nella Service Fabric cluster.

Per iniziare, è necessario aggiungere le nuove risorse al modello di Resource Manager esistente. Tali risorse includono:
* Una risorsa IP pubblico che usa lo SKU Standard.
* Una Load Balancer risorsa con SKU Standard.
* Gruppo di scalabilità di rete a cui fa riferimento la subnet in cui si distribuiscono i set di scalabilità di macchine virtuali.

Al termine della distribuzione delle risorse, è possibile iniziare a disabilitare i nodi nel tipo di nodo da rimuovere dal cluster originale.

## <a name="next-steps"></a>Passaggi successivi 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Tipi di nodo e set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)

