---
title: Benchmark dell'applicazione in archiviazione su disco di Azure
description: Informazioni sul processo di benchmarking delle applicazioni in Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094597"
---
# <a name="benchmark-a-disk"></a>Misurare il benchmark di un disco

Il benchmarking è il processo di simulazione di diversi carichi di lavoro sull'applicazione e di misurazione delle prestazioni dell'applicazione per ogni carico di lavoro. Eseguendo la procedura illustrata nell'[articolo sulla progettazione per prestazioni elevate](premium-storage-performance.md) sono stati raccolti i requisiti relativi alle prestazioni dell'applicazione. Eseguendo gli strumenti di benchmarking nelle macchine virtuali che ospitano l'applicazione, è possibile determinare i livelli di prestazioni che l'applicazione può ottenere con le unità SSD Premium. In questo articolo vengono forniti esempi di benchmarking di una macchina virtuale Standard_D8ds_v4 sottoposta a provisioning con unità SSD Premium di Azure.

Sono stati usati gli strumenti di benchmarking comuni DiskSpd e FIO, rispettivamente per Windows e Linux. Questi strumenti generano più thread che simulano un carico di lavoro analogo a quello di produzione e misurano le prestazioni del sistema. Questi strumenti consentono anche di configurare i parametri quali la dimensione dei blocchi e la profondità della coda, che in genere non possono essere modificati per un'applicazione. Ciò offre una maggiore flessibilità per migliorare le prestazioni in una macchina virtuale a scalabilità elevata con provisioning con unità SSD Premium per diversi tipi di carichi di lavoro dell'applicazione. Per altre informazioni su ogni strumento di benchmarking, vedere [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) e [fio](http://freecode.com/projects/fio).

Per seguire gli esempi riportati di seguito, creare una Standard_D8ds_v4 e aggiungere quattro unità SSD Premium alla macchina virtuale. Dei quattro dischi, configurare tre con la memorizzazione nella cache dell'host come "None" ed eseguirne lo striping in un volume denominato NoCacheWrites. Configurare la memorizzazione nella cache dell'host come "ReadOnly" sul disco rimanente e creare un volume denominato CacheReads con questo disco. Utilizzando questa configurazione, è possibile visualizzare le prestazioni massime di lettura e scrittura da una macchina virtuale Standard_D8ds_v4. Per la procedura dettagliata sulla creazione di un Standard_D8ds_v4 con unità SSD Premium, vedere [progettazione per prestazioni elevate](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare al nostro articolo sulla [progettazione per prestazioni elevate](premium-storage-performance.md).

In questo articolo viene creato un elenco di controllo simile all'applicazione esistente per il prototipo. Usando gli strumenti di benchmarking è possibile simulare i carichi di lavoro e misurare le prestazioni nel prototipo dell'applicazione. Sarà quindi possibile determinare quale offerta di dischi può soddisfare o superare i requisiti relativi alle prestazioni per l'applicazione. Si potranno quindi implementare le stesse indicazioni per l'applicazione di produzione.
