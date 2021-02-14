---
title: Dimensioni delle macchine virtuali di Azure per FPGA (Field-Programmable Gate Array)
description: Elenca le diverse dimensioni ottimizzate per FPGA disponibili per le macchine virtuali in Azure. Elenca informazioni sul numero di vCPU, dei dischi dati e delle schede di rete, nonché sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per le dimensioni di queste serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: b05c3198f6c3d84f852d9535a3cf0b67f66e01f6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417611"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>Dimensioni delle macchine virtuali ottimizzate per FPGA

Le dimensioni delle VM ottimizzate per FPGA sono macchine virtuali specializzate disponibili con uno o più FPGA. Queste dimensioni sono progettate per carichi di lavoro a elevato utilizzo di calcolo. Questo articolo fornisce informazioni sul numero e sul tipo di FPGA, vCPU, dischi dati e schede di rete. Anche velocità effettiva di archiviazione e larghezza di banda della rete sono incluse per ogni dimensione di questo raggruppamento.

- Le dimensioni della [serie NP](np-series.md) sono ottimizzate per i carichi di lavoro, tra cui l'inferenza di Machine Learning, la transcodifica video e la ricerca di database & Analytics. Le serie NP sono basate su Xilinx U250 Accelerator.


## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

- Per la disponibilità delle VM serie N, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

- Le VM serie N possono essere distribuite solo nel modello di distribuzione Resource Manager.

- Per distribuire numerose VM serie N, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.

- Potrebbe essere necessario aumentare la quota di core (per area) nella sottoscrizione di Azure e aumentare la quota separata per i core NP. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../azure-portal/supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per il calcolo](sizes-compute.md)
- [Calcolo con accelerazione GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
