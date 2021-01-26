---
title: Aggiornare o eliminare le istanze esistenti di Azure Load Balancer usate da un set di scalabilità di macchine virtuali
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Questo articolo illustra come iniziare a usare Azure Load Balancer Standard e i set di scalabilità di macchine virtuali.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790090"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Come aggiornare/eliminare Azure Load Balancer usati dai set di scalabilità di macchine virtuali

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Come configurare Azure Load Balancer per la scalabilità orizzontale di set di scalabilità di macchine virtuali
  * Verificare che il Load Balancer disponga di un [pool NAT in ingresso](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) configurato e che il set di scalabilità di macchine virtuali sia inserito nel pool back-end del Load Balancer. Azure Load Balancer creerà automaticamente nuove regole NAT in ingresso nel pool NAT in ingresso quando vengono aggiunte nuove istanze di macchine virtuali al set di scalabilità di macchine virtuali. 
  * Per verificare se il pool NAT in ingresso è configurato correttamente, 
  1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
  
  1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
  
  1. In **Impostazioni** selezionare **regole NAT in ingresso**.
Se nel riquadro destro viene visualizzato un elenco di regole create per ogni singola istanza nel set di scalabilità di macchine virtuali, i contorni sono tutti impostati per la scalabilità verticale in qualsiasi momento.

## <a name="how-to-add-inbound-nat-rules"></a>Come aggiungere le regole NAT in ingresso? 
  * Non è possibile aggiungere una singola regola NAT in ingresso. Tuttavia, è possibile aggiungere un set di regole NAT in ingresso con l'intervallo di porte front-end definito e la porta back-end per tutte le istanze nel set di scalabilità di macchine virtuali.
  * Per aggiungere un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, è necessario prima creare un pool NAT in ingresso nella Load Balancer e quindi fare riferimento al pool NAT in ingresso dal profilo di rete del set di scalabilità di macchine virtuali. Di seguito è riportato un esempio completo di utilizzo dell'interfaccia della riga di comando.
  * Il nuovo pool NAT in ingresso non deve avere un intervallo di porte front-end sovrapposto con i pool NAT in ingresso esistenti. Per visualizzare i pool NAT in ingresso esistenti impostati, è possibile usare questo [comando dell'interfaccia della riga di comando](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Come aggiornare le regole NAT in ingresso? 
  * Non è possibile aggiornare la singola regola NAT in ingresso. Tuttavia, è possibile aggiornare un set di regole NAT in ingresso con l'intervallo di porte front-end definito e la porta back-end per tutte le istanze nel set di scalabilità di macchine virtuali.
  * Per aggiornare un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, è necessario aggiornare il pool NAT in ingresso nella Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Come eliminare le regole NAT in ingresso? 
* Non è possibile eliminare una singola regola NAT in ingresso. Tuttavia, è possibile eliminare l'intero set di regole NAT in ingresso.
* Per eliminare l'intero set di regole NAT in ingresso usate dal set di scalabilità, è necessario prima rimuovere il pool NAT dal set di scalabilità. Di seguito è riportato un esempio completo di utilizzo dell'interfaccia della riga di comando:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Come aggiungere più configurazioni IP:
1. Selezionare **Tutte le risorse** dal menu a sinistra e quindi selezionare **MyLoadBalancer** dall'elenco delle risorse.
   
1. In **Impostazioni** selezionare **configurazioni IP** front-end, quindi selezionare **Aggiungi**.
   
1. Nella pagina **Aggiungi indirizzo IP** front-end digitare i valori e selezionare **OK** .

1. Se sono necessarie nuove regole di bilanciamento del carico, seguire i [passaggi 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) di questa esercitazione

1. Se necessario, creare un nuovo set di regole NAT in ingresso usando le configurazioni IP front-end appena create. L'esempio è disponibile nella [sezione precedente].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Come eliminare la configurazione IP front-end usata dal set di scalabilità di macchine virtuali: 
 1. Per eliminare la configurazione IP front-end usata dal set di scalabilità, è necessario prima eliminare il pool NAT in ingresso (set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni su come eliminare le regole in ingresso sono disponibili nella sezione precedente.
 1. Eliminare la regola di bilanciamento del carico che fa riferimento alla configurazione IP front-end. 
 1. Eliminare la configurazione IP front-end.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Come eliminare Azure Load Balancer usati dal set di scalabilità di macchine virtuali: 
 1. Per eliminare la configurazione IP front-end usata dal set di scalabilità, è necessario prima eliminare il pool NAT in ingresso (set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni su come eliminare le regole in ingresso sono disponibili nella sezione precedente.
 
 1. Eliminare la regola di bilanciamento del carico facendo riferimento al pool back-end contenente il set di scalabilità di macchine virtuali.
 
 1. Rimuovere il riferimento loadBalancerBackendAddressPool dal profilo di rete del set di scalabilità di macchine virtuali. Di seguito è riportato un esempio completo di utilizzo dell'interfaccia della riga di comando:
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Infine, eliminare la risorsa Load Balancer.
 
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Load Balancer e sul set di scalabilità di macchine virtuali, vedere altre informazioni sui concetti.

> [Azure Load Balancer con i set di scalabilità di macchine virtuali di Azure](load-balancer-standard-virtual-machine-scale-sets.md)