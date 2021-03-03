---
title: Gestire le notifiche di avviso per gli eventi del dispositivo per le risorse Pro di Azure Stack Edge | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire gli avvisi per gli eventi del dispositivo nelle risorse di Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: c621584fa1dbed06873ac0867e20201e94c4a757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699682"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Gestire le notifiche di avviso per gli eventi di dispositivo sulle risorse Pro di Azure Stack Edge

Questo articolo descrive come creare regole di azione nel portale di Azure per attivare o disattivare le notifiche di avviso per gli eventi del dispositivo che si verificano all'interno di un gruppo di risorse, una sottoscrizione di Azure o una singola risorsa di Azure Stack Edge. Questo articolo si applica a tutti i modelli di Azure Stack Edge.  

## <a name="about-action-rules"></a>Informazioni sulle regole di azione

Una regola di azione può attivare o disattivare le notifiche di avviso. La regola azione viene aggiunta a un *gruppo di azioni* , ovvero un set di preferenze di notifica usato per notificare agli utenti la necessità di agire sugli avvisi attivati in contesti diversi per una risorsa o un set di risorse.

Per ulteriori informazioni sulle regole di azione, vedere [configurazione di una regola di azione](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). Per ulteriori informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azioni nella portale di Azure](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> La funzionalità regole di azione è in anteprima. Alcune schermate e passaggi potrebbero cambiare quando il processo viene perfezionato.


## <a name="create-an-action-rule"></a>Creare una regola di azione

Eseguire la procedura seguente nel portale di Azure per creare una regola di azione per il dispositivo Azure Stack Edge.

> [!NOTE]
> Questi passaggi consentono di creare una regola di azione che invia notifiche a un gruppo di azione. Per informazioni dettagliate sulla creazione di una regola di azione per eliminare le notifiche, vedere [configurazione di una regola di azione](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. Passare al dispositivo Azure Stack Edge nella portale di Azure, quindi passare a **monitoraggio > avvisi**. Selezionare **Gestisci azioni**.

   ![Monitoraggio degli avvisi, gestione della visualizzazione delle azioni](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Selezionare **regole azione (anteprima)**, quindi selezionare **+ nuova regola azione**.

   ![Gestisci azioni, opzione regole azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Nella schermata **Crea regola azione** usare **ambito** per selezionare una sottoscrizione di Azure, un gruppo di risorse o una risorsa di destinazione. La regola di azione agirà su tutti gli avvisi generati all'interno di tale ambito.

   1. Scegliere **Seleziona** per **ambito**.

      ![Selezionare un ambito per una nuova regola di azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Selezionare la **sottoscrizione** per la regola di azione, facoltativamente applicare un filtro in base a un tipo di **risorsa** . Per filtrare le risorse di Azure Stack Edge, selezionare **dispositivi data box Edge (dataBoxEdge)**.

      L'area **risorse** elenca le risorse disponibili in base alle selezioni effettuate.
  
      ![Risorse disponibili nella schermata Seleziona ambito](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Selezionare la casella di controllo per ogni risorsa a cui si vuole applicare la regola. È possibile selezionare la sottoscrizione, i gruppi di risorse o le singole risorse. Al termine selezionare **Done** (Fine).

      ![Impostazioni di esempio per un ambito della regola azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      La schermata **Crea regola azione** Mostra l'ambito selezionato.

      ![Ambito completato per una regola di azione Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Usare le opzioni di **filtro** per limitare l'applicazione della regola al subset di avvisi nell'ambito selezionato.

   1. Selezionare **Aggiungi** per aprire il riquadro **Aggiungi filtri** .

      ![Opzione per l'aggiunta di filtri a una regola di azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. In **filtri** aggiungere ogni filtro che si desidera applicare. Per ogni filtro, selezionare il tipo di filtro, l' **operatore** e il **valore**.
   
      Per un elenco delle opzioni di filtro, vedere [criteri di filtro](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria).

      I filtri di esempio riportati di seguito si applicano a tutti gli avvisi a livello di gravità 2, 3 e 4 che il servizio di monitoraggio genera per le risorse di Azure Stack Edge.

      Al termine dell'aggiunta dei filtri, selezionare **fine**.
   
      ![Filtro di esempio per una regola di azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Nella schermata **Crea regola azione** selezionare gruppo di **azioni** per creare una regola che invii le notifiche. Quindi, per **azioni**, scegliere **Seleziona**.

   ![Opzione del gruppo di azioni per la creazione di una regola di azione che invia le notifiche](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Per creare una regola che elimini le notifiche, scegliere **soppressione**. Per ulteriori informazioni, vedere [configurazione di una regola di azione](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

6. Selezionare il gruppo di azioni che si desidera utilizzare con questa regola di azione. Quindi scegliere **Seleziona**. La nuova regola azione verrà aggiunta alle preferenze di notifica del gruppo di azioni selezionato.

   Se è necessario creare un nuovo gruppo di azione, selezionare **+ Crea gruppo di azioni** e seguire i passaggi descritti in [creare un gruppo di azione usando il portale di Azure](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Selezionare un gruppo di azioni da usare con la regola, quindi scegliere Seleziona.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Assegnare alla nuova regola di azione un **nome** e una **Descrizione** e assegnare la regola a un gruppo di risorse.

9. La nuova regola è abilitata per impostazione predefinita. Se non si vuole iniziare subito a usare la regola, selezionare **No** per **abilitare la creazione degli aggiornamenti della regola**.

10. Al termine delle impostazioni, selezionare **Crea**.

    ![Impostazioni completate per una regola di azione che invierà notifiche di avviso](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    Viene visualizzata la schermata **regole azione (anteprima)** , ma la nuova regola azione potrebbe non essere visualizzata immediatamente. Lo stato attivo è **tutti** i gruppi di risorse.

11. Per visualizzare la nuova regola azione, selezionare il gruppo di risorse per la regola.

    ![Schermata regole azione con la nuova regola visualizzata](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Visualizzare le notifiche

Le notifiche vengono esposte quando un nuovo evento attiva un avviso per una risorsa che rientra nell'ambito di una regola di azione.

Il gruppo di azioni per un set di regole che riceve una notifica e il tipo di notifica inviato-posta elettronica, un messaggio SMS (Short Message Service) o entrambi.

Potrebbero essere necessari alcuni minuti per ricevere le notifiche dopo l'attivazione di un avviso.

La notifica di posta elettronica sarà simile a questa.

![Notifica di posta elettronica di esempio per una regola di azione](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Passaggi successivi

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Per informazioni su come esaminare gli eventi del dispositivo, lo stato dell'hardware e i grafici delle metriche, vedere [monitorare il Azure stack Edge Pro](azure-stack-edge-monitor.md) . 
- Vedere [uso di monitoraggio di Azure](azure-stack-edge-gpu-enable-azure-monitor.md) per informazioni sull'ottimizzazione di monitoraggio di Azure per i dispositivi GPU Pro Azure stack Edge.
- Per informazioni sulla gestione di singoli avvisi, vedere [creare, visualizzare e gestire gli avvisi delle metriche usando la destinazione del collegamento di monitoraggio di Azure](../azure-monitor/alerts/alerts-metric.md) .