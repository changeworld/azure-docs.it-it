---
title: Come contrassegnare una macchina virtuale usando un modello
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite un modello.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897375"
---
# <a name="tagging-a-vm-using-a-template"></a>Assegnazione di tag a una macchina virtuale tramite un modello

Questo articolo descrive come assegnare un tag a una macchina virtuale in Azure usando un modello di Gestione risorse. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente.

[Questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) inserisce i tag per le risorse seguenti: calcolo (macchina virtuale), archiviazione (Account di archiviazione) e rete (indirizzo IP pubblico, rete virtuale e interfaccia di rete). Questo modello riguarda una VM Windows ma può essere adattato per le VM Linux.

Fare clic sul pulsante **Distribuisci in Azure** dal [collegamento modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Verrà visualizzato il [portale di Azure](https://portal.azure.com/) in cui è possibile distribuire il modello.

![Distribuzione semplice di tag](./media/tag/deploy-to-azure-tags.png)

Questo modello include i tag seguenti: *Reparto*, *Applicazione* e *Creato da*. È possibile aggiungere o modificare questi tag direttamente nel modello se si desiderano diversi nomi di tag.

![Tag di Azure in un modello](./media/tag/azure-tags-in-a-template.png)

Come si può vedere, i tag vengono definiti come coppie chiave/valore, separate da due punti (:). I tag devono essere definiti in questo formato:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Salvare il file di modello al termine della modifica, con i tag di propria scelta.

Successivamente, nella sezione **Modifica parametri** , è possibile compilare i valori per i tag.

![Modificare i tag nel portale di Azure](./media/tag/edit-tags-in-azure-portal.png)

Fare clic su **Crea** per distribuire il modello con i valori dei tag.

### <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni sul modo in cui i tag consentono di gestire l'uso delle risorse di Azure, vedere [informazioni sulla fattura di Azure](../cost-management-billing/understand/review-individual-bill.md).
