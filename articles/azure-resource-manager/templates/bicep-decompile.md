---
title: Convertire i modelli tra JSON e bicipite
description: Descrive i comandi per la conversione di modelli di Azure Resource Manager da bicipite a JSON e da JSON a bicipite.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103422186"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Conversione di modelli ARM tra JSON e bicipite

Questo articolo descrive come convertire i modelli di Azure Resource Manager (modelli ARM) da JSON a bicipite e da bicipite a JSON.

Per eseguire i comandi di conversione è necessario che sia installata l'interfaccia della riga di comando di [bicipite](bicep-install.md) .

I comandi di conversione producono modelli equivalenti dal punto di vista funzionale. Tuttavia, potrebbero non essere esattamente gli stessi nell'implementazione. La conversione di un modello da JSON a bicipite e quindi di nuovo in JSON probabilmente comporta un modello con sintassi diversa rispetto al modello originale. Quando vengono distribuiti, i modelli convertiti producono gli stessi risultati.

## <a name="convert-from-json-to-bicep"></a>Conversione da JSON a bicipite

L'interfaccia della riga di comando bicipite fornisce un comando per decompilare qualsiasi modello JSON esistente in un file bicipite. Per decompilare un file JSON, usare:

```azurecli
bicep decompile mainTemplate.json
```

Questo comando fornisce un punto di partenza per la creazione di bicipiti. Il comando non funziona per tutti i modelli. Attualmente, i modelli annidati possono essere decompilati solo se usano l'ambito di valutazione dell'espressione "Inner". Non è possibile decompilare i modelli che usano i cicli di copia.

## <a name="convert-from-bicep-to-json"></a>Conversione da bicipite a JSON

L'interfaccia della riga di comando bicipite offre anche un comando per convertire il bicipite in JSON. Per compilare un file JSON, usare:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Esporta modello e Converti

È possibile esportare il modello per un gruppo di risorse e passarlo direttamente al comando decompile. Nell'esempio seguente viene illustrato come decompilare un modello esportato.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

[Esportare il modello](export-template-portal.md) tramite il portale.

Usare `bicep decompile <filename>` nel file scaricato.

---

## <a name="side-by-side-view"></a>Visualizzazione affiancata

Il [parco bicipite](https://aka.ms/bicepdemo) consente di visualizzare side-by-Side i file JSON e bicipite equivalenti. È possibile selezionare un modello di esempio per visualizzare entrambe le versioni. In alternativa, selezionare `Decompile` per caricare il proprio modello JSON e visualizzare il file bicipite equivalente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul bicipite, vedere l' [esercitazione sul bicipite](./bicep-tutorial-create-first-bicep.md).
