---
title: Ridimensionare un volume o pool di capacità per Azure NetApp Files | Microsoft Docs
description: Informazioni su come modificare le dimensioni di un pool di capacità o di un volume. Il ridimensionamento del pool di capacità modifica la capacità di Azure NetApp Files acquistata.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594479"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ridimensionare un pool di capacità o un volume
È possibile modificare le dimensioni di un pool o un volume di capacità in base alle esigenze. 

## <a name="resize-the-capacity-pool"></a>Ridimensionare il pool di capacità 

È possibile modificare le dimensioni del pool di capacità in incrementi di 1 TiB o decrementi. Tuttavia, le dimensioni del pool di capacità non possono essere inferiori a 4 TiB. Il ridimensionamento del pool di capacità modifica la capacità di Azure NetApp Files acquistata.

1. Nel pannello Gestisci account NetApp fare clic sul pool di capacità che si intende ridimensionare. 
2. Fare clic con il pulsante destro del mouse sul nome del pool di capacità oppure fare clic sull'icona "..." alla fine della riga del pool di capacità per visualizzare il menu di scelta rapida. 
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il pool di capacità.

## <a name="resize-a-volume"></a>Ridimensionare un volume

È possibile modificare le dimensioni di un volume in base alle esigenze. L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.

1. Nel pannello Gestisci account NetApp fare clic su **Volumi**. 
2. Fare clic con il pulsante destro del mouse sul nome del volume da ridimensionare oppure fare clic sull'icona "..." alla fine della riga del volume per visualizzare il menu di scelta rapida.
3. Usare le opzioni del menu di scelta rapida per ridimensionare o eliminare il volume.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Ridimensionare un volume di destinazione della replica tra aree 

In una relazione di [replica tra aree](cross-region-replication-introduction.md) , un volume di destinazione viene ridimensionato automaticamente in base alle dimensioni del volume di origine. Di conseguenza, non è necessario ridimensionare il volume di destinazione separatamente. Questo comportamento di ridimensionamento automatico è applicabile quando i volumi si trovano in una relazione di replica attiva o quando il peering della replica è interruppe con l' [operazione di risincronizzazione](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

Nella tabella seguente viene descritto il comportamento del ridimensionamento del volume di destinazione in base allo [stato di mirroring](cross-region-replication-display-health-status.md):

|  Stato del mirroring  | Comportamento di ridimensionamento del volume di destinazione |
|-|-|
| *Con mirroring* | Quando il volume di destinazione è stato inizializzato ed è pronto per la ricezione degli aggiornamenti del mirroring, il ridimensionamento del volume di origine ridimensiona automaticamente i volumi di destinazione. |
| *Rotto* | Quando si ridimensiona il volume di origine e lo stato del mirror viene *danneggiato*, il volume di destinazione viene ridimensionato automaticamente con l' [operazione di risincronizzazione](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Inizializzazione annullata* | Quando si ridimensiona il volume di origine e lo stato di mirroring non è ancora *inizializzato*, è necessario eseguire manualmente il ridimensionamento del volume di destinazione. Di conseguenza, è consigliabile attendere il completamento dell'inizializzazione (ovvero, quando lo stato del mirror viene sottoposto a *mirroring*) per ridimensionare il volume di origine. | 

> [!IMPORTANT]
> Assicurarsi di disporre di spazio sufficiente nei pool di capacità per i volumi di origine e di destinazione della replica tra aree. Quando si ridimensiona il volume di origine, il volume di destinazione viene ridimensionato automaticamente. Tuttavia, se il pool di capacità che ospita il volume di destinazione non dispone di spazio sufficiente, il ridimensionamento dei volumi di origine e di destinazione avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
- [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
- [Modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md) 