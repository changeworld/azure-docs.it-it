---
title: Risolvere i problemi del pool di capacità per Azure NetApp Files | Microsoft Docs
description: Descrive i potenziali problemi che possono verificarsi durante la gestione dei pool di capacità e fornisce soluzioni per i problemi.
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 759759b67582b241d0bab1e043dd15e54a804faf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251540"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Risolvere i problemi relativi ai pool di capacità

Questo articolo descrive le soluzioni ai problemi che potrebbero verificarsi durante la gestione dei pool di capacità, inclusa l'operazione di modifica del pool. 

## <a name="issues-managing-a-capacity-pool"></a>Problemi di gestione di un pool di capacità 

|     Condizione di errore    |     Soluzione    |
|-|-|
| Problemi di creazione di un pool di capacità |  Verificare che il numero di pool di capacità non superi il limite. Vedere [limiti delle risorse per Azure NetApp files](azure-netapp-files-resource-limits.md).  Se il conteggio è inferiore al limite e si verificano ancora problemi, archiviare un ticket di supporto e specificare il nome del pool di capacità. |
| Problemi di eliminazione di un pool di capacità  |  Assicurarsi di rimuovere tutti Azure NetApp Files volumi e snapshot nella sottoscrizione in cui si sta provando a eliminare il pool di capacità. <br> Se sono già stati rimossi tutti i volumi e gli snapshot e non è ancora possibile eliminare il pool di capacità, i riferimenti alle risorse potrebbero ancora esistere senza essere visualizzati nel portale. In questo caso, archiviare un ticket di supporto e specificare di aver eseguito i passaggi indicati in precedenza. |
| Creazione o modifica del volume non riuscita con `Requested throughput not available` errore | La velocità effettiva disponibile per un volume è determinata dalle dimensioni del pool di capacità e dal livello di servizio. Se la velocità effettiva non è sufficiente, è necessario aumentare le dimensioni del pool o regolare la velocità effettiva del volume esistente. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problemi durante la modifica del pool di capacità di un volume 

|     Condizione di errore    |     Soluzione    |
|-|-|
| La modifica del pool di capacità per un volume non è consentita. | L'utente potrebbe non essere ancora autorizzato a usare questa funzionalità. <br> La funzionalità per spostare un volume in un altro pool di capacità è attualmente in fase di anteprima. Se si utilizza questa funzionalità per la prima volta, è necessario prima registrare la funzionalità e impostare `-FeatureName ANFTierChange` . Vedere la procedura di registrazione in [modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md). |
| Dimensioni del pool di capacità troppo ridotte per le dimensioni totali del volume. |  L'errore è dovuto al fatto che il pool di capacità di destinazione non dispone della capacità disponibile per il volume spostato.  <br> Aumentare le dimensioni del pool di destinazione o scegliere un altro pool più grande.  Vedere [ridimensionare un pool di capacità o un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  Non è possibile completare la modifica del pool perché un volume denominato `'{source pool name}'` esiste già nel pool di destinazione `'{target pool name}'` | Questo errore si verifica perché il volume con lo stesso nome esiste già nel pool di capacità di destinazione.  Selezionare un altro pool di capacità che non disponga di un volume con lo stesso nome.   | 

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
* [Modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md)
* [Ridimensionare un pool di capacità o un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
