---
title: consente di ripristinare un account di archiviazione eliminato
titleSuffix: Azure Storage
description: Informazioni su come ripristinare un account di archiviazione eliminato all'interno del portale di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c37e4aeb9b9af1c4f792d0827fec39750a1b1c2a
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096043"
---
# <a name="recover-a-deleted-storage-account"></a>consente di ripristinare un account di archiviazione eliminato

In alcuni casi, è possibile ripristinare un account di archiviazione eliminato dall'interno del portale di Azure. Per ripristinare un account di archiviazione, è necessario che siano soddisfatte le condizioni seguenti:

- L'account di archiviazione è stato eliminato negli ultimi 14 giorni.
- L'account di archiviazione è stato creato con il modello di distribuzione Azure Resource Manager.
- Non è stato creato un nuovo account di archiviazione con lo stesso nome dopo l'eliminazione dell'account originale.

Prima di tentare di ripristinare un account di archiviazione eliminato, verificare che sia presente il gruppo di risorse per l'account. Se il gruppo di risorse è stato eliminato, è necessario ricrearlo. Il ripristino di un gruppo di risorse non è possibile. Per ulteriori informazioni su, vedere [Manage Resource Groups](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Se l'account di archiviazione eliminato usava chiavi gestite dal cliente con Azure Key Vault e l'insieme di credenziali delle chiavi è stato eliminato, è necessario ripristinare l'insieme di credenziali delle chiavi prima di ripristinare l'account di archiviazione. Per altre informazioni, vedere [Panoramica di Azure Key Vault Recovery](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Il ripristino di un account di archiviazione eliminato non è garantito. Il ripristino è un tentativo con il massimo sforzo. Microsoft consiglia di bloccare le risorse per evitare l'eliminazione accidentale di un account. Per altre informazioni sui blocchi delle risorse, vedere [bloccare le risorse per impedire le modifiche](../../azure-resource-manager/management/lock-resources.md).
>
> Un'altra procedura consigliata per evitare l'eliminazione accidentale di un account consiste nel limitare il numero di utenti che dispongono delle autorizzazioni per eliminare un account tramite il controllo degli accessi in base al ruolo (RBAC di Azure). Per altre informazioni, vedere [procedure consigliate per RBAC di Azure](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Ripristinare un account eliminato dalla portale di Azure

Per ripristinare un account di archiviazione eliminato da un altro account di archiviazione, seguire questa procedura:

1. Passare alla pagina Panoramica per un account di archiviazione esistente nel portale di Azure.
1. Nella sezione **supporto e risoluzione dei problemi** selezionare **Ripristina account eliminato**.
1. Nell'elenco a discesa selezionare l'account da ripristinare, come illustrato nella figura seguente. Se l'account di archiviazione che si desidera ripristinare non è presente nell'elenco a discesa, non sarà possibile recuperarlo.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Screenshot che illustra come ripristinare un account di archiviazione in portale di Azure":::

1. Selezionare il pulsante **Ripristina** per ripristinare l'account. Nel portale viene visualizzata una notifica che indica che è in corso il ripristino.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Ripristinare un account eliminato tramite un ticket di supporto

1. Nella portale di Azure passare a guida e **supporto**.
1. Selezionare **Nuova richiesta di supporto**.
1. Nella scheda **nozioni di base** , nel campo **tipo di problema** , selezionare **tecnico**.
1. Nel campo **sottoscrizione** selezionare la sottoscrizione che contiene l'account di archiviazione eliminato.
1. Nel campo **servizio** selezionare **Gestione account di archiviazione**.
1. Nel campo **risorsa** selezionare una risorsa dell'account di archiviazione. L'account di archiviazione eliminato non verrà visualizzato nell'elenco.
1. Aggiungere un breve riepilogo del problema.
1. Nel campo **tipo di problema** selezionare **eliminazione e ripristino**.
1. Nel campo **sottotipo di problema** selezionare **Ripristina account di archiviazione eliminati**. Nell'immagine seguente viene illustrato un esempio della scheda **nozioni di base** da compilare:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Screenshot che illustra come ripristinare un account di archiviazione tramite il ticket di supporto-scheda nozioni di base":::

1. Passare quindi alla scheda **soluzioni** e selezionare **ripristino dell'account di archiviazione controllato dal cliente**, come illustrato nella figura seguente:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Screenshot che illustra come ripristinare un account di archiviazione tramite la scheda ticket di supporto-soluzioni":::

1. Nell'elenco a discesa selezionare l'account da ripristinare, come illustrato nella figura seguente. Se l'account di archiviazione che si desidera ripristinare non è presente nell'elenco a discesa, non sarà possibile recuperarlo.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Screenshot che illustra come ripristinare un account di archiviazione tramite un ticket di supporto":::

1. Selezionare il pulsante **Ripristina** per ripristinare l'account. Nel portale viene visualizzata una notifica che indica che è in corso il ripristino.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione](storage-account-overview.md)
- [Creare un account di archiviazione](storage-account-create.md)