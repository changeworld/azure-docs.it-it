---
title: Ottenere un gruppo di risorse gestite e ridimensionare le macchine virtuali - Interfaccia della riga di comando di Azure
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che ottiene un gruppo di risorse gestito in un'applicazione gestita di Azure. Lo script ridimensiona le VM.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 179b1b64656d3f97778e183d57797e4b3660fece
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775440"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Ottenere risorse in un gruppo di risorse gestite e ridimensionare le macchine virtuali con l'interfaccia della riga di comando di Azure

Questo script recupera le risorse da un gruppo di risorse gestite e ridimensiona le macchine virtuali in tale gruppo.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az_managedapp_list) | Elenca le applicazioni gestite. Fornire i valori di query per definire i risultati. |
| [az resource list](/cli/azure/resource#az_resource_list) | Elenca le risorse. Fornire un gruppo di risorse e i valori di query per definire i risultati. |
| [az vm resize](/cli/azure/vm#az_vm_resize) | Aggiorna le dimensioni di una macchina virtuale. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
