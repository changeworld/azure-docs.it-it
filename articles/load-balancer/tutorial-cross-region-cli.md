---
title: "Esercitazione: Creare un servizio di bilanciamento del carico tra aree usando l'interfaccia della riga di comando di Azure"
titleSuffix: Azure Load Balancer
description: Introduzione a questa esercitazione sulla distribuzione di un ambiente tra aree Azure Load Balancer'interfaccia della riga di comando di Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791914"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Esercitazione: Creare un'interfaccia della riga di comando tra aree Azure Load Balancer'interfaccia della riga di comando di Azure

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
    - Per informazioni sulla creazione di un servizio di bilanciamento del carico standard a livello di regione e di macchine virtuali per i pool back-end, vedere Avvio rapido: Creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali usando l'interfaccia della riga di [comando di Azure.](quickstart-load-balancer-standard-public-cli.md)
        - Aggiungere il nome dei servizi di bilanciamento del carico e delle macchine virtuali in ogni area con **-R1** e **-R2.** 
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere all'interfaccia della riga di comando di Azure:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Creare un servizio di bilanciamento del carico tra aree

In questa sezione si creerà un servizio di bilanciamento del carico tra aree, un indirizzo IP pubblico e una regola di bilanciamento del carico.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

* Denominato **myResourceGroupLB-CR.**
* Nella posizione **westus.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico tra aree [con az network cross-region-lb create:](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)

* Denominato **myLoadBalancer-CR.**
* Un pool front-end denominato **myFrontEnd-CR.**
* Un pool back-end **denominato myBackEndPool-CR.**

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

Creare una regola di bilanciamento del carico [con az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Denominato **myHTTPRule-CR**
* Ascolto sulla **porta 80** nel pool front-end **myFrontEnd-CR**.
* Invio del traffico di rete con bilanciamento del carico al pool di indirizzi back-end **myBackEndPool-CR** **tramite la porta 80.** 
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
> Per completare questi passaggi, assicurarsi che nella sottoscrizione siano stati distribuiti due servizi di bilanciamento del carico a livello di area con pool back-end.  Per altre informazioni, vedere Avvio rapido: Creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine **[virtuali usando l'interfaccia della riga di comando di Azure.](quickstart-load-balancer-standard-public-cli.md)**

### <a name="add-the-regional-frontends-to-load-balancer"></a>Aggiungere i front-end regionali al servizio di bilanciamento del carico

In questa sezione si inseriranno gli ID risorsa di due front-end di bilanciamento del carico a livello di regione nelle variabili.  Si useranno quindi le variabili per aggiungere i front-end al pool di indirizzi back-end del servizio di bilanciamento del carico tra aree.

Recuperare gli ID risorsa con [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Usare [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) per aggiungere i front-end inseriti nelle variabili nel pool back-end del servizio di bilanciamento del carico tra aree:

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

1. Per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico, usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

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

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az_group_delete).

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
