---
title: Serie ANGC T4 V3
description: Specifiche per le macchine virtuali della serie ANGC T4 V3.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: d73bd81f15263c79e16b574eb961d4ae0ac61175
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417808"
---
# <a name="ncast4_v3-series"></a>Serie NCasT4_v3 

Le macchine virtuali serie NCasT4_v3 sono basate su GPU [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPU AMD EPYC 7V12 (Roma). Le macchine virtuali sono dotate di un massimo di 4 GPU NVIDIA T4 con 16 GB di memoria ciascuno, fino a 64 core del processore AMD EPYC 7V12 (Roma) non multithread e 440 GiB della memoria di sistema. Queste macchine virtuali sono ideali per la distribuzione di servizi di intelligenza artificiale, ad esempio l'inferenza in tempo reale delle richieste generate dall'utente o per carichi di lavoro grafici e di visualizzazione interattivi tramite il Driver GRID e la tecnologia GPU virtuale di NVIDIA. I carichi di lavoro di calcolo GPU standard basati su CUDA, TensorRT, caffe, ONNX e altri Framework, o applicazioni grafiche con accelerazione GPU basate su OpenGL e DirectX, possono essere distribuiti in modo economico, con prossimità agli utenti, nella serie NCasT4_v3.

<br>

[ACU](acu.md): 230-260<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
Interconnessione NVIDIA NVLink: supportata<br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8/32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie NCasT4_v3 di Azure che eseguono Windows o Linux, è necessario installare i driver GPU NVIDIA.

Per installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU serie N per Windows](./windows/n-series-driver-setup.md) per i sistemi operativi supportati, i driver, l'installazione e i passaggi di verifica.

L'estensione driver GPU NVIDIA di Azure distribuirà i driver CUDA nelle VM serie NCasT4_v3. Per i carichi di lavoro grafici e di visualizzazione installare manualmente i driver della griglia supportati da Azure.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
