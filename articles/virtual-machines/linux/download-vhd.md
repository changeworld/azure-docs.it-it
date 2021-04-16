---
title: Scaricare un disco rigido virtuale Linux da Azure
description: Scaricare un disco rigido virtuale Linux usando l'interfaccia della riga di comando di Azure e il portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565186"
---
# <a name="download-a-linux-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file del disco rigido virtuale (VHD) Linux da Azure usando il portale di Azure. 

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Se si vuole mantenere la macchina virtuale in esecuzione, è possibile [creare uno snapshot e quindi scaricare lo snapshot](#alternative-snapshot-the-vm-disk).

Per arrestare la macchina virtuale:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Nel menu a sinistra selezionare **Macchine virtuali**.
3.  Selezionare la VM dall'elenco.
4.  Nella pagina della macchina virtuale selezionare **Arresta**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Mostra il pulsante di menu per arrestare la macchina virtuale.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternativa: Eseguire lo snapshot del disco della macchina virtuale

Creare uno snapshot del disco da scaricare.

1. Selezionare la macchina virtuale nel [portale](https://portal.azure.com).
2. Selezionare **Dischi nel** menu a sinistra e quindi selezionare il disco di cui si vuole creare lo snapshot. Verranno visualizzati i dettagli del disco.  
3. Selezionare **Crea snapshot** dal menu nella parte superiore della pagina. Verrà **visualizzata la pagina** Crea snapshot.
4. In **Nome** digitare un nome per lo snapshot. 
5. Per **Tipo di snapshot** selezionare Completo **o** **Incrementale.**
6. Al termine, selezionare **Rivedi e crea**.

Lo snapshot verrà creato a breve e potrà quindi essere usato per scaricare o creare un'altra macchina virtuale.

> [!NOTE]
> Se non si arresta prima la macchina virtuale, lo snapshot non sarà pulito. Lo snapshot sarà nello stesso stato di quando la macchina virtuale è stata determinare un ciclo di alimentazione o un arresto anomalo nel momento in cui è stato creato lo snapshot.  Anche se in genere è sicuro, potrebbe causare problemi se le applicazioni in esecuzione che eseguono un'ora non sono in grado di causare arresti anomalo del sistema.
>  
> Questo metodo è consigliato solo per le macchine virtuali con un singolo disco del sistema operativo. Le macchine virtuali con uno o più dischi dati devono essere arrestate prima del download o prima di creare uno snapshot per il disco del sistema operativo e ogni disco dati.

## <a name="generate-sas-url"></a>Generare l'URL SAS

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1. Nel menu della pagina per la macchina virtuale selezionare **Dischi**.
2. Selezionare il disco del sistema operativo per la macchina virtuale e quindi selezionare **Esportazione disco**.
1. Se necessario, aggiornare il valore dell'URL scade **in (secondi) per** concedere tempo sufficiente per completare il download. Il valore predefinito è 3600 secondi (un'ora).
3. Selezionare **Generate URL (Genera URL).**
 
      
## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1.  Nell'URL generato selezionare **Scarica il file VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Mostra il pulsante per scaricare il disco rigido virtuale.":::

2.  Potrebbe essere necessario selezionare **Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

## <a name="next-steps"></a>Passaggi successivi

- Apprendere come [caricare e creare una macchina virtuale Linux da un disco personalizzato usando l'interfaccia della riga di comando di Azure](upload-vhd.md). 
- [Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure](tutorial-manage-disks.md).
