---
title: Trovare un acquirente di prenotazioni dai log Monitoraggio di Azure prenotazione
description: Questo articolo consente di trovare un acquirente di prenotazioni con le informazioni Monitoraggio di Azure log.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773373"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Trovare un acquirente di prenotazioni dai log di Azure

Questo articolo consente di trovare un acquirente di prenotazioni con le informazioni contenute nei log della directory. I log della directory Monitoraggio di Azure gli ID di posta elettronica degli utenti che hanno effettuato acquisti di prenotazioni.

## <a name="find-the-purchaser"></a>Trovare l'acquirente

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Monitor** Activity Log Activity Activity  >  **(Monitoraggio attività log**  >  **attività).**  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Screenshot che mostra la navigazione in Log attività - Attività." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Selezionare **Attività directory.** Se viene visualizzato il messaggio È necessaria *l'autorizzazione* per [](../../role-based-access-control/elevate-access-global-admin.md) visualizzare i log a livello di directory, selezionare il collegamento per informazioni su come ottenere le autorizzazioni.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Screenshot che mostra l'attività della directory senza l'autorizzazione per visualizzare il log." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Dopo aver creato l'autorizzazione, **filtrare Il provider di risorse tenant** con **Microsoft.Capacity**. Verranno visualizzati tutti gli eventi correlati alla prenotazione per l'intervallo di tempo selezionato. Se necessario, modificare l'intervallo di tempo.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Screenshot che mostra l'utente che ha acquistato la prenotazione." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Se necessario, potrebbe essere necessario modificare **le colonne** per selezionare Evento **avviato da**.
   L'utente che ha effettuato l'acquisto della prenotazione viene visualizzato **in Evento avviato da**.

## <a name="next-steps"></a>Passaggi successivi

- Se necessario, gli amministratori della fatturazione [possono assumere la proprietà di una prenotazione](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations).
