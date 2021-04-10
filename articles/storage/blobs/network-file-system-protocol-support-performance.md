---
title: Considerazioni sulle prestazioni di NFS 3,0 nell'archivio BLOB di Azure (anteprima) | Microsoft Docs
description: Ottimizzare le prestazioni delle richieste di archiviazione NFS (Network File System) 3,0 usando le indicazioni fornite in questo articolo.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 28fb5ffc0ce9c554294eaca9e460694856cb95a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589872"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Considerazioni sulle prestazioni di Network File System (NFS) 3,0 nell'archivio BLOB di Azure (anteprima)

L'archiviazione BLOB supporta ora il protocollo NFS (Network File System) 3,0. Questo articolo contiene indicazioni che consentono di ottimizzare le prestazioni delle richieste di archiviazione. Per altre informazioni sul supporto per NFS 3,0 nell'archiviazione BLOB di Azure, vedere [supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)](network-file-system-protocol-support.md).

> [!NOTE]
> Il supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure è in versione di anteprima pubblica. Supporta gli account di archiviazione GPV2 con prestazioni del livello standard nelle aree seguenti: Australia orientale, Corea centrale e Stati Uniti centro-meridionali. L'anteprima supporta anche il BLOB in blocchi con livello di prestazioni Premium in tutte le aree pubbliche.

## <a name="add-clients-to-increase-throughput"></a>Aggiungere i client per aumentare la velocità effettiva 

L'archiviazione BLOB di Azure viene ridimensionata in modo lineare fino a raggiungere il limite massimo in uscita e in ingresso dell'account di archiviazione. Pertanto, le applicazioni possono ottenere una velocità effettiva maggiore utilizzando più client.  Per visualizzare i limiti in uscita e in ingresso dell'account di archiviazione, vedere [obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md).

Il grafico seguente mostra come aumenta la larghezza di banda Man mano che si aggiungono altri client. In questo grafico, un client è una macchina virtuale (VM) e l'account usa il livello di prestazioni standard. 

> [!div class="mx-imgBorder"]
> ![Prestazioni standard](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Il grafico seguente mostra questo stesso effetto se applicato a un account che usa il livello di prestazioni Premium.

> [!div class="mx-imgBorder"]
> ![Prestazioni Premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Usare il livello di prestazioni Premium per le applicazioni su scala ridotta

Non tutte le applicazioni possono essere ridimensionate aggiungendo più client. Per queste applicazioni, l' [account di archiviazione BLOB in blocchi di Azure Premium](storage-blob-create-account-block-blob.md) offre una bassa latenza coerente e tassi di transazione elevati. L'account di archiviazione BLOB in blocchi Premium può raggiungere una larghezza di banda massima con meno thread e client. Con un singolo client, ad esempio, un account di archiviazione BLOB in blocchi Premium può ottenere una larghezza di banda di **2,3 x** rispetto alla stessa configurazione usata con un account di archiviazione standard per le prestazioni per utilizzo generico V2. 

Ogni barra nel grafico seguente mostra la differenza tra gli account di archiviazione prestazioni Premium e standard e la larghezza di banda garantita. Con l'aumentare del numero di client, la differenza diminuisce.  

> [!div class="mx-imgBorder"]
> ![Prestazioni relative](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Evitare sovrascritture frequenti sui dati

Il completamento di un'operazione di sovrascrittura richiede più tempo rispetto a una nuova operazione di scrittura. Questo perché un'operazione di sovrascrittura NFS, in particolare una modifica parziale del file sul posto, è una combinazione di diverse operazioni BLOB sottostanti: un'operazione di lettura, modifica e scrittura. Pertanto, un'applicazione che richiede modifiche frequenti sul posto non è adatta per gli account di archiviazione BLOB abilitati per NFS. 

## <a name="other-best-practice-recommendations"></a>Altre procedure consigliate 

- Usare VM con una larghezza di banda di rete sufficiente.

- Usare più punti di montaggio quando i carichi di lavoro lo consentono.

- Usare il maggior numero di thread possibile.

- Utilizzare dimensioni di blocchi di grandi dimensioni.

- Eseguire richieste di archiviazione da un client che si trova nella stessa area dell'account di archiviazione. Questo può migliorare la latenza di rete.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul supporto per NFS 3,0 nell'archiviazione BLOB di Azure, vedere [supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)](network-file-system-protocol-support.md).

- Per iniziare, vedere [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)](network-file-system-protocol-support-how-to.md).
