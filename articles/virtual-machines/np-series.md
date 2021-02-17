---
title: Serie NP-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 0b73697097e13f26a83eeaa72837fc00c31ef826
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557190"
---
# <a name="np-series-preview"></a>Serie NP (anteprima)

Le macchine virtuali serie NP sono basate su [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA per accelerare i carichi di lavoro, tra cui l'inferenza di Machine Learning, la transcodifica video e la ricerca di database & Analytics. Le macchine virtuali della serie NP sono basate anche su CPU Intel Xeon 8171M (Skylake) con tutta la velocità di clock di base Turbo 3,2 GHz.


[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
Supporto per la generazione di VM: generazione 1<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | FPGA | Memoria FPGA: GiB | Numero massimo di dischi dati | NIC massimo/larghezza di banda di rete prevista (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati
Per ottenere l'elenco completo dei sistemi operativi supportati, vedere le [Note sulla versione di Xilinx Runtime (XRT)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) .

Durante il programma di anteprima Microsoft Azure i team di progettazione condivideranno istruzioni specifiche per l'installazione dei driver.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
