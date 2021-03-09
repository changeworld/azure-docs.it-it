---
title: "Guida introduttiva: creare e configurare un server di route usando l'interfaccia della riga"
description: Questa Guida introduttiva illustra come creare e configurare un server di route usando l'interfaccia della riga di comando di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: ef41c52fa1b63094d952dc34f81db36f7aeaac95
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521289"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Guida introduttiva: creare e configurare un server di route usando l'interfaccia della riga 

Questo articolo illustra come configurare il server di route di Azure in peer con appliance virtuali di rete nella rete virtuale usando l'interfaccia della riga di comando di Azure. Il server di route di Azure apprenderà le route dall'appliance virtuale di rete e le programmerà per le macchine virtuali nella rete virtuale. Inoltre, le route di rete virtuale vengono annunciate all'appliance virtuale di rete. Per altre informazioni, vedere [server di route di Azure](overview.md).

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Prerequisiti 

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Assicurarsi di avere la versione più recente dell'interfaccia della riga di comando di Azure oppure è possibile usare Azure Cloud Shell nel portale. 
* Esaminare i [limiti del servizio per il server di route di Azure](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Creare un server di route 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione. 

Per iniziare la configurazione, accedere al proprio account Azure. Se si usa l'opzione "Prova" di Cloud Shell, l'accesso viene effettuato automaticamente. Per eseguire la connessione, usare gli esempi che seguono:

```azurecli-interactive
az login
```

Controllare le sottoscrizioni per l'account.

```azurecli-interactive
az account list
```

Selezionare la sottoscrizione per la quale si vuole creare un circuito ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale 

Prima di poter creare un server di route di Azure, è necessaria una rete virtuale per ospitare la distribuzione. Usare il comando seguente per creare un gruppo di risorse e una rete virtuale. Se si dispone già di una rete virtuale, è possibile passare alla sezione successiva.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Aggiungere una subnet 

1. Aggiungere una subnet denominata *RouteServerSubnet* per distribuire il server di route di Azure in. Questa subnet è una subnet dedicata solo per il server di route di Azure. Il valore di RouteServerSubnet deve essere/27 o un prefisso più breve (ad esempio/26,/25) oppure verrà visualizzato un messaggio di errore quando si aggiunge il server di route di Azure.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Ottenere l'ID RouteServerSubnet. Per visualizzare l'ID risorsa di tutte le subnet nella rete virtuale, usare questo comando: 

    ```azurecli-interactive 
    subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

L'ID RouteServerSubnet è simile a quello riportato di seguito: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Creazione del server di route 

Creare il server di route con questo comando: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

Il percorso deve corrispondere al percorso della rete virtuale. HostedSubnet è l'ID RouteServerSubnet ottenuto nella sezione precedente. 

## <a name="create-peering-with-an-nva"></a>Creare il peering con un'appliance virtuale di dispositivo 

Usare il comando seguente per stabilire il peering dal server di route all'appliance virtuale di dispositivo: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" è l'indirizzo IP della rete virtuale assegnato all'appliance virtuale di rete. "nva_asn" è il numero di sistema autonomo (ASN) configurato nell'appliance virtuale di rete. Il numero ASN può essere qualsiasi numero a 16 bit diverso da quelli nell'intervallo 65515-65520. Questo intervallo di ASN è riservato da Microsoft. 

Per configurare il peering con diversi appliance virtuale di sistema o un'altra istanza della stessa appliance virtuale di sistema per la ridondanza, usare questo comando:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Completare la configurazione nell'appliance virtuale di dispositivo 

Per completare la configurazione nell'appliance virtuale di dispositivo e abilitare le sessioni BGP, sono necessari l'IP e l'ASN del server di route di Azure. È possibile ottenere queste informazioni usando questo comando: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

L'output contiene le informazioni seguenti. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Configurare lo scambio delle route 

Se si dispone di un gateway ExpressRoute e di un gateway VPN di Azure nella stessa VNet e si vuole scambiare le route, è possibile abilitare lo scambio delle route nel server di route di Azure.

> [!IMPORTANT]
> Per le distribuzioni di Greenfield, assicurarsi di creare il gateway VPN di Azure prima di creare il server di route di Azure. in caso contrario, la distribuzione del gateway VPN di Azure avrà esito negativo.
> 

1. Per abilitare lo scambio di route tra il server di route di Azure e i gateway, usare questo comando:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Per disabilitare lo scambio di route tra il server di route di Azure e i gateway, usare questo comando:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Risoluzione dei problemi 

È possibile visualizzare le route annunciate e ricevute dal server di route di Azure con questo comando:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up"></a>Eseguire la pulizia 

Se il server di route di Azure non è più necessario, usare questi comandi per rimuovere il peering BGP e quindi rimuovere il server di route. 

1. Rimuovere il peering BGP tra il server di route di Azure e un appliance virtuale di Azure con questo comando:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Rimuovere il server di route di Azure con questo comando: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il server di route di Azure, continuare a conoscere il modo in cui il server di routing di Azure interagisce con i gateway ExpressRoute e VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute e supporto VPN di Azure](expressroute-vpn-support.md)
 
