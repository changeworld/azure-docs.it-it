---
title: Serie Msv2 (anteprima)-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 04d6daac6644bd8d29479ac527bbdd8a5bdbd116
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679695"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Memoria media della serie Msv2 e Mdsv2 (anteprima)


> [!IMPORTANT]
> Per partecipare all'anteprima, compilare il modulo all'indirizzo **https://aka.ms/Mv2MedMemoryPreview** .  

La serie di macchine virtuali Msv2 e Mdsv2 Media Memory include il processore Intel® Xeon® Platinum 8280 (Cascade Lake) con una frequenza di base di tutti i core a 2,7 GHz e una frequenza Turbo a core singolo a 4,0 GHz. Con queste macchine virtuali, i clienti ottengono una maggiore flessibilità con le opzioni disco locale e senza dischi. I clienti hanno anche accesso a un set di nuove dimensioni di VM isolate con più CPU e memoria che passano fino a 192 vCPU con 4 TiB di memoria. 


Le macchine virtuali serie Msv2 e Mdsv2 sono solo di seconda generazione e supportano un subset di immagini supportate di seconda generazione. Vedere di seguito per l'elenco completo delle immagini supportate per la serie Msv2 e Mdsv2.  

- Windows Server 2019 o versione successiva
- SUSE Linux Enterprise Server 12 SP4 e versioni successive o SUSE Linux Enterprise Server 15 SP1 e versioni successive
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 o versione successiva 
- Oracle Enterprise Linux 7,7 o versione successiva

Per altre informazioni sulle macchine virtuali di seconda generazione, vedere Supporto per le macchine virtuali [di seconda generazione in Azure](./generation-2.md).



[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 2<br>
[Acceleratore di scrittura](./how-to-enable-write-accelerator.md): supportato<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 di memoria media senza dischi 

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Memoria media Mdsv2 con disco  

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Disco dati max | Velocità effettiva massima memorizzata nella cache e archiviazione temporanea: IOPS/MBps | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
