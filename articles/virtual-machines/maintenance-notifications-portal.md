---
title: Usare il portale per le notifiche di manutenzione
description: Visualizzare le notifiche di manutenzione per le macchine virtuali in esecuzione in Azure e avviare la manutenzione Self-Service usando il portale.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557710"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Gestione delle notifiche di manutenzione pianificata tramite il portale

**Questo articolo si applica alle macchine virtuali che eseguono sia Linux che Windows.**

Una volta pianificata un'ondata di [manutenzione pianificata](maintenance-notifications.md) , è possibile verificare la presenza di un elenco di macchine virtuali interessate. 

È possibile usare il portale di Azure per cercare le macchine virtuali pianificate per la manutenzione.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel percorso di spostamento a sinistra fare clic su **macchine virtuali**.

3. Nel riquadro macchine virtuali fare clic sul pulsante **modifica colonne** per aprire l'elenco delle colonne disponibili.

4. Selezionare e aggiungere le colonne seguenti:

   **Stato manutenzione**: Mostra lo stato di manutenzione della macchina virtuale. Ecco di seguito i valori possibili:
      
    | Valore | Descrizione |
    |-------|-------------|
    | Avvia ora | La macchina virtuale si trova nella finestra di manutenzione self-service che consente di avviare manualmente la manutenzione. Vedere di seguito per informazioni su come avviare la manutenzione nella macchina virtuale. | 
    | Scheduled | La macchina virtuale è pianificata per la manutenzione senza alcuna opzione per avviare la manutenzione. È possibile apprendere la finestra di manutenzione selezionando la finestra manutenzione pianificata in questa visualizzazione o facendo clic sulla macchina virtuale. | 
    | Già aggiornato | La macchina virtuale è già aggiornata e al momento non sono necessarie altre azioni. | 
    | Riprova più tardi | La manutenzione è stata avviata, ma senza successo. Sarà possibile utilizzare l'opzione di manutenzione Self-Service in un secondo momento. | 
    | Riprova adesso | È possibile ritentare una manutenzione avviata in precedenza non riuscita. | 
    | - | La macchina virtuale non fa parte di un'ondata di manutenzione pianificata. |

   **Finestra Manutenzione - Self-service** - Mostra la finestra temporale in cui è possibile avviare autonomamente la manutenzione delle macchine virtuali.
   
   **Finestra Manutenzione - Pianificata**: Mostra la finestra temporale in cui Azure competerà la manutenzione della macchina virtuale. 



## <a name="notification-and-alerts-in-the-portal"></a>Notifiche e avvisi del portale

Azure comunica il programma di una manutenzione pianificata inviando un messaggio di posta elettronica al proprietario e ai comproprietari della sottoscrizione. È possibile aggiungere altri destinatari e canali a questa comunicazione creando avvisi del log attività di Azure. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](../service-health/alerts-activity-log-service-notifications-portal.md).

Assicurarsi di impostare il **tipo di evento** come **manutenzione pianificata** e i **Servizi** come **set di scalabilità di macchine virtuali** e/o **macchine virtuali**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Avviare la manutenzione della macchina virtuale dal portale

Mentre si esaminano i dettagli della macchina virtuale, è possibile visualizzare ulteriori dettagli relativi alla manutenzione.  
Nella parte superiore della visualizzazione dei dettagli della macchina virtuale viene aggiunta una nuova barra delle notifiche nel caso in cui la macchina virtuale sia inclusa in un'ondata di manutenzione pianificata. Inoltre, laddove possibile, viene aggiunta una nuova opzione per avviare la manutenzione. 


Fare clic sulla notifica di manutenzione per visualizzare la pagina Manutenzione con maggiori dettagli sulla manutenzione pianificata. Da questa pagina è possibile **avviare la manutenzione** della macchina virtuale.

La manutenzione della macchina virtuale viene avviata e lo stato di manutenzione viene aggiornato per riflettere il risultato entro pochi minuti.

Se si perde la finestra Self-service, è comunque possibile visualizzarla quando viene eseguita la manutenzione della macchina virtuale da Azure. 


## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire la manutenzione pianificata usando l'interfaccia della riga di comando di [Azure](maintenance-notifications-cli.md) o [PowerShell](maintenance-notifications-powershell.md).
