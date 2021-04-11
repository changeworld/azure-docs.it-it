---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078800"
---
Le dimensioni della macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, rese disponibili per la VM. È necessario creare macchine virtuali usando le dimensioni della VM appropriate per il carico di lavoro. Anche se tutti i computer verranno eseguiti sullo stesso hardware, le dimensioni dei computer hanno limiti diversi per l'accesso al disco. Ciò consente di gestire l'accesso globale al disco tra le macchine virtuali. Se un carico di lavoro aumenta, è anche possibile ridimensionare una macchina virtuale esistente.

Le macchine virtuali seguenti sono supportate per la creazione sul dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Serie Dv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)   | Numero massimo di dischi dati | Schede di interfaccia di rete max |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3,5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>Serie DSv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Numero massimo di dischi dati| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3,5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


Per ulteriori informazioni, vedere [dv2 e serie DSv2](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>Serie NCasT4_v3 (anteprima)

Queste dimensioni sono supportate per le macchine virtuali GPU nel dispositivo e sono ottimizzate per le applicazioni con accelerazione GPU a elevato utilizzo di calcolo. Questa serie è incentrata sui carichi di lavoro di inferenza con la GPU Tesla T4 di NVIDIA. 

|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | GPU | Memoria GPU (GiB) | Schede di interfaccia di rete max |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

Per ulteriori informazioni, vedere la [serie NCasT4_v3](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>Serie F

Queste serie sono ottimizzate per carichi di lavoro computazionali ed eseguite su processori Intel Xeon. 

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (GiB) |  Numero massimo di dischi dati | Schede di interfaccia di rete max |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

Per ulteriori informazioni, vedere la [serie Fsv2](../articles/virtual-machines/fsv2-series.md).

