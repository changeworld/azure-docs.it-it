---
title: Identificare i dischi di Azure non collegati - Portale di Azure
description: Informazioni su come trovare dischi gestiti e non gestiti di Azure (dischi rigidi virtuali/BLOB di pagine) non collegati tramite il portale di Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898156"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Trovare ed eliminare dischi gestiti e non gestiti di Azure che non sono collegati tramite il portale di Azure

Quando si elimina una macchina virtuale (VM) in Azure, per impostazione predefinita, nessun disco collegato alla macchina virtuale viene eliminato. In questo modo si previene la perdita di dati a causa dell'eliminazione accidentale delle macchine virtuali. Dopo l'eliminazione di una macchina virtuale, si continuerà a pagare per i dischi scollegati. Questo articolo illustra come trovare ed eliminare i dischi non collegati usando il portale di Azure e ridurre i costi non necessari. Le eliminazioni sono permanenti e non sarà possibile ripristinare i dati dopo l'eliminazione di un disco.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dischi gestiti: Trovare ed eliminare i dischi scollegati

Il processo illustrato di seguito spiega come usare il portale di Azure per trovare i dischi gestiti non collegati in cui sono archiviati dati non più necessari:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Cercare e selezionare **Dischi**.

    Nel pannello **Dischi** viene visualizzato un elenco di tutti i dischi. I dischi con il simbolo " **-** " nella colonna **Proprietario** sono dischi non collegati.

    [![Screenshot del pannello Managed disks, se il disco è presente nella colonna Owner, si tratta di un disco non collegato.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Selezionare il disco non collegato che si vuole eliminare. Si aprirà il pannello del disco.
1. Nel pannello del disco è possibile confermare che lo stato del disco è Scollegato, quindi selezionare **Elimina**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Screenshot del pannello dei singoli dischi gestiti. Questo pannello visualizzerà lo stato Scollegato se il disco non è collegato. È possibile eliminare il disco se non è più necessario conservare i dati":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dischi non gestiti: Trovare ed eliminare i dischi scollegati

I dischi non gestiti sono file disco rigido virtuale che sono archiviati come [BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) negli [account di archiviazione di Azure](../storage/common/storage-account-overview.md).

Il processo illustrato di seguito spiega come usare il portale di Azure per eliminare i dischi non gestiti che non sono collegati a una macchina virtuale, in cui sono archiviati dati non più necessari:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Cercare e selezionare **Dischi (versione classica)** .

    Verrà visualizzato un elenco di tutti i dischi non gestiti. I dischi con il simbolo " **-** " nella colonna **Collegato a** sono dischi non collegati.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Screenshot del pannello dei dischi non gestiti. In questo pannello i dischi con il simbolo - nella colonna Scollegato sono dischi non collegati.":::

1. Selezionare il disco non collegato che si vuole eliminare. Si aprirà il pannello del disco.

1. Nel pannello del disco è possibile confermare che il disco non è collegato, poiché sarà ancora visualizzato il simbolo **-** in **Scollegato**.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Screenshot di un pannello dei dischi non gestiti. Il valore in Scollegato sarà - se è il disco non è collegato. Se i dati dei dischi non sono più necessari, è possibile eliminarli.":::

1. Selezionare **Elimina**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Screenshot di un pannello dei dischi non gestiti, con evidenziazione dell'opzione di eliminazione.":::

## <a name="next-steps"></a>Passaggi successivi

Per un modo automatizzato che consenta di trovare ed eliminare gli account di archiviazione non collegati, vedere gli articoli relativi all'[interfaccia della riga di comando](linux/find-unattached-disks.md) o a [PowerShell](windows/find-unattached-disks.md).

Per altre informazioni, vedere [Eliminare l'account di archiviazione](../storage/common/storage-account-create.md#delete-a-storage-account) e [Identificare i dischi orfani con PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
