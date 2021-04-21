---
title: Bilanciare il carico di più siti Web - Interfaccia della riga di comando di Azure - Azure Load Balancer
description: Questo esempio di script dell'interfaccia della riga di comando di Azure illustra come eseguire il bilanciamento del carico di più siti Web nella stessa macchina virtuale
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77dfc42164419d86e174bc47297e2f596e757ffe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790096"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Esempio di script dell'interfaccia della riga di comando di Azure: bilanciare il carico di più siti Web

Questo esempio di script dell'interfaccia della riga di comando di Azure crea una rete virtuale con due macchine virtuali che fanno parte di un set di disponibilità. Il bilanciamento del carico indirizza il traffico di due diversi indirizzi IP verso le due macchine virtuali. Dopo l'esecuzione dello script, è possibile distribuire il software del server Web alle macchine virtuali e ospitare più siti Web, ciascuno con il suo indirizzo IP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Consente di creare una rete virtuale e una subnet di Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Consente di creare un indirizzo IP pubblico con un indirizzo IP statico e un nome DNS associato. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Crea un servizio di bilanciamento del carico di Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Crea un probe di bilanciamento del carico. Il probe di bilanciamento del carico viene usato per monitorare tutte le macchine virtuali nel set di bilanciamento del carico. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Crea una regola di bilanciamento del carico. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al bilanciamento del carico, viene indirizzato sulla porta 80 di una delle macchine virtuali presenti nel set di bilanciamento del carico. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Crea un indirizzo IP di front-end per il servizio di bilanciamento del carico. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Crea un pool di indirizzi back-end. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Crea una scheda di rete virtuale e la collega alla rete virtuale e alla subnet. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Consente di creare un set di disponibilità. I set di disponibilità garantiscono il tempo di attività dell'applicazione suddividendo le macchine virtuali in risorse fisiche in modo tale che, in caso di errore, non venga interessato l'intero set. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Crea una configurazione IP. È necessario che la funzionalità Microsoft.Network/AllowMultipleIpConfigurationsPerNic sia abilitata per la sottoscrizione. È possibile designare una sola configurazione come configurazione IP primaria per scheda di rete, usando il flag --make-primary. |
| [az vm create](/cli/azure/vm/availability-set#az_vm_availability_set_create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).