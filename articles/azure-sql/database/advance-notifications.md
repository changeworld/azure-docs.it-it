---
title: Notifiche avanzate (anteprima) per gli eventi di manutenzione pianificata
description: Ricevere una notifica prima della manutenzione pianificata per il database SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 895b9081ba7eb6d7e8b5d3304d37168e4064ed39
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560047"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Notifiche anticipate per gli eventi di manutenzione pianificata (anteprima)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Notifiche avanzate (anteprima) è disponibile per i database configurati per la [finestra di manutenzione (anteprima)](maintenance-window.md). Le notifiche di avanzamento consentono ai clienti di configurare le notifiche da inviare fino a 24 ore prima di qualsiasi evento pianificato.

Le notifiche possono essere configurate in modo da poter ricevere testi, messaggi di posta elettronica, notifiche push di Azure e messaggi vocali quando la manutenzione pianificata è prevista per l'inizio nelle prossime 24 ore. Le notifiche aggiuntive vengono inviate all'inizio della manutenzione e al termine della manutenzione.

> [!Note]
> Sebbene la possibilità di scegliere una finestra di manutenzione sia disponibile per le istanze gestite di SQL Azure, le notifiche anticipate non sono attualmente disponibili per le istanze gestite di SQL Azure.

## <a name="create-an-advance-notification"></a>Creare una notifica avanzata

Sono disponibili notifiche avanzate per i database SQL di Azure in cui è configurata la finestra di manutenzione. 

Completare i passaggi seguenti per abilitare una notifica.  

1. Passare alla pagina [manutenzione pianificata](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , selezionare **avvisi di integrità** e quindi **Aggiungi avviso di integrità del servizio**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="opzione di menu Crea nuovo avviso di integrità":::

2. Nella sezione **azioni** selezionare **Aggiungi gruppi di azioni**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="opzione di menu Aggiungi un gruppo di azioni":::

3. Completare il modulo **Crea gruppo di azioni** , quindi selezionare **Avanti: notifiche**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="modulo Crea gruppo di azioni":::

1. Nella scheda **notifiche** selezionare il tipo di **notifica**. L'opzione **messaggio di posta elettronica/SMS/push/Voice** offre la massima flessibilità ed è l'opzione consigliata. Selezionare la penna per configurare la notifica.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="configurare le notifiche":::



   1. Completare il modulo *Aggiungi o modifica notifica* visualizzato e selezionare **OK**: 

   2. Le azioni e i tag sono facoltativi. Qui è possibile configurare azioni aggiuntive da attivare o usare i tag per suddividere in categorie e organizzare le risorse di Azure. 

   4. Controllare i dettagli nella scheda **Verifica + crea** e selezionare **Crea**. 

7. Dopo aver selezionato Crea, viene visualizzata la schermata Configurazione regola di avviso in cui verrà selezionato il gruppo di azioni. Assegnare un nome alla nuova regola di avviso, quindi scegliere il gruppo di risorse e selezionare **Crea regola di avviso**. 

8. Fare di nuovo clic sulla voce di menu **avvisi di integrità** e l'elenco degli avvisi contiene ora il nuovo avviso. 


Le impostazioni sono state completate. La volta successiva che si verifica un evento di manutenzione SQL di Azure pianificato, si riceverà una notifica di avanzamento.

## <a name="receiving-notifications"></a>Ricezione di notifiche

La tabella seguente illustra le notifiche generali che è possibile ricevere: 

|Stato|Descrizione|
|:---|:---|
|**Distribuzione pianificata**| Sono state ricevute 24 ore prima dell'evento di manutenzione. La manutenzione è pianificata in una data compresa tra le 17.00 e le 8.00 (ora locale) per il database XYZ.|
|**In corso** | Avvio della manutenzione per il database *XYZ* in corso   .| 
|**Operazione completata** | La manutenzione del database *XYZ* è stata completata. |

La tabella seguente illustra le notifiche aggiuntive che possono essere inviate mentre è in corso la manutenzione: 

|Stato|Descrizione|
|:---|:---|
|**Esteso** | La manutenzione è in corso ma non è stata completata per il database *XYZ*. La manutenzione continuerà alla successiva finestra di manutenzione.| 
|**Canceled**| La manutenzione per il database *XYZ* viene annullata e verrà ripianificata in un secondo momento. |
|**Bloccato**|Si è verificato un problema durante la manutenzione per il database *XYZ*. Si riceverà una notifica quando si riprende.| 
|**Resumed**|Il problema è stato risolto e la manutenzione continuerà alla successiva finestra di manutenzione.|


## <a name="next-steps"></a>Passaggi successivi

- [Finestra di manutenzione](maintenance-window.md)
- [Domande frequenti sulla finestra di manutenzione](maintenance-window-faq.yml)
- [Panoramica degli avvisi in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)