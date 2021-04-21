---
title: Configurare l'indirizzo IP in uscita statico
description: Configurare il firewall di Azure e le route definite dall'utente per Istanze di Azure Container che usano l'indirizzo IP pubblico del firewall per il traffico in ingresso e in uscita
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 1cd0ff48da58706a1be59caf4b9d5974dc5f552a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790816"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Configurare un singolo indirizzo IP pubblico per il traffico in uscita e in ingresso verso un gruppo di contenitori

La configurazione di [un gruppo di contenitori](container-instances-container-groups.md) con un indirizzo IP esterno consente ai client esterni di usare l'indirizzo IP per accedere a un contenitore nel gruppo. Ad esempio, un browser può accedere a un'app Web in esecuzione in un contenitore. Tuttavia, attualmente un gruppo di contenitori usa un indirizzo IP diverso per il traffico in uscita. Questo indirizzo IP in uscita non viene esposto a livello di codice, quindi il monitoraggio e la configurazione dei gruppi di contenitori delle regole del firewall client sono più complesse.

Questo articolo illustra i passaggi per configurare un gruppo di contenitori in [una rete virtuale](container-instances-virtual-network-concepts.md) integrata con [Firewall di Azure](../firewall/overview.md). Configurando una route definita dall'utente per il gruppo di contenitori e le regole del firewall, è possibile instradare e identificare il traffico da e verso il gruppo di contenitori. I dati in ingresso e in uscita del gruppo di contenitori usano l'indirizzo IP pubblico del firewall. Un singolo indirizzo IP in uscita può essere usato da più gruppi di contenitori distribuiti nella subnet della rete virtuale delegata a Istanze di Azure Container.

In questo articolo si usa l'interfaccia della riga di comando di Azure per creare le risorse per questo scenario:

* Gruppi di contenitori distribuiti in una subnet [delegata nella rete virtuale](container-instances-vnet.md) 
* Un firewall di Azure distribuito nella rete con un indirizzo IP pubblico statico
* Una route definita dall'utente nella subnet dei gruppi di contenitori
* Una regola NAT per l'ingresso del firewall e una regola dell'applicazione per l'uscita

È quindi possibile convalidare l'ingresso e l'uscita da gruppi di contenitori di esempio attraverso il firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>Distribuire ACI in una rete virtuale

In un caso tipico potrebbe essere già presente una rete virtuale di Azure in cui distribuire un gruppo di contenitori. A scopo dimostrativo, i comandi seguenti creano una rete virtuale e una subnet quando viene creato il gruppo di contenitori. La subnet viene delegata a Istanze di Azure Container. 

Il gruppo di contenitori esegue una piccola app Web `aci-helloworld` dall'immagine. Come illustrato in altri articoli della documentazione, questa immagine contiene una piccola app Web scritta in Node.js che serve una pagina HTML statica.

Se necessario, creare prima un gruppo di risorse di Azure con [il comando az group create.][az-group-create] Ad esempio:

```azurecli
az group create --name myResourceGroup --location eastus
```

Per semplificare gli esempi di comando seguenti, usare una variabile di ambiente per il nome del gruppo di risorse:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Creare il gruppo di contenitori con [il comando az container create:][az-container-create]

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Modificare il valore `--subnet address-prefix` di per lo spazio indirizzi IP necessario nella subnet. La subnet più piccola supportata è /29, che fornisce otto indirizzi IP. Alcuni indirizzi IP sono riservati per l'uso da parte di Azure.

Per usarlo in un passaggio successivo, ottenere l'indirizzo IP privato del gruppo di contenitori eseguendo il comando [az container show][az-container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Distribuire Firewall di Azure in rete

Nelle sezioni seguenti usare l'interfaccia della riga di comando di Azure per distribuire un firewall di Azure nella rete virtuale. Per informazioni di base, [vedere Tutorial: Deploy and configure Firewall di Azure using the portale di Azure](../firewall/deploy-cli.md).

Per prima cosa, usare [az network vnet subnet create per][az-network-vnet-subnet-create] aggiungere una subnet denominata AzureFirewallSubnet per il firewall. AzureFirewallSubnet è il *nome* obbligatorio di questa subnet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Usare i comandi [dell'interfaccia della riga di](../firewall/deploy-cli.md) comando di Azure seguenti per creare un firewall nella subnet.

Se non è già installato, aggiungere l'estensione del firewall all'interfaccia della riga di comando di Azure usando [il comando az extension add:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

Creare le risorse del firewall:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Aggiornare la configurazione del firewall usando il [comando az network firewall update:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Ottenere l'indirizzo IP privato del firewall usando [il comando az network firewall ip-config list.][az-network-firewall-ip-config-list] Questo indirizzo IP privato viene usato in un comando successivo.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Ottenere l'indirizzo IP pubblico del firewall usando [il comando az network public-ip show.][az-network-public-ip-show] Questo indirizzo IP pubblico viene usato in un comando successivo.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definire una route definita dall'utente nella subnet ACI

Definire una route definita dall'uso nella subnet ACI per deviare il traffico verso il firewall di Azure. Per altre informazioni, vedere [Instradare il traffico di rete.](../virtual-network/tutorial-create-route-table-cli.md) 

### <a name="create-route-table"></a>Creare una tabella di route

Eseguire prima di tutto il [comando az network route-table create][az-network-route-table-create] per creare la tabella di route. Creare la tabella di route nella stessa area della rete virtuale.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Creare una route

Eseguire [az network-route-table route create][az-network-route-table-route-create] per creare una route nella tabella di route. Per instradare il traffico al firewall, impostare il tipo di hop successivo su e passare l'indirizzo IP privato del `VirtualAppliance` firewall come indirizzo hop successivo.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Associare la tabella di route alla subnet ACI

Eseguire il [comando az network vnet subnet update][az-network-vnet-subnet-update] per associare la tabella di route alla subnet delegata a Istanze di Azure Container.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Configurare le regole nel firewall

Per impostazione predefinita, Firewall di Azure nega (blocca) il traffico in ingresso e in uscita. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Configurare la regola NAT nel firewall per la subnet ACI

Creare una [regola NAT nel](../firewall/rule-processing.md) firewall per convertire e filtrare il traffico Internet in ingresso nel contenitore di applicazioni avviato in precedenza nella rete. Per informazioni dettagliate, vedere [Filtrare il traffico Internet in ingresso Firewall di Azure DNAT](../firewall/tutorial-firewall-dnat.md)

Creare una regola NAT e una raccolta usando il [comando az network firewall nat-rule create:][az-network-firewall-nat-rule-create]

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Aggiungere le regole NAT necessarie per filtrare il traffico verso altri indirizzi IP nella subnet. Ad esempio, altri gruppi di contenitori nella subnet potrebbero esporre gli indirizzi IP per il traffico in ingresso o altri indirizzi IP interni potrebbero essere assegnati al gruppo di contenitori dopo un riavvio.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Creare una regola dell'applicazione in uscita nel firewall

Eseguire il comando [az network firewall application-rule create][az-network-firewall-application-rule-create] seguente per creare una regola in uscita nel firewall. Questa regola di esempio consente l'accesso dalla subnet delegata Istanze di Azure Container all'FQDN `checkip.dyndns.org` . L'accesso HTTP al sito viene usato in un passaggio successivo per confermare l'indirizzo IP in uscita Istanze di Azure Container.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Accesso al gruppo di test container tramite il firewall

Le sezioni seguenti verificano che la subnet delegata Istanze di Azure Container sia configurata correttamente dietro il firewall di Azure. I passaggi precedenti hanno instradato sia il traffico in ingresso verso la subnet che il traffico in uscita dalla subnet attraverso il firewall.

### <a name="test-ingress-to-a-container-group"></a>Testare l'ingresso in un gruppo di contenitori

Testare l'accesso in ingresso *all'appcontainer* in esecuzione nella rete virtuale accedendo all'indirizzo IP pubblico del firewall. In precedenza, l'indirizzo IP pubblico è stato archiviato nella variabile $FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

L'output è simile a:

```console
52.142.18.133
```

Se la regola NAT nel firewall è configurata correttamente, quando si immette l'indirizzo IP pubblico del firewall nel browser viene visualizzato quanto segue:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Passare all'indirizzo IP pubblico del firewall":::

### <a name="test-egress-from-a-container-group"></a>Testare l'uscita da un gruppo di contenitori


Distribuire il contenitore di esempio seguente nella rete virtuale. Quando viene eseguito, invia una singola richiesta HTTP a , che visualizza l'indirizzo IP del mittente `http://checkip.dyndns.org` (l'indirizzo IP in uscita). Se la regola dell'applicazione nel firewall è configurata correttamente, viene restituito l'indirizzo IP pubblico del firewall.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Visualizzare i log del contenitore per verificare che l'indirizzo IP sia lo stesso dell'indirizzo IP pubblico del firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

L'output è simile a:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo vengono impostati gruppi di contenitori in una rete virtuale dietro un firewall di Azure. Sono stati configurati una route definita dall'utente e le regole NAT e dell'applicazione nel firewall. Usando questa configurazione, si configura un singolo indirizzo IP statico per l'ingresso e l'uscita da Istanze di Azure Container.

Per altre informazioni sulla gestione del traffico e sulla protezione delle risorse di Azure, vedere la [documentazione Firewall di Azure](../firewall/index.yml) sicurezza.



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create
