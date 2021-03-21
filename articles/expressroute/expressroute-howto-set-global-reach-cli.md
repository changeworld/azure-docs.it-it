---
title: 'Azure ExpressRoute: configurare Copertura globale di ExpressRoute: interfaccia della riga di comando'
description: Informazioni su come collegare circuiti ExpressRoute per creare una rete privata tra le reti locali e abilitare Copertura globale usando l'interfaccia della riga di comando di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260232"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Configurare ExpressRoute Copertura globale usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare Copertura globale di Azure ExpressRoute usando l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Copertura globale di ExpressRoute](expressroute-global-reach.md).
 
Prima di iniziare la configurazione, è necessario soddisfare i requisiti seguenti:

* Installare la versione più recente dell'interfaccia della riga di comando di Azure. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).
* Avere familiarità con i [flussi di lavoro](expressroute-workflows.md) di provisioning dei circuiti ExpressRoute.
* Assicurarsi che i circuiti ExpressRoute siano in stato Provisioning eseguito.
* Assicurarsi che il peering privato di Azure sia configurato nei circuiti ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Per iniziare la configurazione, accedere al proprio account Azure. Il comando seguente apre il browser predefinito e chiede di specificare le credenziali di accesso per l'account Azure:  

```azurecli
az login
```

Se si hanno più sottoscrizioni di Azure, verificare le sottoscrizioni per l'account:

```azurecli
az account list
```

Specificare la sottoscrizione da usare:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificare i circuiti ExpressRoute per la configurazione

È possibile abilitare ExpressRoute Copertura globale tra due circuiti ExpressRoute. I circuiti devono trovarsi nei paesi/aree geografiche supportati e sono stati creati in località di peering diverse. Se la sottoscrizione è proprietaria di entrambi i circuiti, è possibile selezionare uno dei due circuiti per eseguire la configurazione. Tuttavia, se i due circuiti si trovano in sottoscrizioni di Azure diverse, è necessario creare una chiave di autorizzazione da uno dei circuiti. Utilizzando la chiave di autorizzazione generata dal primo circuito è possibile abilitare Copertura globale sul secondo circuito.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Abilitare la connettività tra le reti locali

Quando si esegue il comando per abilitare la connettività, tenere presenti i requisiti seguenti per i valori dei parametri:

* *peer-circuit* deve essere l'ID risorsa completo. Ad esempio:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *address-prefix* deve essere una subnet IPv4 "/29" (ad esempio, "10.0.0.0/29"). Gli indirizzi IP di questa subnet vengono usati per stabilire la connettività tra i due circuiti ExpressRoute. Non è possibile usare gli indirizzi in questa subnet nelle reti virtuali di Azure o nelle reti locali.

Eseguire il comando dell'interfaccia della riga di comando seguente per connettere due circuiti ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

L'output dell'interfaccia della riga di comando è simile al seguente:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Al termine di questa operazione, si avrà connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Abilitare la connettività tra circuiti ExpressRoute in sottoscrizioni di Azure diverse

Se i due circuiti non sono inclusi nella stessa sottoscrizione di Azure, è necessaria l'autorizzazione. Nella configurazione seguente viene generata l'autorizzazione nella sottoscrizione del circuito 2. Passare quindi la chiave di autorizzazione al circuito 1.

1. Generare una chiave di autorizzazione:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   L'output dell'interfaccia della riga di comando è simile al seguente:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Prendere nota dell'ID risorsa e della chiave di autorizzazione per il circuito 2.

1. Eseguire il comando seguente sul circuito 1, passando l'ID risorsa e la chiave di autorizzazione al circuito 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Al termine di questa operazione, si avrà connettività tra le reti locali su entrambi i lati attraverso i due circuiti ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Ottenere e verificare la configurazione

Usare il comando seguente per verificare la configurazione nel circuito in cui è stata eseguita (il circuito 1 nell'esempio precedente):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Nell'output dell'interfaccia della riga di comando verrà visualizzato *CircuitConnectionStatus*. Questa proprietà indica se la connettività tra i due circuiti è stata stabilita ("Connected") o meno ("Disconnected"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Disabilitare la connettività tra le reti locali

Per disabilitare la connettività, usare il comando seguente sul circuito in cui è stata eseguita la configurazione (il circuito 1 nell'esempio precedente).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Usare il comando ```show``` per verificare lo stato.

Al termine di questa operazione, non si avrà più connettività tra le reti locali attraverso i circuiti ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Copertura globale di ExpressRoute](expressroute-global-reach.md)
* [Verifica della connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
