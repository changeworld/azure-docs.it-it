---
title: Come contrassegnare una macchina virtuale usando il portale di Azure
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite il portale di Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897409"
---
# <a name="tagging-a-vm-using-the-portal"></a>Assegnazione di tag a una macchina virtuale tramite il portale

Questo articolo descrive come aggiungere tag a una macchina virtuale usando il portale. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente.


1. Passare alla macchina virtuale nel portale.
1. In **Essentials** selezionare **fare clic qui per aggiungere i tag**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot della sezione Essentials della pagina VM.":::

1. Aggiungere un valore per **nome** e **valore**, quindi selezionare **Salva**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot della pagina per l'aggiunta di una coppia chiave-valore come tag.":::

### <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni sul modo in cui i tag consentono di gestire l'uso delle risorse di Azure, vedere [informazioni sulla fattura di Azure](../cost-management-billing/understand/review-individual-bill.md).
