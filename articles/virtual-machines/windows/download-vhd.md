---
title: Scaricare un disco rigido virtuale Linux da Azure
description: Scaricare un disco rigido virtuale Windows tramite il portale di Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565288"
---
# <a name="download-a-windows-vhd-from-azure"></a>Scaricare un disco rigido virtuale Linux da Azure

Questo articolo illustra come scaricare un file di disco rigido virtuale (VHD) Windows da Azure usando il portale di Azure.

## <a name="optional-generalize-the-vm"></a>Facoltativo: Generalizzare la macchina virtuale

Se si vuole usare il disco [](tutorial-custom-images.md) rigido virtuale come immagine per creare altre macchine virtuali, è consigliabile usare [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) per generalizzare il sistema operativo. In caso contrario, sarà necessario creare una copia del disco per ogni macchina virtuale che si vuole creare.

Per usare il disco rigido virtuale come immagine per creare altre macchine virtuali, generalizzare la macchina virtuale.

1. Se non è già stato fatto, accedere al portale di Azure [.](https://portal.azure.com/)
2. [Connettersi alla macchina virtuale](connect-logon.md). 
3. Nella VM aprire la finestra del prompt dei comandi come amministratore.
4. Passare alla directory *%windir%\system32\sysprep* e quindi eseguire sysprep.exe.
5. Nella finestra di dialogo Utilità preparazione sistema selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
6. In Opzioni di arresto selezionare **Arresta** e quindi fare clic su **OK.** 

Se non si vuole generalizzare la macchina virtuale corrente, è comunque possibile creare un'immagine generalizzata creando prima uno [snapshot](#alternative-snapshot-the-vm-disk)del disco del sistema operativo, creando una nuova macchina virtuale dallo snapshot e quindi generalizzando la copia.

## <a name="stop-the-vm"></a>Arrestare la VM

Un disco rigido virtuale non può essere scaricato da Azure se è collegato a una macchina virtuale in esecuzione. Se si vuole mantenere la macchina virtuale in esecuzione, è possibile [creare uno snapshot e quindi scaricare lo snapshot](#alternative-snapshot-the-vm-disk).

1. Nel menu Hub del portale di Azure fare clic su **Macchine virtuali**.
1. Selezionare la VM dall'elenco.
1. Nel pannello della VM fare clic su **Interrompi**.

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
> Se non si arresta prima la macchina virtuale, lo snapshot non sarà pulito. Lo snapshot sarà nello stesso stato di quando la macchina virtuale è stata cicliata o si è ata in modo anomalo nel momento in cui è stato creato lo snapshot.  Anche se in genere è sicuro, può causare problemi se le applicazioni in esecuzione che eseguono un'ora non sono erre contro l'arresto anomalo del sistema.
>  
> Questo metodo è consigliato solo per le macchine virtuali con un singolo disco del sistema operativo. Le macchine virtuali con uno o più dischi dati devono essere arrestate prima del download o prima di creare uno snapshot per il disco del sistema operativo e ogni disco dati.

## <a name="generate-download-url"></a>Generare l'URL di download

Per scaricare il file VHD, è necessario generare un URL di [firma di accesso condiviso (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Quando viene generato, all'URL viene assegnata una scadenza.

1. Nella pagina della macchina virtuale fare clic **su Dischi** nel menu a sinistra.
1. Selezionare il disco del sistema operativo per la macchina virtuale.
1. Nella pagina del disco selezionare **Disk Export (Esportazione** disco) dal menu a sinistra.
1. L'ora di scadenza predefinita dell'URL *è 3600* secondi (un'ora). Potrebbe essere necessario aumentare questo valore per i dischi del sistema operativo Windows o i dischi dati di grandi dimensioni. **36000 secondi** (10 ore) è in genere sufficiente.
1. Fare clic su **Genera URL**.

> [!NOTE]
> La scadenza viene aumentata rispetto all'impostazione predefinita per fornire un tempo sufficiente a scaricare il file VHD di grandi dimensioni di un sistema operativo Windows Server. Il download di dischi rigidi virtuali di grandi dimensioni può richiedere fino a diverse ore a seconda della connessione e delle dimensioni della macchina virtuale. 
> 
> 

## <a name="download-vhd"></a>Scaricare il disco rigido virtuale

1. Nell'URL appena generato fare clic su Scarica il file VHD.
1. Potrebbe essere necessario fare clic **su Salva** nel browser per avviare il download. Il nome predefinito per il file VHD è *abcd*.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [caricare un file VHD in Azure](upload-generalized-managed.md). 
- [Creare dischi gestiti da dischi non gestiti in un account di archiviazione](attach-disk-ps.md).
- [Gestire i dischi di Azure con PowerShell](tutorial-manage-data-disk.md).
