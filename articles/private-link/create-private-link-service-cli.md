---
title: "Guida introduttiva: creare un servizio di collegamento privato di Azure usando l'interfaccia della riga"
description: Questa Guida introduttiva illustra come creare un servizio di collegamento privato di Azure usando l'interfaccia della riga di comando di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563082"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Guida introduttiva: creare un servizio di collegamento privato usando l'interfaccia della riga di comando

Introduzione alla creazione di un servizio Collegamento privato che fa riferimento al proprio servizio.  Assegnare l'accesso con collegamento privato al servizio o alla risorsa distribuita dietro un'istanza di Azure Load Balancer Standard.  Gli utenti del servizio hanno accesso privato dalla loro rete virtuale.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

* Denominato **CreatePrivLinkService-RG**. 
* Nella posizione **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Creare un bilanciamento del carico interno

In questa sezione si crea una rete virtuale e un'istanza interna di Azure Load Balancer.

### <a name="virtual-network"></a>Rete virtuale

In questa sezione si creano la rete virtuale e la subnet che ospiteranno l'istanza di Load Balancer usata per accedere al servizio Collegamento privato.

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* **Subnet denominata subnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreatePrivLinkService-RG** .
* Percorso di **eastus2**.
* Disabilitare i criteri di rete per il servizio di collegamento privato nella subnet.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Per aggiornare la subnet per disabilitare i criteri di rete del servizio di collegamento privato, usare [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Con [az network lb create](/cli/azure/network/lb#az-network-lb-create) creare un servizio di bilanciamento del carico pubblico:

* Denominato **myLoadBalancer**.
* Un pool front-end denominato **myFrontEnd**.
* Un pool back-end denominato **myBackEndPool**.
* Associato alla rete virtuale **myVNet**.
* Associato **alla subnet di** back-end della subnet.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Con [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) creare un probe di integrità:

* Che esegua il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Con [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) creare una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Timeout di inattività di **15 minuti**.
* Abilitare la reimpostazione TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

In questa sezione viene creato un servizio di collegamento privato che usa il Azure Load Balancer creato nel passaggio precedente.

Creare un servizio di collegamento privato usando una configurazione IP front-end di Load Balancer standard con [AZ network private-Link-Service create](/cli/azure/network/private-link-service#az-network-private-link-service-create):

* Denominato **myPrivateLinkService**.
* Nella rete virtuale **myVNet**.
* Associato a **myLoadBalancer** di bilanciamento del carico standard e a **frontend** per la configurazione front-end.
* Nel percorso **eastus2** .
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Il servizio di collegamento privato viene creato e può ricevere traffico. Per visualizzare i flussi di traffico, configurare l'applicazione in base al servizio di bilanciamento del carico standard.


## <a name="create-private-endpoint"></a>Creare un endpoint privato

In questa sezione si eseguirà il mapping del servizio di collegamento privato a un endpoint privato. Una rete virtuale contiene l'endpoint privato per il servizio di collegamento privato. Questa rete virtuale contiene le risorse che accederanno al servizio di collegamento privato.

### <a name="create-private-endpoint-virtual-network"></a>Crea rete virtuale endpoint privato

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) creare una rete virtuale:

* Denominato **myVNetPE**.
* Prefisso dell'indirizzo **11.1.0.0/16**.
* Subnet denominata **mySubnetPE**.
* Prefisso della subnet di **11.1.0.0/24**.
* Nel gruppo di risorse **CreatePrivLinkService-RG** .
* Percorso di **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Per aggiornare la subnet per disabilitare i criteri di rete degli endpoint privati, usare [AZ Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Crea endpoint e connessione

* Usare [AZ network private-Link-Service Show](/cli/azure/network/private-link-service#az_network_private_link_service_show) per ottenere l'ID risorsa del servizio di collegamento privato. Il comando inserisce l'ID risorsa in una variabile per un uso successivo.

* Usare [AZ network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) per creare l'endpoint privato nella rete virtuale creata in precedenza.

* Denominato **MyPrivateEndpoint**.
* Nel gruppo di risorse **CreatePrivLinkService-RG** .
* Nome della connessione **myPEconnectiontoPLS**.
* Percorso di **eastus2**.
* Nella rete virtuale **myVNetPE** e nella subnet **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, usare il comando [AZ Group Delete](/cli/azure/group#az-group-delete) per rimuovere il gruppo di risorse, il servizio di collegamento privato, il servizio di bilanciamento del carico e tutte le risorse correlate.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* Sono state create una rete virtuale e un'istanza interna di Azure Load Balancer.
* È stato creato un servizio Collegamento privato

Per altre informazioni sull'endpoint privato di Azure, passare a:
> [!div class="nextstepaction"]
> [Avvio rapido: Creare un endpoint privato con l'interfaccia della riga di comando di Azure](create-private-endpoint-cli.md)
