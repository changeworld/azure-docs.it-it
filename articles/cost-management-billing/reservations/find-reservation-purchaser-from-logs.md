---
title: Trovare un acquirente di prenotazione da log di monitoraggio di Azure
description: Questo articolo consente di trovare un acquirente di prenotazione con le informazioni dei log di monitoraggio di Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: eedb5e7a55b50a353fd16498500b891e289e61e5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477055"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Trovare un acquirente di prenotazione da log di Azure

Questo articolo consente di trovare un acquirente di prenotazione con le informazioni provenienti dai log di directory. I registri di directory da monitoraggio di Azure mostrano gli ID di posta elettronica degli utenti che hanno effettuato acquisti di prenotazione.

## <a name="find-the-purchaser"></a>Trovare l'acquirente

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **monitoraggio**  >  **attività log attività**  >  .  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Screenshot che mostra la navigazione a log attività-Activity." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Selezionare **attività directory**. Se viene visualizzato un messaggio che informa *che è necessaria l'autorizzazione per visualizzare i log a livello di directory*, selezionare il [collegamento](../../role-based-access-control/elevate-access-global-admin.md) per informazioni su come ottenere le autorizzazioni.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Screenshot che mostra l'attività della directory senza autorizzazione per visualizzare il log." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Dopo aver ottenuto l'autorizzazione, filtrare il **provider di risorse tenant** con **Microsoft. Capacity**. Verranno visualizzati tutti gli eventi relativi alla prenotazione per l'intervallo di tempo selezionato. Se necessario, modificare l'intervallo di tempo.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Screenshot che mostra l'utente che ha acquistato la prenotazione." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Se necessario, potrebbe essere necessario **modificare le colonne** per selezionare l' **evento avviato da**.
   L'utente che ha effettuato l'acquisto della prenotazione viene visualizzato in **caso di evento avviato da**.

## <a name="next-steps"></a>Passaggi successivi

- Se necessario, gli amministratori della fatturazione possono [assumere la proprietà di una prenotazione](view-reservations.md#how-billing-administrators-view-or-manage-reservations).