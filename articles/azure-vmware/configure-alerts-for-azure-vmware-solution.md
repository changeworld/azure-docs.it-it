---
title: Configurare gli avvisi e usare le metriche nella soluzione VMware di Azure
description: Informazioni su come usare gli avvisi per ricevere notifiche. Scopri anche come usare le metriche per ottenere informazioni più approfondite sul cloud privato della soluzione VMware di Azure.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: f021662658399111187e9963fc5caec434fabf4a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096838"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Configurare gli avvisi di Azure in una soluzione VMware di Azure 

Questo articolo illustra come configurare i gruppi di azioni di [Azure](/azure/azure-monitor/alerts/action-groups) in [Microsoft Azure avvisi](/azure/azure-monitor/alerts/alerts-overvie) per ricevere notifiche di eventi attivati definiti dall'utente. Si apprenderà anche come usare le [metriche di monitoraggio di Azure](/azure/azure-monitor/essentials/data-platform-metrics) per ottenere informazioni più approfondite sul cloud privato della soluzione VMware di Azure.


## <a name="supported-metrics-and-activities"></a>Metriche e attività supportate

Le metriche seguenti sono visibili tramite le metriche di monitoraggio di Azure.

| **Nome del segnale**                                                         | **Tipo di segnale** | **Monitorare il servizio** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Capacità totale del disco dell'archivio dati                                           | Metrica          | Piattaforma            |
| Percentuale di disco archivio dati utilizzata                                          | Metrica          | Piattaforma            |
| CPU percentuale                                                          | Metrica          | Piattaforma            |
| Memoria effettiva media                                                | Metrica          | Piattaforma            |
| Overhead medio della memoria                                                 | Metrica          | Piattaforma            |
| Memoria totale media                                                    | Metrica          | Piattaforma            |
| Utilizzo medio memoria                                                    | Metrica          | Piattaforma            |
| Disco del datastore usato                                                     | Metrica          | Piattaforma            |
| Tutte le operazioni amministrative                                           | Log attività    | Amministrativo      |
| Registrare il provider di risorse Microsoft. AVS. (Microsoft. AVS/privateClouds) | Log attività    | Amministrativo      |
| Crea o aggiorna un PrivateCloud. (Microsoft. AVS/privateClouds)          | Log attività    | Amministrativo      |
| Eliminare un PrivateCloud. (Microsoft. AVS/privateClouds)                    | Log attività    | Amministrativo      |

## <a name="configure-an-alert-rule"></a>Configurare una regola di avviso
1. Dal cloud privato della soluzione VMware di Azure selezionare   >  **avvisi** di monitoraggio e quindi **nuova regola di avviso**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Screenshot che mostra dove configurare una regola di avviso nel cloud privato della soluzione VMware di Azure." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Verrà visualizzata una nuova schermata di configurazione in cui è possibile:
   - Definire l'ambito
   - Configurare una condizione
   - Configurare il gruppo di azioni
   - Definisci i dettagli della regola di avviso
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Screenshot che mostra la finestra Crea regola di avviso." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. In **ambito** selezionare la risorsa di destinazione che si vuole monitorare. Per impostazione predefinita, il cloud privato della soluzione VMware di Azure da cui è stato aperto il menu avvisi è stato definito.

1. In **condizione** selezionare **Aggiungi condizione** e nella finestra visualizzata selezionare il segnale che si desidera creare per la regola di avviso. 

   In questo esempio è stato selezionato il **disco del datastore percentuale usato**, che è pertinente dal punto di vista del [contratto di soluzione VMware di Azure](https://aka.ms/avs/sla) . 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Screenshot che mostra la finestra Configura logica del segnale con i nomi dei segnali predefiniti."::: 

1. Definire la logica che attiverà l'avviso e quindi fare clic su **fine**. 

   In questo esempio sono state modificate solo la **soglia** e la **frequenza di valutazione** . 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Screenshot che mostra le informazioni per la logica del segnale selezionata."::: 

1. In **azioni** selezionare **Aggiungi gruppi di azioni**. Il gruppo di azioni definisce la *modalità* di ricezione della notifica e l'utente *che* la riceve.   È possibile ricevere notifiche tramite posta elettronica, SMS, [notifiche push dell'app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) o messaggio vocale.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Screenshot che mostra i gruppi di azioni esistenti e dove creare un nuovo gruppo di azioni.":::

1. Nella finestra visualizzata selezionare **Crea gruppo di azioni**.

   >[!TIP]
   > È anche possibile usare un gruppo di azioni esistente.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Screenshot che mostra i gruppi di azione da selezionare per l'avviso."::: 

 

 
1. Nella finestra che viene visualizzata, nella scheda informazioni di **base** assegnare un nome al gruppo di azioni e un nome visualizzato.

1. Selezionare la scheda **notifiche** , selezionare un **tipo di notifica** e un **nome**. Selezionare **OK**.

   Questo esempio è basato sulla notifica tramite posta elettronica.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Screenshot che mostra le impostazioni relative alla posta elettronica, al messaggio SMS, al push e alla voce per l'avviso." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Opzionale Configurare le **azioni** se si desidera eseguire azioni proattive e ricevere notifiche sull'evento. Selezionare un **tipo di azione** disponibile, quindi selezionare **Verifica + crea**. 
   - Automation Runbooks
   - Funzioni di Azure: per l'esecuzione personalizzata di codice senza server basata su eventi
   - ITSM: per l'integrazione con un provider di servizi come ServiceNow per creare un ticket
   - App per la logica: per l'orchestrazione di flussi di lavoro
   - Webhook: per attivare un processo in un altro servizio

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Screenshot che mostra la finestra Crea gruppo di azioni con lo stato attivo sull'elenco a discesa tipo di azione." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. In **Dettagli regola di avviso** specificare un nome, una descrizione, un gruppo di risorse in cui archiviare la regola di avviso, ovvero la gravità. Quindi selezionare **Crea regola di avviso**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Screenshot che mostra i dettagli della regola di avviso."::: 
 
   La regola di avviso è visibile e può essere gestita dal portale di Azure.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Screenshot che mostra la nuova regola di avviso nella finestra regole." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Non appena una metrica raggiunge la soglia definita in una regola di avviso, il menu **avvisi** viene aggiornato e reso visibile.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Screenshot che mostra l'avviso dopo aver raggiunto la soglia definita." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   A seconda del gruppo di azioni configurato, si riceverà una notifica tramite il supporto configurato. In questo esempio è stato configurato un messaggio di posta elettronica.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Screenshot di un avviso di monitoraggio di Azure con la stringa di errore e l'evento di data e ora è stato attivato."::: 

## <a name="work-with-metrics"></a>Usare le metriche

1. Dal cloud privato della soluzione VMware di Azure selezionare   >  **metriche** di monitoraggio. Quindi selezionare la metrica desiderata dall'elenco a discesa.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Screenshot che mostra la finestra metriche e uno stato attivo sull'elenco a discesa della metrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. È possibile modificare i parametri del diagramma, ad esempio l' **intervallo di tempo** o la **granularità temporale**. 

   Altre opzioni sono:
   - **Esaminare i log** ed eseguire query sui dati nell'area di lavoro log Analytics correlata
   - **Aggiungere questo diagramma** a un dashboard di Azure per praticità.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Screenshot che mostra l'intervallo di tempo e le opzioni di granularità temporali per la metrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Passaggi successivi

Ora che è stata configurata una regola di avviso per il cloud privato della soluzione VMware di Azure, è possibile saperne di più su:
- [Metriche di Monitoraggio di Azure](/azure/azure-monitor/essentials/data-platform-metrics)
- [Avvisi di Monitoraggio di Azure](/azure/azure-monitor/alerts/alerts-overview)
- [Gruppi di azioni di Azure](/azure/azure-monitor/alerts/action-groups)

È anche possibile continuare con una delle altre guide alle procedure per la [soluzione VMware di Azure](index.yml) .





