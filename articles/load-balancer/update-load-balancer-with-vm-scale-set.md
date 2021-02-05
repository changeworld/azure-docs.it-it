---
title: Aggiornare o eliminare un servizio di bilanciamento del carico esistente usato dai set di scalabilità di macchine virtuali
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
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
ms.openlocfilehash: 0c491275f793ce2cd5e830ca6a3014dc45d6d509
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594536"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aggiornare o eliminare un servizio di bilanciamento del carico usato dai set di scalabilità di macchine virtuali

Quando si lavora con i set di scalabilità di macchine virtuali e un'istanza di Azure Load Balancer, è possibile:

- Aggiungere, aggiornare ed eliminare regole.
- Aggiungere configurazioni.
- Eliminare il servizio di bilanciamento del carico.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Configurare un servizio di bilanciamento del carico per la scalabilità orizzontale di set di scalabilità di macchine virtuali

Verificare che l'istanza di Azure Load Balancer disponga di un [pool NAT in ingresso](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) configurato e che il set di scalabilità di macchine virtuali sia inserito nel pool back-end del servizio di bilanciamento del carico. Load Balancer creerà automaticamente nuove regole NAT in ingresso nel pool NAT in ingresso quando vengono aggiunte nuove istanze di macchine virtuali al set di scalabilità di macchine virtuali.

Per verificare se il pool NAT in ingresso è configurato correttamente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu a sinistra selezionare **Tutte le risorse**. Quindi selezionare **MyLoadBalancer** nell'elenco delle risorse.
1. In **Impostazioni** selezionare **regole NAT in ingresso**. Nel riquadro di destra, se viene visualizzato un elenco di regole create per ogni singola istanza nel set di scalabilità di macchine virtuali, è possibile scegliere di eseguire la scalabilità verticale in qualsiasi momento.

## <a name="add-inbound-nat-rules"></a>Aggiungere le regole NAT in ingresso

Non è possibile aggiungere singole regole NAT in ingresso. È tuttavia possibile aggiungere un set di regole NAT in ingresso con l'intervallo di porte front-end definito e la porta back-end per tutte le istanze nel set di scalabilità di macchine virtuali.

Per aggiungere un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, creare prima un pool NAT in ingresso nel servizio di bilanciamento del carico. Fare quindi riferimento al pool NAT in ingresso dal profilo di rete del set di scalabilità di macchine virtuali. Viene visualizzato un esempio completo di utilizzo dell'interfaccia della riga di comando.

Il nuovo pool NAT in ingresso non deve avere un intervallo di porte front-end sovrapposto con i pool NAT in ingresso esistenti. Per visualizzare i pool NAT in ingresso esistenti configurati, usare il [comando dell'interfaccia della riga di comando](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list):
  
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
## <a name="update-inbound-nat-rules"></a>Aggiornare le regole NAT in ingresso

Non è possibile aggiornare le singole regole NAT in ingresso. È tuttavia possibile aggiornare un set di regole NAT in ingresso con un intervallo di porte front-end definito e una porta back-end per tutte le istanze nel set di scalabilità di macchine virtuali.

Per aggiornare un intero set di regole NAT in ingresso per i set di scalabilità di macchine virtuali, aggiornare il pool NAT in ingresso nel servizio di bilanciamento del carico.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Eliminare le regole NAT in ingresso

Le regole NAT in ingresso singole non possono essere eliminate, ma è possibile eliminare l'intero set di regole NAT in ingresso.

Per eliminare l'intero set di regole NAT in ingresso usate dal set di scalabilità, rimuovere innanzitutto il pool NAT dal set di scalabilità. Di seguito è riportato un esempio completo di utilizzo dell'interfaccia della riga di comando:
    
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

## <a name="add-multiple-ip-configurations"></a>Aggiungere più configurazioni IP

Per aggiungere più configurazioni IP:

1. Nel menu a sinistra selezionare **Tutte le risorse**. Quindi selezionare **MyLoadBalancer** nell'elenco delle risorse.
1. In **Impostazioni** selezionare **Configurazione IP front-end**. Quindi selezionare **Aggiungi**.
1. Nella pagina **Aggiungi indirizzo IP** front-end immettere i valori e fare clic su **OK**.
1. Se sono necessarie nuove regole di bilanciamento del carico, seguire i [passaggi 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) in questa esercitazione.
1. Se necessario, creare un nuovo set di regole NAT in ingresso usando le configurazioni IP front-end appena create. Un esempio è disponibile nella sezione precedente.

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Eliminare la configurazione IP front-end usata dal set di scalabilità di macchine virtuali

Per eliminare la configurazione IP front-end usata dal set di scalabilità:

 1. Eliminare prima di tutto il pool NAT in ingresso (il set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni per l'eliminazione delle regole in ingresso sono disponibili nella sezione precedente.
 1. Eliminare la regola di bilanciamento del carico che fa riferimento alla configurazione IP front-end.
 1. Eliminare la configurazione IP front-end.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Eliminare un servizio di bilanciamento del carico usato da un set di scalabilità di macchine virtuali

Per eliminare la configurazione IP front-end usata dal set di scalabilità:

 1. Eliminare prima di tutto il pool NAT in ingresso (il set di regole NAT in ingresso) che fa riferimento alla configurazione IP front-end. Le istruzioni per l'eliminazione delle regole in ingresso sono disponibili nella sezione precedente.
 1. Eliminare la regola di bilanciamento del carico che fa riferimento al pool back-end che contiene il set di scalabilità di macchine virtuali.
 1. Rimuovere il `loadBalancerBackendAddressPool` riferimento dal profilo di rete del set di scalabilità di macchine virtuali.
 
 Di seguito è riportato un esempio completo di utilizzo dell'interfaccia della riga di comando:

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
Infine, eliminare la risorsa del servizio di bilanciamento del carico.
 
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui set di scalabilità di macchine virtuali e Azure Load Balancer, vedere altre informazioni sui concetti.

> [Azure Load Balancer con i set di scalabilità di macchine virtuali](load-balancer-standard-virtual-machine-scale-sets.md)