---
title: Serie NCv3-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie NCv3.
ms.subservice: hpc
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d42b4eac09579dfa09cc07bfbbfffa1bbdaa65b5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670197"
---
# <a name="ncv3-series"></a>Serie NCv3

Le macchine virtuali serie NCv3 sono basate sulle GPU NVIDIA Tesla V100. Queste GPU possono offrire prestazioni di calcolo una volta e mezzo superiori rispetto a quelle della serie NCv2. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. La configurazione NC24rs v3 offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati. Oltre alle GPU, le macchine virtuali della serie NCv3 sono basate anche su CPU Intel Xeon E5-2690 V4 (Broadwell).

[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): non supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
Interconnessione NVIDIA NVLink: non supportata<br>

> [!IMPORTANT]
> Per questa serie di macchine virtuali, la quota di vCPU (Core) nella sottoscrizione viene inizialmente impostata su 0 in ogni area. [Richiedere un aumento della quota di vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) per questa serie in un' [area disponibile](https://azure.microsoft.com/regions/services/). Questi SKU non sono disponibili per le sottoscrizioni di valutazione o sottoscrittore di Visual Studio. Il livello di abbonamento potrebbe non supportare la selezione o la distribuzione di questi SKU. 
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = Pari a una scheda V100.

*Con supporto di RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie N di Azure, è necessario installare i driver GPU NVIDIA.

L'[estensione del driver NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione. Per altre informazioni sulle estensioni macchina virtuale, vedere [Azure virtual machine extensions and features](./extensions/overview.md) (Funzionalità ed estensioni macchina virtuale di Azure).

Se si sceglie di installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU della serie n per](./windows/n-series-driver-setup.md) la configurazione di driver GPU per Windows o [serie n per Linux](./linux/n-series-driver-setup.md) per i sistemi operativi, i driver, l'installazione e i passaggi di verifica supportati.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
