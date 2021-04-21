---
title: Creare, modificare o eliminare una tabella di route di Azure
titlesuffix: Azure Virtual Network
description: Informazioni su dove trovare informazioni sul routing del traffico di rete virtuale e su come creare, modificare o eliminare una tabella di route.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783378"
---
# <a name="create-change-or-delete-a-route-table"></a>Creare, modificare o eliminare una tabella di route

Azure effettua il routing automatico del traffico tra subnet di Azure, reti virtuali e reti locali. Per modificare il routing predefinito di Azure è necessario creare una tabella di route. Se non si ha la funzionalità di routing nelle reti virtuali, è possibile ottenere altre informazioni sul [routing](virtual-networks-udr-overview.md) del traffico di rete virtuale o completando [un'esercitazione.](tutorial-create-route-table-portal.md)

## <a name="before-you-begin"></a>Prima di iniziare

Se non se ne ha uno, configurare un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Completare quindi una di queste attività prima di iniziare i passaggi in qualsiasi sezione di questo articolo:

- **Utenti del portale:** accedere alla [portale di Azure](https://portal.azure.com) con l'account Azure.

- **Utenti di PowerShell:** eseguire i comandi nel [Azure Cloud Shell](https://shell.azure.com/powershell)o eseguire PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Nella scheda Azure Cloud Shell browser individuare l'elenco a discesa Seleziona ambiente e quindi scegliere **PowerShell** se non è già selezionato. 

    Se si esegue PowerShell in locale, usare Azure PowerShell modulo versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az.Network` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Eseguire anche `Connect-AzAccount` per creare una connessione con Azure.

- **Utenti dell'interfaccia della** riga di comando di Azure: eseguire i comandi nel [Azure Cloud Shell](https://shell.azure.com/bash)o eseguire l'interfaccia della riga di comando dal computer. Usare l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva se si esegue l'interfaccia della riga di comando di Azure in locale. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Eseguire anche `az login` per creare una connessione con Azure.

L'account a cui si accede o con [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cui ci si connette ad Azure deve essere assegnato al ruolo Collaboratore alla rete o [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) un ruolo personalizzato a cui sono assegnate le azioni appropriate elencate in [Autorizzazioni](#permissions).

## <a name="create-a-route-table"></a>Creare una tabella di route

È previsto un limite al numero di tabelle di route che è possibile creare per ogni località e sottoscrizione di Azure. Per informazioni dettagliate, vedere [Limiti di rete - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella casella di ricerca immettere *Tabella di route*. Selezionare **Tabella di route** quando viene visualizzata nei risultati della ricerca.

1. Nella pagina **Tabella di route** selezionare **Crea**.

1. Nella finestra **di dialogo Crea tabella di** route:

    1. Immettere un **nome** per la tabella di route.
    1. Scegliere la propria **sottoscrizione**.
    1. Scegliere un gruppo **di risorse esistente** o selezionare Crea **nuovo** per creare un nuovo gruppo di risorse.
    1. Scegliere una **Posizione**.
    1. Se si prevede di associare la tabella di route a una subnet in una rete virtuale connessa alla rete locale tramite un gateway VPN e non si vogliono propagare le route locali alle interfacce di rete nella subnet, impostare Propagazione route **gateway** di rete virtuale su **Disabilitato.**

1. Selezionare **Crea** per creare la nuova tabella di route.

### <a name="create-route-table---commands"></a>Creare una tabella di route : comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Visualizzare tabelle di route

Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**. Vengono elencate le tabelle di route presenti nella sottoscrizione.

### <a name="view-route-table---commands"></a>Visualizzare la tabella di route : comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table list](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Visualizzare i dettagli di una tabella di route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route per cui si vogliono visualizzare i dettagli.

1. Nella pagina della tabella di route, in **Impostazioni**, visualizzare **le** route nella tabella di route o le **subnet** a cui è associata la tabella di route.

Per altre informazioni sulle impostazioni comuni di Azure, vedere le informazioni seguenti:

- [Log attività](../azure-monitor/essentials/platform-logs-overview.md)
- [Controllo di accesso (IAM)](../role-based-access-control/overview.md)
- [Tag](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Locks](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script di automazione](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visualizzare i dettagli della tabella di route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Modificare una tabella di route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route che si vuole modificare.

Le modifiche più [](#create-a-route) comuni sono l'aggiunta [di](#delete-a-route) route, la rimozione [delle](#associate-a-route-table-to-a-subnet) route, l'associazione di tabelle di route alle subnet o la [dissociazione](#dissociate-a-route-table-from-a-subnet) delle tabelle di route dalle subnet.

### <a name="change-a-route-table---commands"></a>Modificare una tabella di route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Facoltativamente, è possibile associare una tabella di route a una subnet. Una tabella di route può essere associata a zero o più subnet. Poiché le tabelle di route non sono associate alle reti virtuali, è necessario associare una tabella di route a ogni subnet a cui si vuole associare la tabella di route. Azure instrada tutto il traffico in uscita dalla subnet in base alle route create all'interno di tabelle di [route,](virtual-networks-udr-overview.md#default)route predefinite e route propagate da una rete locale, se la rete virtuale è connessa a un gateway di rete virtuale di Azure (ExpressRoute o VPN). È possibile associare solo una tabella di route alle subnet delle reti virtuali presenti nella stessa località e sottoscrizione di Azure della tabella di route.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

1. Nell'elenco della rete virtuale scegliere la rete virtuale che contiene la subnet a cui si vuole associare una tabella di route.

1. Nella barra dei menu della rete virtuale scegliere **Subnet.**

1. Selezionare la subnet a cui si vuole associare la tabella di route.

1. In **Tabella di** route scegliere la tabella di route da associare alla subnet.

1. Selezionare **Salva**.

Se la rete virtuale è connessa a un gateway VPN di Azure, non associare una tabella di route alla [subnet del gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) che include una route con una destinazione *0.0.0.0/0.* Ciò potrebbe impedire il corretto funzionamento del gateway. Per altre informazioni sull'uso *di 0.0.0.0/0* in una route, vedere [Routing del traffico di rete virtuale.](virtual-networks-udr-overview.md#default-route)

### <a name="associate-a-route-table---commands"></a>Associare una tabella di route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Annullare l'associazione di una tabella di route da una subnet

Quando si annulla l'associazione di una tabella di route da una subnet, Azure instrada il traffico in base alle relative [route predefinite](virtual-networks-udr-overview.md#default).

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

1. Nell'elenco di reti virtuali scegliere la rete virtuale che contiene la subnet da cui si vuole dissociare una tabella di route.

1. Nella barra dei menu della rete virtuale scegliere **Subnet.**

1. Selezionare la subnet da cui si vuole annullare l'associazione della tabella di route.

1. In **Tabella di route** scegliere **Nessuno.**

1. Selezionare **Salva**.

### <a name="dissociate-a-route-table---commands"></a>Dissociare una tabella di route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Eliminare una tabella route

Non è possibile eliminare una tabella di route associata ad alcuna subnet. [Annullare l'associazione](#dissociate-a-route-table-from-a-subnet) di una tabella di route da tutte le subnet prima di tentare di eliminarla.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route da eliminare.

1. Selezionare **Elimina** e quindi Sì **nella** finestra di dialogo di conferma.

### <a name="delete-a-route-table---commands"></a>Eliminare una tabella di route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Creare una route

È previsto un limite al numero di route che è possibile creare per ogni tabella di route per località e sottoscrizione di Azure. Per informazioni dettagliate, vedere [Limiti di rete - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route a cui si vuole aggiungere una route.

1. Dalla barra dei menu della tabella di route scegliere **Route**  >  **Aggiungi.**

1. Immettere un nome di **route univoco** per la route all'interno della tabella di route.

1. Immettere il **prefisso dell'indirizzo** in Notazione CIDR (Classless Inter-Domain Routing) a cui si vuole instradare il traffico. Il prefisso non può essere duplicato in più di una route all'interno della tabella di route, anche se il prefisso può essere all'interno di un altro prefisso. Ad esempio, se è stato definito *10.0.0.0/16* come prefisso in una route, è comunque possibile definire un'altra route con il prefisso degli indirizzi *10.0.0.0/22.* Azure seleziona una route per il traffico in base all'algoritmo LPM (Longest Prefix Match). Per altre informazioni, vedere [Come Azure seleziona una route.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Scegliere un **tipo hop successivo**. Per altre informazioni sui tipi di hop successivi, vedere Routing [del traffico di rete virtuale.](virtual-networks-udr-overview.md)

1. Se si sceglie il **tipo hop successivo** Appliance **virtuale**, immettere un indirizzo IP per Indirizzo **hop successivo**.

1. Selezionare **OK**.

### <a name="create-a-route---commands"></a>Creare una route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Visualizzare le route

Una tabella di route può contenere zero o più route. Per altre informazioni sulle informazioni elencate durante la visualizzazione delle route, vedere Routing [del traffico di rete virtuale.](virtual-networks-udr-overview.md)

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route per cui si vogliono visualizzare le route.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

### <a name="view-routes---commands"></a>Visualizzare le route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route list](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route contenente la route per cui si vogliono visualizzare i dettagli.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

1. Selezionare le route di cui si vogliono visualizzare i dettagli.

### <a name="view-details-of-a-route---commands"></a>Visualizzare i dettagli di una route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Modificare una route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route contenente la route da modificare.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco delle route.

1. Scegliere il percorso da modificare.

1. Modificare le impostazioni esistenti con le nuove impostazioni e quindi selezionare **Salva**.

### <a name="change-a-route---commands"></a>Modificare una route - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Eliminare una route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le tabelle di route. Cercare e selezionare **Tabelle di route**.

1. Nell'elenco della tabella di route scegliere la tabella di route contenente la route da eliminare.

1. Nella barra dei menu della tabella di route scegliere **Route** per visualizzare l'elenco di route.

1. Scegliere la route da eliminare.

1. Selezionare **Elimina** e quindi Sì **nella** finestra di dialogo di conferma.

### <a name="delete-a-route---commands"></a>Eliminare una route : comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network route-table route delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Visualizzare le route valide

Le route effettive per ogni interfaccia di rete collegata alla macchina virtuale sono una combinazione di tabelle di route create, route predefinite di Azure ed eventuali route propagate da reti locali tramite Border Gateway Protocol (BGP) tramite un gateway di rete virtuale di Azure. Conoscere le route valide per un'interfaccia di rete può essere utile durante la risoluzione dei problemi di routing. È possibile visualizzare le route effettive per qualsiasi interfaccia di rete collegata a una macchina virtuale in esecuzione.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le macchine virtuali. Cercare e selezionare **Macchine virtuali**.

1. Nell'elenco delle macchine virtuali scegliere la macchina virtuale per cui si vogliono visualizzare le route effettive.

1. Nella barra dei menu della macchina virtuale scegliere **Rete**.

1. Selezionare il nome di un'interfaccia di rete.

1. Nella barra dei menu dell'interfaccia di rete selezionare **Route effettive.**

1. Esaminare l'elenco delle route effettive per verificare se esiste la route corretta per la destinazione a cui si vuole instradare il traffico. Per altre informazioni sui tipi di hop successivi, vedere questo elenco in [Routing del traffico di rete virtuale](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visualizzare le route effettive : comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Convalidare il routing tra due endpoint

È possibile determinare il tipo di hop successivo tra una macchina virtuale e l'indirizzo IP di un'altra risorsa di Azure, una risorsa locale o una risorsa in Internet. Determinare il routing di Azure può essere utile durante la risoluzione dei problemi di routing. Per completare questa attività, è necessario disporre di un network watcher esistente. Se non si ha un network watcher esistente, crearne uno completando la procedura descritta in Creare [un'istanza](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Network Watcher rete .

1. Passare al [portale di Azure](https://portal.azure.com) per gestire i network watcher. Cercare e selezionare **Network Watcher**.

1. Nella barra dei menu di Network Watcher scegliere **Hop successivo.**

1. Nella finestra **di Network Watcher | Pagina hop** successivo:

    1. Scegliere la **sottoscrizione e** il gruppo **di risorse** della macchina virtuale di origine da cui si vuole convalidare il routing.

    1. Scegliere **la macchina virtuale** **e** l'interfaccia di rete collegata alla macchina virtuale.
    
    1. Immettere un **indirizzo IP di origine** assegnato all'interfaccia di rete da cui si vuole convalidare il routing.

    1. Immettere un **indirizzo IP di destinazione** a cui si vuole convalidare il routing.

1. Selezionare **Hop successivo**.

Dopo una breve attesa, Azure indica il tipo di hop successivo e l'ID della route che ha indirizzato il traffico. Altre informazioni sui tipi di hop successivo restituiti in Routing [del traffico di rete virtuale](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Convalidare il routing tra due endpoint - comandi

| Strumento | Comando |
| ---- | ------- |
| Interfaccia della riga di comando di Azure | [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Autorizzazioni

Per eseguire attività su route e tabelle di [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) route, l'account deve essere assegnato al ruolo Collaboratore Rete o [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) un ruolo Personalizzato a cui sono assegnate le azioni appropriate elencate nella tabella seguente:

| Azione                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Leggere una tabella di route                                    |
| Microsoft.Network/routeTables/write                             |   Creare o aggiornare una tabella di route                        |
| Microsoft.Network/routeTables/delete                            |   Eliminare una tabella route                                  |
| Microsoft.Network/routeTables/join/action                       |   Associare una route a una subnet                   |
| Microsoft.Network/routeTables/routes/read                       |   Leggere una route                                          |
| Microsoft.Network/routeTables/routes/write                      |   Creare o aggiornare una route                              |
| Microsoft.Network/routeTables/routes/delete                     |   Eliminare una route                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Ottenere la tabella di route valida per un'interfaccia di rete |
| Microsoft.Network/networkWatchers/nextHop/action                |   Ottenere l'hop successivo da una macchina virtuale                           |

## <a name="next-steps"></a>Passaggi successivi

- Creare una tabella di route usando script [di esempio di PowerShell](powershell-samples.md) o dell'interfaccia della riga di comando di [Azure](cli-samples.md) o modelli Resource Manager [Azure](template-samples.md)
- Creare e assegnare [definizioni Criteri di Azure per](./policy-reference.md) le reti virtuali