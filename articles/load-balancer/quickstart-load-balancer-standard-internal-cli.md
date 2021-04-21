---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno - Interfaccia della riga di comando di Azure'
titleSuffix: Azure Load Balancer
description: Questa guida introduttiva illustra come creare un servizio di bilanciamento del carico interno usando l'interfaccia della riga di comando di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788836"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Guida introduttiva: Creare un servizio di bilanciamento del carico interno usando l'interfaccia della riga di comando di Azure

Iniziare a usare Azure Load Balancer'interfaccia della riga di comando di Azure per creare un servizio di bilanciamento del carico interno e tre macchine virtuali.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui distribuire e gestire le risorse di Azure.

Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). Assegnare al gruppo di risorse il nome **CreateIntLBQS-rg** e specificare il percorso **eastus.**

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. Il diagramma seguente illustra le risorse create in questa guida introduttiva:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Risorse di Load Balancer Standard create per l'avvio rapido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

#### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Specificare le opzioni seguenti:

* Denominato **myVNet**
* Prefisso dell'indirizzo **10.1.0.0/16**
* Subnet denominata **myBackendSubnet**
* Prefisso subnet **10.1.0.0/24**
* Nel gruppo **di risorse CreateIntLBQS-rg**
* Posizione **dell'eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create per](/cli/azure/network/public-ip#az_network_public_ip_create) creare un indirizzo IP pubblico per l Azure Bastion host. Specificare le opzioni seguenti:

* Creare un indirizzo IP pubblico con ridondanza della zona standard **denominato myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Creare una subnet Azure Bastion locale

Usare [az network vnet subnet create per](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) creare una subnet. Specificare le opzioni seguenti:

* **AzureBastionSubnet denominato**
* Prefisso dell'indirizzo **10.1.1.0/24**
* Nella rete virtuale **myVNet**
* Nel gruppo di **risorse CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Creare un host Azure Bastion

Usare [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) per creare un host. Specificare le opzioni seguenti:

* Denominato **myBastionHost**
* In **CreateIntLBQS-rg**
* Associato all'indirizzo IP **pubblico myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella **località eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico Standard, assicurarsi che le macchine virtuali abbia interfacce di rete che appartengono a un gruppo di sicurezza di rete. Creare un gruppo di sicurezza di rete [usando az network nsg create.](/cli/azure/network/nsg#az_network_nsg_create) Specificare le opzioni seguenti:

* Denominato **myNSG**
* Nel gruppo di **risorse CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Creare una regola del gruppo di sicurezza di rete [usando az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Specificare le opzioni seguenti:

* Denominato **myNSGRuleHTTP**
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, **myNSG**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Protocollo **(*)**
* Direzione **in ingresso**
* Origine **(*)**
* Destinazione **(*)**
* Porta di **destinazione 80**
* Consenti **accesso**
* Priorità **200**

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

### <a name="create-back-end-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Tre macchine virtuali da usare come server per il servizio di bilanciamento del carico.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Creare tre interfacce di rete con [az network nic create.](/cli/azure/network/nic#az_network_nic_create) Specificare le opzioni seguenti:

* Denominati **myNicVM1,** **myNicVM2** e **myNicVM3**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Nella rete virtuale **myVNet**
* Nella subnet **myBackendSubnet**
* Nel gruppo di sicurezza di rete **myNSG**

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

#### <a name="create-the-virtual-machines"></a>Creare le macchine virtuali

Creare le macchine virtuali con il comando [az vm create](/cli/azure/vm#az_vm_create). Specificare le opzioni seguenti:

* Denominati **myVM1,** **myVM2** e **myVM3**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Collegato all'interfaccia di **rete myNicVM1,** **myNicVM2** e **myNicVM3**
* Immagine di macchina virtuale **win2019datacenter**
* In **Zona 1**, **Zona 2** e **Zona 3**

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

La distribuzione delle macchine virtuali può richiedere alcuni minuti.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Un pool IP che riceve il traffico di rete in ingresso nel servizio di bilanciamento del carico.
* Un secondo pool IP, in cui il primo pool invia il traffico di rete con carico bilanciato.
* Probe di integrità che determina l'integrità delle istanze di macchina virtuale.
* Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

#### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico pubblico [con az network lb create.](/cli/azure/network/lb#az_network_lb_create) Specificare le opzioni seguenti:

* Denominato **myLoadBalancer**.
* Un pool denominato **myFrontEnd**
* Un pool denominato **myBackEndPool**
* Associato alla rete virtuale **myVNet**
* Associato alla subnet **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Creare un probe di integrità [con az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Specificare le opzioni seguenti:

* Monitora l'integrità delle macchine virtuali
* Denominato **myHealthProbe**
* Protocollo **TCP**
* Monitoraggio **della porta 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* Configurazione IP per il traffico in ingresso.
* Pool IP per ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Creare una regola di bilanciamento del carico [con az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Specificare le opzioni seguenti:

* Denominata **myHTTPRule**.
* Ascolto sulla **porta 80** nel pool **myFrontEnd**
* Invio di traffico di rete con carico bilanciato al pool **di indirizzi myBackEndPool** tramite **la porta 80** 
* Uso del probe di **integrità myHealthProbe**
* Protocollo **TCP**
* Timeout di inattività **di 15 minuti**
* Abilitare la reimpostazione TCP

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Aggiungere macchine virtuali al pool di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Specificare le opzioni seguenti:

* Nel pool di **indirizzi myBackEndPool**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Associato all'interfaccia **di rete myNicVM1,** **myNicVM2** e **myNicVM3**
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

In questa sezione verrà creato un servizio di bilanciamento del carico che bilancia il carico delle macchine virtuali. Quando si crea un servizio di bilanciamento del carico interno, occorre configurare una rete virtuale in cui eseguire il servizio. Il diagramma seguente illustra le risorse create in questa guida introduttiva:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Risorse di bilanciamento del carico di base create per l'avvio rapido." border="false":::

### <a name="configure-the-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

#### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale usando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt). Specificare le opzioni seguenti:

* Denominato **myVNet**
* Prefisso dell'indirizzo **10.1.0.0/16**
* Subnet denominata **myBackendSubnet**
* Prefisso subnet **10.1.0.0/24**
* Nel gruppo **di risorse CreateIntLBQS-rg**
* Posizione **dell'eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Usare [az network public-ip create per](/cli/azure/network/public-ip#az_network_public_ip_create) creare un indirizzo IP pubblico per l Azure Bastion host. Specificare le opzioni seguenti:

* Creare un indirizzo IP pubblico con ridondanza della zona standard **denominato myBastionIP**
* In **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Creare una subnet Azure Bastion locale

Usare [az network vnet subnet create per](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) creare una subnet. Specificare le opzioni seguenti:

* **AzureBastionSubnet denominato**
* Prefisso dell'indirizzo **10.1.1.0/24**
* Nella rete virtuale **myVNet**
* Nel gruppo di **risorse CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Creare un host Azure Bastion

Usare [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) per creare un host. Specificare le opzioni seguenti:

* Denominato **myBastionHost**
* In **CreateIntLBQS-rg**
* Associato all'indirizzo IP **pubblico myBastionIP**
* Associato alla rete virtuale **myVNet**
* Nella **località eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Per un servizio di bilanciamento del carico Standard, assicurarsi che le macchine virtuali abbia interfacce di rete che appartengono a un gruppo di sicurezza di rete. Creare un gruppo di sicurezza di rete [usando az network nsg create.](/cli/azure/network/nsg#az_network_nsg_create) Specificare le opzioni seguenti:

* Denominato **myNSG**
* Nel gruppo di **risorse CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Creare una regola del gruppo di sicurezza di rete

Creare una regola del gruppo di sicurezza di rete [usando az network nsg rule create.](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) Specificare le opzioni seguenti:

* Denominato **myNSGRuleHTTP**
* Nel gruppo di sicurezza di rete creato nel passaggio precedente, **myNSG**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Protocollo **(*)**
* Direzione **in ingresso**
* Origine **(*)**
* Destinazione **(*)**
* Porta di **destinazione 80**
* Consenti **accesso**
* Priorità **200**

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

### <a name="create-back-end-servers"></a>Creare i server back-end

In questa sezione verranno creati:

* Tre interfacce di rete per le macchine virtuali.
* Set di disponibilità per le macchine virtuali.
* Tre macchine virtuali da usare come server per il servizio di bilanciamento del carico.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Creare le interfacce di rete per le macchine virtuali

Creare tre interfacce di rete con [az network nic create.](/cli/azure/network/nic#az_network_nic_create) Specificare le opzioni seguenti:

* Denominati **myNicVM1,** **myNicVM2** e **myNicVM3**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Nella rete virtuale **myVNet**
* Nella subnet **myBackendSubnet**
* Nel gruppo di sicurezza di rete **myNSG**

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Creare il set di disponibilità per le macchine virtuali

Creare il set di disponibilità con [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). Specificare le opzioni seguenti:

* Denominato **myAvailabilitySet**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Località **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Creare le macchine virtuali

Creare le macchine virtuali con il comando [az vm create](/cli/azure/vm#az_vm_create). Specificare le opzioni seguenti:

* Denominati **myVM1,** **myVM2** e **myVM3**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Collegato all'interfaccia di **rete myNicVM1,** **myNicVM2** e **myNicVM3**
* Immagine di macchina **virtuale win2019datacenter**
* In **myAvailabilitySet**


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
La distribuzione delle macchine virtuali può richiedere alcuni minuti.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Pool IP che riceve il traffico di rete in ingresso nel servizio di bilanciamento del carico.
* Un secondo pool IP, in cui il primo pool invia il traffico di rete con carico bilanciato.
* Probe di integrità che determina l'integrità delle istanze di macchina virtuale.
* Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

#### <a name="create-the-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico pubblico [con az network lb create](/cli/azure/network/lb#az_network_lb_create). Specificare le opzioni seguenti:

* Denominato **myLoadBalancer**.
* Un pool denominato **myFrontEnd**
* Un pool denominato **myBackEndPool**
* Associato alla rete virtuale **myVNet**
* Associato alla subnet **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando il problema viene risolto.

Creare un probe di integrità con [az network lb probe create.](/cli/azure/network/lb/probe#az_network_lb_probe_create) Specificare le opzioni seguenti:

* Monitora l'integrità delle macchine virtuali
* Denominato **myHealthProbe**
* Protocollo **TCP**
* Monitoraggio **della porta 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* Configurazione IP per il traffico in ingresso.
* Pool IP per ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Creare una regola di bilanciamento del carico [con az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Specificare le opzioni seguenti:

* Denominata **myHTTPRule**.
* In ascolto **sulla porta 80** nel pool **myFrontEnd**
* Invio del traffico di rete con carico bilanciato al pool di **indirizzi myBackEndPool** tramite **la porta 80** 
* Uso del probe di **integrità myHealthProbe**
* Protocollo **TCP**
* Timeout di inattività **di 15 minuti**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Aggiungere macchine virtuali al pool di bilanciamento del carico

Aggiungere le macchine virtuali al pool back-end con [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Specificare le opzioni seguenti:

* Nel pool di **indirizzi myBackEndPool**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Associato all'interfaccia **di rete myNicVM1,** **myNicVM2** e **myNicVM3**
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
## <a name="test-the-load-balancer"></a>Testare il bilanciamento del carico

Creare l'interfaccia di rete [con az network nic create](/cli/azure/network/nic#az_network_nic_create). Specificare le opzioni seguenti:

* Denominato **myNicTestVM**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Nella rete virtuale **myVNet**
* Nella subnet **myBackendSubnet**
* Nel gruppo di sicurezza di rete **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az_vm_create). Specificare le opzioni seguenti:

* **MyTestVM denominato**
* Nel gruppo di **risorse CreateIntLBQS-rg**
* Collegato all'interfaccia di rete **myNicTestVM**
* Immagine di macchina **virtuale Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Potrebbe essere necessario attendere alcuni minuti per la distribuzione della macchina virtuale.

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

2. Nella pagina **Panoramica** trovare l'indirizzo IP privato per il servizio di bilanciamento del carico. Nel menu a sinistra selezionare **Tutti** i servizi Tutte le  >  **risorse**  >  **myLoadBalancer**.

3. Nella panoramica di **myLoadBalancer** copiare l'indirizzo accanto a **Indirizzo IP privato**.

4. Nel menu a sinistra selezionare Tutti **i servizi** Tutte  >  **le risorse.** Nell'elenco delle risorse, nel gruppo di risorse **CreateIntLBQS-rg** selezionare **myTestVM**.

5. Nella pagina **Panoramica** selezionare **Connetti**  >  **Bastion**.

6. Immettere il nome utente e la password immessi al momento della creazione della macchina virtuale.

7. In **myTestVM** aprire **Internet Explorer**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. La pagina predefinita del server Web IIS viene visualizzata nel browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Screenshot dell'indirizzo IP nella barra degli indirizzi del browser." border="true":::
   
Per vedere il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre le macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ogni macchina virtuale. Aggiornare quindi manualmente il Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse non sono più necessarie, usare il [comando az group delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

È disponibile una panoramica di Azure Load Balancer.
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)
