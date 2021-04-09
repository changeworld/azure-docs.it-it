---
title: Spostare le sottoscrizioni della soluzione VMware di Azure EA e CSP
description: Informazioni su come spostare il cloud privato da una sottoscrizione a un'altra. Lo spostamento può essere effettuato per diversi motivi, ad esempio per la fatturazione.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555616"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Spostare le sottoscrizioni della soluzione VMware di Azure EA e CSP

In questo articolo si apprenderà come spostare il cloud privato da una sottoscrizione a un'altra. Lo spostamento può essere effettuato per diversi motivi, ad esempio per la fatturazione. 

>[!IMPORTANT]
>È necessario avere almeno diritti di collaboratore per le sottoscrizioni di origine e di destinazione. Il gateway VNet e VNet non può essere spostato da una sottoscrizione a un'altra. Inoltre, lo stato di trasferimento delle sottoscrizioni non ha alcun effetto sulla gestione e sui carichi di lavoro, ad esempio le macchine virtuali vCenter, NSX e del carico di lavoro.

1. Accedere al portale di Azure e selezionare il cloud privato che si desidera spostare.

1. Selezionare il collegamento **sottoscrizione (modifica)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Screenshot che mostra i dettagli del cloud privato.":::

1. Specificare i dettagli della sottoscrizione per **destinazione** e selezionare **Avanti**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Screenshot della risorsa di destinazione." lightbox="media/move-resources-subscription-target.png":::

1. Confermare la convalida delle risorse selezionate per lo spostamento e selezionare **Avanti**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Screenshot che mostra la risorsa spostata." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Selezionare la casella di controllo che indica che gli strumenti e gli script associati non funzioneranno finché non vengono aggiornati per l'uso dei nuovi ID risorsa. Quindi selezionare **Sposta**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Screenshot che mostra il riepilogo della risorsa selezionata spostata. " lightbox="media/review-move-resources-subscription-target.png":::

   Una volta completata lo spostamento delle risorse, viene visualizzata una notifica. La nuova sottoscrizione verrà visualizzata nella panoramica del cloud privato.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Screenshot che mostra una nuova sottoscrizione." lightbox="media/moved-subscription-target.png":::

