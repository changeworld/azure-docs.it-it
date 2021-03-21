---
title: Sincronizzare le impostazioni server DNS della rete virtuale in SQL Istanza gestita cluster virtuale
description: Informazioni su come sincronizzare i server DNS della rete virtuale in SQL Istanza gestita cluster virtuale.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173552"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Sincronizzare le impostazioni server DNS della rete virtuale in SQL Istanza gestita cluster virtuale
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra quando e come sincronizzare i server DNS di rete virtuale impostando il cluster virtuale SQL Istanza gestita.

## <a name="when-to-synchronize-the-dns-setting"></a>Quando sincronizzare l'impostazione DNS

In alcuni scenari, ad esempio la posta elettronica del database e i server collegati ad altre istanze di SQL Server nell'ambiente cloud o ibrido, sono richiesti nomi host privati che devono essere risolti dall'Istanza gestita di SQL. In questo caso, è necessario configurare un DNS personalizzato all'interno di Azure. Per conoscere i dettagli, vedere [Configurare un DNS personalizzato per Istanza gestita di SQL di Azure](custom-dns-configure.md).

Se questa modifica viene implementata dopo la creazione di un [cluster virtuale](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) che ospita istanza gestita, sarà necessario sincronizzare le impostazioni dei server DNS nel cluster virtuale con la configurazione della rete virtuale.

> [!IMPORTANT]
> La sincronizzazione delle impostazioni dei server DNS influirà su tutte le istanze gestite ospitate nel cluster virtuale.

## <a name="how-to-synchronize-the-dns-setting"></a>Come sincronizzare l'impostazione DNS

### <a name="azure-rbac-permissions-required"></a>Autorizzazioni RBAC di Azure richieste

La configurazione del server DNS per la sincronizzazione degli utenti deve avere uno dei ruoli di Azure seguenti:

- Ruolo di proprietario della sottoscrizione o
- Istanza gestita ruolo Collaboratore o
- Ruolo personalizzato con l'autorizzazione seguente:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

Ottenere la rete virtuale in cui è stata aggiornata l'impostazione dei server DNS.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Usare il comando PowerShell [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) per sincronizzare la configurazione dei server DNS per tutti i cluster virtuali nella subnet.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Ottenere la rete virtuale in cui è stata aggiornata l'impostazione dei server DNS.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Usare l'interfaccia della riga di comando di Azure [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) per sincronizzare la configurazione dei server DNS per tutti i cluster virtuali nella subnet.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla configurazione di un DNS personalizzato [configurare un DNS personalizzato per il istanza gestita SQL di Azure](custom-dns-configure.md).
- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
