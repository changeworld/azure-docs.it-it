---
title: 'Azure ExpressRoute: reimpostare il peering del circuito'
description: Informazioni su come abilitare e disabilitare i peering per un circuito ExpressRoute di Azure usando Azure PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559574"
---
# <a name="reset-expressroute-circuit-peerings"></a>Reimpostare i peering del circuito ExpressRoute

Questo articolo descrive come abilitare e disabilitare i peering di un circuito ExpressRoute usando PowerShell. I peering sono abilitati per impostazione predefinita quando vengono creati. Quando si disabilita un peering, la sessione BGP nella connessione primaria e secondaria del circuito ExpressRoute verrà arrestata. Si perderà la connettività per questo peering a Microsoft. Quando si Abilita un peering, verrà stabilita la sessione BGP nella connessione primaria e secondaria del circuito ExpressRoute. Per questo peering verrà ripristinata la connettività a Microsoft. È possibile abilitare e disabilitare il peering per il peering Microsoft e il peering privato di Azure indipendentemente dal circuito ExpressRoute.

Esistono due scenari in cui può risultare utile reimpostare i peering di ExpressRoute.
* Se si vuole testare la progettazione e l'implementazione del ripristino di emergenza. Ad esempio, sono presenti due circuiti ExpressRoute. È possibile disabilitare i peering in un circuito e forzare il failover del traffico di rete sull'altro circuito.
* Abilitare il rilevamento di inoltri bidirezionali (BFD) nel peering privato di Azure o nel peering Microsoft del circuito ExpressRoute. BFD viene abilitato per impostazione predefinita nel peering privato di Azure se il circuito ExpressRoute è stato creato dopo il 1 ° agosto 2018 e per il peering Microsoft dopo il 10 gennaio 2020. Se il circuito è stato creato prima della data elencata, sarà necessario reimpostare il peering per abilitare BFD. 

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Reimpostare un peering

1. Se si esegue PowerShell in locale, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Specificare la sottoscrizione da usare.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Eseguire i comandi seguenti per recuperare il circuito ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identificare il peering che si vuole disabilitare o abilitare. *Peerings* è una matrice. Nell'esempio seguente Peerings[0] si riferisce al peering privato di Azure e Peerings[1] al peering Microsoft.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Eseguire i comandi seguenti per modificare lo stato del peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Lo stato del peering deve essere quello impostato. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla risoluzione di un problema ExpressRoute, vedere gli articoli seguenti:
* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Risoluzione dei problemi di prestazioni di rete](expressroute-troubleshooting-network-performance.md)
