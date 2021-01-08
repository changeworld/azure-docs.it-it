---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida di avvio rapido illustra come creare un servizio di bilanciamento del carico interno con l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 15060a367bba2d50d7054730321f7f20d4c25e46
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916678"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali mediante l'interfaccia della riga di comando di Azure

Iniziare a usare Azure Load Balancer con l'interfaccia della riga di comando di Azure per creare un servizio di bilanciamento del carico interno e tre macchine virtuali.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

* Con nome **CreateIntLBQS-rg**. 
* Nella posizione **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Risorse di Load Balancer Standard create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---standard"></a>Configurare la rete virtuale - Standard

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Creare una subnet bastion

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.1.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Creare un host bastion

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreateIntLBQS-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.


### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Creare i server back-end - Standard

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) creare tre interfacce di rete:

* Denominate **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](/cli/azure/vm#az-vm-create) creare le macchine virtuali:

* Denominate **myVM1**, **myVM2** e **myVM3**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Immagine della macchina virtuale **win2019datacenter**.
* In **Zona 1**, **Zona 2** e **Zona 3**.

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

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
* Associato alla subnet back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Le macchine virtuali nel pool back-end non avranno connettività Internet in uscita con questa configurazione. </br> Per altre informazioni su come fornire la connettività in uscita, vedere: </br> **[Connessioni in uscita in Azure](load-balancer-outbound-connections.md)**</br> Opzioni per fornire la connettività: </br> **[Configurazione del servizio di bilanciamento del carico solo in uscita](egress-only.md)** </br> **[Che cos'è NAT di rete virtuale?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Risorse di Load Balancer Basic create per l'avvio rapido." border="false":::

## <a name="configure-virtual-network---basic"></a>Configurare la rete virtuale - Basic

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Con [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) creare una rete virtuale:

* Denominata **myVNet**.
* Prefisso indirizzo **10.1.0.0/16**.
* Con una subnet denominata **myBackendSubnet**.
* Prefisso subnet **10.1.0.0/24**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) per creare un indirizzo IP pubblico per l'host bastion:

* Creare un indirizzo IP pubblico ridondante di zona standard denominato **myBastionIP**.
* In **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Creare una subnet bastion

Usare [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) per creare una subnet bastion:

* Denominata **AzureBastionSubnet**.
* Prefisso indirizzo **10.1.1.0/24**.
* Nella rete virtuale **myVNet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Creare un host bastion

Usare [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) per creare un host bastion:

* **myBastionHost** denominato.
* In **CreateIntLBQS-rg**.
* Associato all'IP pubblico **myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella località **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico standard, le macchine virtuali nell'indirizzo back-end devono avere interfacce di rete appartenenti a un gruppo di sicurezza di rete. 

Con [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) creare un gruppo di sicurezza di rete:

* Denominato **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Con [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) creare una regola del gruppo di sicurezza di rete:

* Denominata **myNSGRuleHTTP**.
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, ovvero **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Protocollo **(*)** .
* Direzione **In ingresso**.
* Origine **(*)** .
* Destinazione **(*)** .
* Porta di destinazione **Porta 80**.
* Accesso **Consenti**.
* Priorità **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Creare i server back-end - Basic

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Un set di disponibilità per le macchine virtuali.
* Tre macchine virtuali da usare come server back-end per il bilanciamento del carico.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create) creare tre interfacce di rete:

* Denominate **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Creare il set di disponibilità per le macchine virtuali

Con [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) creare il set di disponibilità:

* Denominato **myAvailabilitySet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Posizione **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Con il comando [az vm create](/cli/azure/vm#az-vm-create) creare le macchine virtuali:

* Denominate **myVM1**, **myVM2** e **myVM3**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Immagine della macchina virtuale **win2019datacenter**.
* In **myAvailabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
La distribuzione delle macchine virtuali potrebbe richiedere alcuni minuti.

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

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
* Associato alla subnet back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Aggiungere le macchine virtuali al pool back-end di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Nel pool di indirizzi back-end **myBackEndPool**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con associazione all'interfaccia di rete **myNicVM1**, **myNicVM2** e **myNicVM3**.
* Con associazione al servizio di bilanciamento del carico **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

### <a name="create-test-virtual-machine"></a>Creare una macchina virtuale di test

Creare l'interfaccia di rete con il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create):

* Denominata **myNicTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create):

* Denominata **myTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicTestVM**.
* Immagine della macchina virtuale **Win2019Datacenter**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
La distribuzione della macchina virtuale può richiedere alcuni minuti.

## <a name="install-iis"></a>Installare IIS

Usare [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) per installare IIS nelle macchine virtuali e impostare il sito Web predefinito sul nome computer.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Test

1. [Accedere](https://portal.azure.com) al portale di Azure.

2. Individuare l'indirizzo IP privato del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer**.

3. Annotare o copiare l'indirizzo accanto a **Indirizzo IP privato** nella pagina **Panoramica** di **myLoadBalancer**.

4. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myTestVM**, che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

5. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Creare un servizio di bilanciamento del carico interno standard" border="true":::
   
Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido:

* È stato creato un servizio di bilanciamento del carico standard o pubblico.
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a:
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)