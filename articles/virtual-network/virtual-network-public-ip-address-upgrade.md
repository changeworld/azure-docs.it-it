---
title: Aggiornare gli indirizzi IP pubblici
titleSuffix: Azure Virtual Network
description: Aggiornare gli indirizzi IP pubblici da Basic a Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598415"
---
# <a name="upgrade-public-ip-addresses"></a>Aggiornare gli indirizzi IP pubblici

Gli indirizzi IP pubblici di Azure vengono creati con uno SKU, Basic o Standard, che determina gli aspetti delle relative funzionalità, tra cui il metodo di allocazione, il supporto delle funzionalità e le risorse a cui possono essere associati. 

In questo articolo vengono esaminati gli scenari seguenti:
* Come aggiornare un indirizzo IP pubblico dello SKU Basic a un indirizzo IP pubblico dello SKU Standard (tramite PowerShell o l'interfaccia della riga di comando)
* Come eseguire la migrazione di un'istanza classica di Azure IP riservato a un indirizzo IP pubblico Azure Resource Manager SKU Basic

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Aggiornare l'indirizzo IP pubblico dallo SKU Basic a Standard

Per aggiornare un indirizzo IP pubblico, non deve essere [](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) associato ad alcuna risorsa (vedere questa pagina per altre informazioni su come dissociare gli indirizzi IP pubblici).

>[!IMPORTANT]
>Gli IP pubblici aggiornati dallo SKU Basic a Standard continuano a non avere zone di [disponibilità garantite.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Assicurarsi di tenere presente questo problema quando si scelgono le risorse a cui associare l'indirizzo IP.

---
# <a name="basic-to-standard---powershell"></a>[**Da basic a standard - PowerShell**](#tab/option-upgrade-powershell)

L'esempio seguente presuppone la creazione precedente di un indirizzo IP pubblico dello SKU Basic, usando l'esempio fornito [in](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) questa pagina con un INDIRIZZO IP pubblico Basic **myBasicPublicIP** in **myResourceGroup**.

Per aggiornare l'indirizzo IP, è sufficiente eseguire i comandi seguenti usando PowerShell.  Si noti che se l'indirizzo IP è già allocato in modo statico, tale sezione può essere ignorata.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Da basic a standard - Interfaccia della riga di comando**](#tab/option-upgrade-cli)

L'esempio seguente presuppone la creazione precedente di un indirizzo IP pubblico dello SKU Basic, usando l'esempio fornito [in](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) questa pagina con un INDIRIZZO IP pubblico Basic **myBasicPublicIP** in **myResourceGroup**.

Per aggiornare l'indirizzo IP, è sufficiente eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.  Si noti che se l'indirizzo IP è già allocato in modo statico, tale sezione può essere ignorata.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Aggiornare (eseguire la migrazione) un IP riservato classico a un indirizzo IP pubblico statico

Per trarre vantaggio dalle nuove funzionalità di Azure Resource Manager, è possibile eseguire la migrazione di indirizzi IP statici pubblici esistenti, denominati INDIRIZZI IP riservati, dal modello classico al modello di Azure Resource Manager moderno.  L'INDIRIZZO IP pubblico migrato sarà di tipo SKU Basic.


---

# <a name="reserved-to-basic---powershell"></a>[**Riservato a Basic - PowerShell**](#tab/option-migrate-powershell)

L'esempio seguente presuppone la creazione precedente di un'istanza classica di Azure IP riservato **myReservedIP** in **myResourceGroup**. Un altro prerequisito per la migrazione è garantire che la sottoscrizione Azure Resource Manager sia registrata per la migrazione. Questa operazione è illustrata in dettaglio nei passaggi 3 e 4 [di questa pagina.](../virtual-machines/migration-classic-resource-manager-ps.md)

Per eseguire la migrazione del IP riservato, eseguire i comandi seguenti usando PowerShell.  Si noti che se l'indirizzo IP non è associato ad alcun servizio (sotto è presente un servizio denominato **myService**), tale passaggio può essere ignorato.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Il comando precedente visualizza tutti gli avvisi e gli errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con i passaggi seguenti per preparare ed eseguire il commit della migrazione:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Viene creato un nuovo gruppo di risorse in Azure Resource Manager usando il nome del IP riservato migrato (nell'esempio precedente, sarebbe il gruppo di risorse **myReservedIP-Migrated**).

# <a name="reserved-to-basic---cli"></a>[**Riservato a Basic - Interfaccia della riga di comando**](#tab/option-migrate-cli)

L'esempio seguente presuppone la creazione precedente di un'istanza classica di Azure IP riservato **myReservedIP** in **myResourceGroup**. Un altro prerequisito per la migrazione è garantire che la sottoscrizione Azure Resource Manager sia registrata per la migrazione. Questa operazione è illustrata in dettaglio nei passaggi 3 e 4 [di questa pagina.](../virtual-machines/migration-classic-resource-manager-cli.md)

Per eseguire la migrazione del IP riservato, eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.  Si noti che se l'indirizzo IP non è associato ad alcun servizio (di seguito è presente un servizio denominato **myService** e distribuzione **myDeployment**), tale passaggio può essere ignorato.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Il comando precedente visualizza tutti gli avvisi e gli errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con i passaggi seguenti per preparare ed eseguire il commit della migrazione:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Viene creato un nuovo gruppo di risorse in Azure Resource Manager usando il nome del IP riservato migrato (nell'esempio precedente si tratta del gruppo di risorse **myReservedIP-Migrated**).

---

## <a name="limitations"></a>Limitazioni

* Per aggiornare un indirizzo IP pubblico Basic, non può essere associato ad alcuna risorsa di Azure.  Per altre [informazioni su](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) come disassociare gli IP pubblici, vedere questa pagina.  Analogamente, per eseguire la migrazione di un IP riservato, non può essere associato ad alcun servizio cloud.  Per altre [informazioni su](./remove-public-ip-address-vm.md) come disassociare gli indirizzi IP riservati, vedere questa pagina.  
* Gli IP pubblici aggiornati dallo SKU Basic a Standard continuano a non avere zone di [disponibilità garantite.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Assicurarsi di tenere presente questo problema quando si scelgono le risorse a cui associare l'indirizzo IP.
* Non è possibile eseguire il downgrade da Standard a Basic.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure, tra cui la differenza tra i tipi di SKU e le impostazioni degli indirizzi IP [pubblici.](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Informazioni su come aggiornare [i servizi di bilanciamento del carico pubblici di Azure da Basic a Standard.](../load-balancer/upgrade-basic-standard.md)
- Informazioni [su indirizzi IP riservati di Azure](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) classici e migrazione delle risorse classiche [Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
