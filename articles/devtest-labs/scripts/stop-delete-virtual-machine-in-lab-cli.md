---
title: Interfaccia della riga di comando di Azure - Arrestare ed eliminare una macchina virtuale in un lab
description: Questo articolo include uno script dell'interfaccia della riga di comando di Azure che arresta ed elimina una macchina virtuale in un lab in Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: e30ee47c4f34fe6a71e8c934a4f36cb7edbbe20e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777317"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usare l'interfaccia della riga di comando di Azure per arrestare ed eliminare una macchina virtuale in un lab in Azure DevTest Labs

Questo script dell'interfaccia della riga di comando di Azure arresta ed elimina una macchina virtuale (VM) in un lab. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az_lab_vm_stop) | Arresta una macchina virtuale in un lab. Il completamento di questa operazione può richiedere tempo. |
| [az lab vm delete](/cli/azure/lab/vm#az_lab_vm_delete) | Elimina una macchina virtuale in un lab. Il completamento di questa operazione può richiedere tempo. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per Azure Lab Services sono disponibili in [Azure Lab Services CLI samples](../samples-cli.md) (Esempi dell'interfaccia della riga di comando per Azure Lab Services).
