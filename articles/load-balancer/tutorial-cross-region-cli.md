---
title: "Esercitazione: creare un servizio di bilanciamento del carico tra aree usando l'interfaccia della riga di comando di Azure"
titleSuffix: Azure Load Balancer
description: Introduzione a questa esercitazione distribuzione di una Azure Load Balancer tra più aree usando l'interfaccia della riga di comando di Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225184"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Esercitazione: creare un Azure Load Balancer tra aree usando l'interfaccia della riga di comando di Azure

Un servizio di bilanciamento del carico tra più aree garantisce che un servizio sia disponibile a livello globale in più aree di Azure. Se si verifica un errore in un'area, il traffico viene indirizzato al servizio di bilanciamento del carico a livello di area integro più vicino.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico tra aree.
> * Creare una regola del servizio di bilanciamento del carico.
> * Creare un pool back-end contenente due servizi di bilanciamento del carico a livello di area.
> * Testare il servizio di bilanciamento del carico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Due istanze **standard** di SKU Azure Load Balancer con pool back-end distribuiti tra due diverse aree di Azure.
    - Per informazioni sulla creazione di un servizio di bilanciamento del carico standard a livello di area e delle macchine virtuali per i pool back-end, vedere [Guida introduttiva: creare un servizio di bilanciamento del carico pubblico per bilanciare il carico](quickstart-load-balancer-standard-public-cli.md)delle macchine virtuali
        - Aggiungere il nome dei bilanciamenti del carico e delle macchine virtuali in ogni area con a **-R1** e **-R2**. 
- INTERFACCIA della riga di comando di Azure installata localmente o Azure Cloud Shell.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere all'interfaccia della riga di comando di Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Creare un servizio di bilanciamento del carico tra aree

In questa sezione verrà creato un servizio di bilanciamento del carico tra aree, un indirizzo IP pubblico e una regola di bilanciamento del carico.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az-group-create) un gruppo di risorse:

* Denominato **myResourceGroupLB-CR**.
* Nella località **westus** .

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico tra aree con [AZ Network Cross-Region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Denominato **myLoadBalancer-CR**.
* Un pool Front-End denominato " **frontend-CR**".
* Un pool back-end denominato **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Creare una regola di bilanciamento del carico con [AZ Network Cross-Region-lb Rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Denominato **myhttprule come-CR**
* In ascolto sulla **porta 80** nel pool di front **-end**.
* Invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool-CR** usando la **porta 80**. 
* Protocollo **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Creare un pool back-end

In questa sezione verranno aggiunti due servizi di bilanciamento del carico standard a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.

> [!IMPORTANT]
> Per completare questi passaggi, assicurarsi che nella sottoscrizione siano stati distribuiti due servizi di bilanciamento del carico a livello di area con pool back-end.  Per altre informazioni, vedere **[Guida introduttiva: creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali tramite l'interfaccia](quickstart-load-balancer-standard-public-cli.md)** della riga di comando

### <a name="add-the-regional-frontends-to-load-balancer"></a>Aggiungere i front-end regionali al servizio di bilanciamento del carico

In questa sezione verranno inseriti gli ID risorsa di due front-end di bilanciamento del carico a livello di area nelle variabili.  Si useranno quindi le variabili per aggiungere i front-end al pool di indirizzi back-end del servizio di bilanciamento del carico tra aree.

Recuperare gli ID risorsa con il comando [AZ Network lb frontend-IP Show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Usare [AZ Network Cross-Region-lb address-pool Address Add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) per aggiungere i front-end inseriti nelle variabili nel pool back-end del servizio di bilanciamento del carico tra aree:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

In questa sezione verrà testato il servizio di bilanciamento del carico tra più aree. Ci si connetterà all'indirizzo IP pubblico in un Web browser.  Si arresteranno le macchine virtuali in uno dei pool back-end del servizio di bilanciamento del carico a livello di area e si osserverà il failover.

1. Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare [AZ Network Public-IP Show](/cli/azure/network/public-ip#az-network-public-ip-show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

3. Arrestare le macchine virtuali nel pool di back-end di uno dei servizi di bilanciamento del carico a livello di area.

4. Aggiornare il Web browser e osservare il failover della connessione all'altro servizio di bilanciamento del carico a livello di area.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* È stato creato un servizio di bilanciamento del carico tra aree.
* È stata creata una regola di bilanciamento del carico.
* Sono stati aggiunti i servizi di bilanciamento del carico a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.
* È stato testato il servizio di bilanciamento del carico.

Passare all'articolo successivo per informazioni su come...
> [!div class="nextstepaction"]
> [Bilanciare il carico delle macchine virtuali tra zone di disponibilità](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
