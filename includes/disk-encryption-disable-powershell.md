---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776792"
---
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. La disabilitazione della crittografia dei dischi dati nella macchina virtuale Windows quando sia i dischi dati che il disco del sistema operativo sono stati crittografati non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

- **Disabilitare la crittografia dei dischi con Azure PowerShell:** per disabilitare la crittografia, usare il cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** 

    1. Fare clic su **Deploy to Azure** (Distribuisci in Azure) dal modello per [disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, il tipo di volume, i termini legali e il contratto.
    3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 
