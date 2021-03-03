---
title: Configurare le impostazioni di rete per Service Fabric cluster gestiti (anteprima)
description: Informazioni su come configurare il cluster gestito Service Fabric per le regole NSG, l'accesso alle porte RDP, le regole di bilanciamento del carico e altro ancora.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745915"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Configurare le impostazioni di rete per Service Fabric cluster gestiti (anteprima)

Service Fabric cluster gestiti vengono creati con una configurazione di rete predefinita. Questa configurazione è costituita da regole obbligatorie per la funzionalità del cluster essenziale e da alcune regole facoltative progettate per semplificare la configurazione dei clienti.

Oltre alla configurazione di rete predefinita, è possibile modificare le regole di rete per soddisfare le esigenze dello scenario.

## <a name="nsg-rules-guidance"></a>Linee guida per le regole di NSG

Tenere presenti queste considerazioni quando si creano nuove regole NSG per il cluster gestito.

* Service Fabric cluster gestiti riservano l'intervallo di priorità della regola NSG da 0 a 999 per la funzionalità essenziale. Non è possibile creare regole NSG personalizzate con un valore di priorità inferiore a 1000.
* Service Fabric cluster gestiti riservano l'intervallo di priorità 3001 a 4000 per la creazione di regole NSG facoltative. Queste regole vengono aggiunte automaticamente per semplificare e velocizzare le configurazioni. È possibile eseguire l'override di queste regole aggiungendo regole NSG personalizzate nell'intervallo di priorità 1000 a 3000.
* Le regole NSG personalizzate devono avere una priorità compresa nell'intervallo compreso tra 1000 e 3000.

## <a name="apply-nsg-rules"></a>Applicare le regole di NSG

Con i cluster di Service Fabric classici (non gestiti), è necessario dichiarare e gestire una risorsa *Microsoft. Network/networkSecurityGroups* separata per [applicare le regole del gruppo di sicurezza di rete (NSG) al cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Service Fabric cluster gestiti consentono di assegnare regole NSG direttamente all'interno della risorsa cluster del modello di distribuzione.

Usare la proprietà [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) della risorsa *Microsoft. ServiceFabric/managedclusters* (versione `2021-01-01-preview` o successiva) per assegnare le regole NSG. Ad esempio:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>Porte RDP

Per impostazione predefinita, Service Fabric cluster gestiti non consentono l'accesso alle porte RDP. È possibile aprire le porte RDP a Internet impostando la proprietà seguente su una risorsa cluster gestita Service Fabric.

```json
"allowRDPAccess": true 
```

Quando la proprietà allowRDPAccess è impostata su true, la seguente regola NSG verrà aggiunta alla distribuzione del cluster.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Porte ClientConnection e HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Regola NSG: SFMC_AllowServiceFabricGatewayToSFRP

Viene aggiunta una regola NSG predefinita per consentire al provider di risorse Service Fabric di accedere al clientConnectionPort e httpGatewayConnectionPort del cluster. Questa regola consente l'accesso alle porte tramite il tag di servizio ' ServiceFabric '.

>[!NOTE]
>Questa regola viene sempre aggiunta e non può essere sottoposta a override.

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Regola NSG: SFMC_AllowServiceFabricGatewayPorts

Si tratta di una regola NSG facoltativa per consentire l'accesso a clientConnectionPort e httpGatewayPort da Internet. Questa regola consente ai clienti di accedere a SFX, connettersi al cluster tramite PowerShell e usare Service Fabric endpoint dell'API cluster dall'esterno del. 

>[!NOTE]
>Questa regola non verrà aggiunta se è presente una regola personalizzata con gli stessi valori di accesso, direzione e protocollo per la stessa porta. È possibile eseguire l'override di questa regola con regole NSG personalizzate. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

## <a name="load-balancer-ports"></a>Porte del servizio di bilanciamento del carico

Service Fabric cluster gestiti crea una regola NSG nell'intervallo di priorità predefinito per tutte le porte del servizio di bilanciamento del carico (LB) configurate nella sezione "loadBalancingRules" in proprietà *ManagedCluster* . Questa regola apre le porte LB per il traffico in ingresso da Internet.  

>[!NOTE]
>Questa regola viene aggiunta nell'intervallo di priorità facoltativo e può essere sottoposta a override aggiungendo regole NSG personalizzate.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>Probe del servizio di bilanciamento del carico

Service Fabric cluster gestiti crea automaticamente probe del servizio di bilanciamento del carico per le porte del gateway infrastruttura, nonché tutte le porte configurate nella sezione "loadBalancingRules" delle proprietà del cluster gestito.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="next-steps"></a>Passaggi successivi

[Opzioni di configurazione del cluster gestito Service Fabric](how-to-managed-cluster-configuration.md)

[Panoramica di Service Fabric cluster gestiti](overview-managed-cluster.md)
