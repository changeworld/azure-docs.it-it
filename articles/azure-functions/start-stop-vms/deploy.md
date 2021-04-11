---
title: Distribuire le VM di avvio/arresto V2 (anteprima)
description: Questo articolo illustra come distribuire la funzionalità di avvio/arresto di macchine virtuali V2 (anteprima) per le macchine virtuali di Azure nella sottoscrizione di Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111979"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Distribuire le VM di avvio/arresto V2 (anteprima)

Eseguire i passaggi descritti in questo argomento in sequenza per installare la funzionalità avvia/arresta VM V2 (anteprima). Dopo aver completato il processo di installazione, configurare le pianificazioni per personalizzarlo in base alle proprie esigenze.

## <a name="deploy-feature"></a>Distribuisci funzionalità

La distribuzione viene avviata dall'organizzazione di GitHub Start/Stop VM v2 [qui](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md). Sebbene questa funzionalità sia destinata a gestire tutte le VM nella sottoscrizione in tutti i gruppi di risorse da una singola distribuzione all'interno della sottoscrizione, è possibile installarne un'altra in base al modello operativo o ai requisiti dell'organizzazione. Può anche essere configurato per gestire centralmente le macchine virtuali tra più sottoscrizioni.

Per semplificare la gestione e la rimozione, è consigliabile distribuire le VM di avvio/arresto V2 (anteprima) a un gruppo di risorse dedicato.

> [!NOTE]
> Attualmente questa anteprima non supporta la specifica di un account di archiviazione esistente o di una risorsa Application Insights.

1. Aprire il browser e passare all'organizzazione Start/Stop VMS v2 [GitHub](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md).
1. Selezionare l'opzione di distribuzione in base all'ambiente cloud di Azure in cui vengono create le VM di Azure. Verrà aperta la pagina di distribuzione Azure Resource Manager personalizzata nel portale di Azure.
1. Se richiesto, accedere al [portale di Azure](https://portal.azure.com).
1. Immettere i valori seguenti:

    |Nome |Valore |
    |-----|------|
    |Region |Selezionare un'area vicina alle nuove risorse.|
    |Nome gruppo di risorse |Specificare il nome del gruppo di risorse che conterrà le singole risorse per le macchine virtuali di avvio/arresto. |
    |Area del gruppo di risorse |Specificare l'area per il gruppo di risorse. Ad esempio **Stati Uniti centrali**.|
    |Nome del app per le funzioni di Azure |digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure. |
    |Nome Application Insights |Specificare il nome dell'istanza di Application Insights che conterrà l'analisi per le macchine virtuali di avvio/arresto. |
    |Area Application Insights |Specificare l'area per l'istanza di Application Insights.|
    |Nome account di archiviazione |Specificare il nome dell'account di archiviazione di Azure per archiviare i dati di telemetria dell'esecuzione di macchine virtuali di avvio/arresto. |
    |Indirizzo di posta elettronica |Specificare uno o più indirizzi di posta elettronica per ricevere notifiche di stato, separate da una virgola (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Avviare/arrestare la configurazione della distribuzione del modello di VM":::

1. Selezionare **Verifica + crea** nella parte inferiore della pagina.
1. Selezionare **Crea** per avviare la distribuzione.
1. Selezionare l'icona a forma di campana delle notifiche nella parte superiore della schermata per visualizzare lo stato della distribuzione. Verrà visualizzato lo stato **Distribuzione in corso**. Attendere il completamento della distribuzione.
1. Selezionare **Vai al gruppo di risorse** nel riquadro di notifica. Verrà visualizzata una schermata simile alla seguente:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Avviare/arrestare l'elenco di risorse di distribuzione modello di VM":::

## <a name="enable-multiple-subscriptions"></a>Abilita più sottoscrizioni

Al termine della distribuzione di avvio/arresto, seguire questa procedura per abilitare l'avvio/arresto di macchine virtuali V2 (anteprima) per eseguire un'azione tra più sottoscrizioni.

1. Copiare il valore per il nome del app per le funzioni di Azure specificato durante la distribuzione.

1. Nel portale passare alla sottoscrizione secondaria. Selezionare la sottoscrizione e quindi selezionare **controllo di accesso (IAM)**

1. Selezionare **Aggiungi** e quindi selezionare **Aggiungi assegnazione ruolo**.

1. Selezionare il ruolo **collaboratore** dall'elenco a discesa **ruolo** .

1. Immettere il nome dell'applicazione per le funzioni di Azure nel campo **Seleziona** . Selezionare il nome della funzione nei risultati.

1. Selezionare **Salva** per eseguire il commit delle modifiche.

## <a name="configure-schedules-overview"></a>Panoramica sulla configurazione delle pianificazioni

Per gestire il metodo di automazione per controllare l'avvio e l'arresto delle macchine virtuali, è necessario configurare una o più app per la logica incluse in base ai propri requisiti.

- Le azioni pianificate per l'avvio e l'arresto sono basate su una pianificazione specificata rispetto a Azure Resource Manager e VM classiche.**ststv2_vms_Scheduled_start** e **ststv2_vms_Scheduled_stop** configurare l'avvio e l'arresto pianificati.

- Sequenced: le azioni di avvio e arresto sono basate su una pianificazione destinata alle macchine virtuali con tag di sequenziazione predefiniti. Sono supportati solo due tag denominati: **sequencestart** e **sequencestop**. **ststv2_vms_Sequenced_start** e **ststv2_vms_Sequenced_stop** configurare l'avvio e l'arresto sequenziate.

    > [!NOTE]
    > Questo scenario supporta solo Azure Resource Manager macchine virtuali.

- Autostop: questa funzionalità viene usata solo per eseguire un'azione di arresto sulle macchine virtuali Azure Resource Manager e classiche in base all'utilizzo della CPU. Può anche trattarsi di un' *azione* basata su pianificazione, che crea avvisi sulle VM e in base alla condizione, l'avviso viene attivato per eseguire l'azione di arresto.**ststv2_vms_AutoStop** configura la funzionalità di arresto automatico.

Se sono necessarie pianificazioni aggiuntive, è possibile duplicare una delle app per la logica fornite usando l'opzione **Clone** nel portale di Azure.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Selezionare l'opzione Clona per duplicare un'app per la logica":::

## <a name="scheduled-start-and-stop-scenario"></a>Scenario di avvio e arresto pianificato

Eseguire la procedura seguente per configurare l'azione di avvio e arresto pianificata per le macchine virtuali Azure Resource Manager e classiche. Ad esempio, è possibile configurare la pianificazione **ststv2_vms_Scheduled_start** per avviarli al mattino quando si è in ufficio e arrestare tutte le macchine virtuali in una sottoscrizione quando si lascia il lavoro serale in base alla pianificazione del **ststv2_vms_Scheduled_stop** .

È supportata la configurazione dell'app per la logica per avviare solo le macchine virtuali.

Per ogni scenario, è possibile indirizzare l'azione a una o più sottoscrizioni, a uno o più gruppi di risorse e specificare una o più macchine virtuali in un elenco di inclusione o di esclusione. Non è possibile specificarli insieme nella stessa app per la logica.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare ad app per la **logica**.

1. Dall'elenco di app per la logica, per configurare l'avvio pianificato, selezionare **ststv2_vms_Scheduled_start**. Per configurare l'arresto pianificato, selezionare **ststv2_vms_Scheduled_stop**.

1. Selezionare **progettazione app** per la logica nel riquadro a sinistra.

1. Dopo aver visualizzato Progettazione app per la logica, nel riquadro di progettazione selezionare **ricorrenza** per configurare la pianificazione dell'app per la logica. Per informazioni sulle opzioni di ricorrenza specifiche, vedere [pianificare attività ricorrenti](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurare la frequenza di ricorrenza per l'app per la logica":::

1. Nel riquadro della finestra di progettazione selezionare **funzione-prova** per configurare le impostazioni di destinazione. Nel corpo della richiesta, se si vuole gestire le macchine virtuali in tutti i gruppi di risorse nella sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Specificare più sottoscrizioni nella `subscriptions` matrice con ogni valore separato da una virgola, come nell'esempio seguente.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Nel corpo della richiesta, se si vuole gestire le macchine virtuali per gruppi di risorse specifici, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare un solo gruppo di risorse.

    Questo esempio illustra anche l'esclusione di una macchina virtuale. È possibile escludere la macchina virtuale specificando il percorso della risorsa VM o il carattere jolly.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Qui l'azione verrà eseguita su tutte le macchine virtuali, ad eccezione del fatto che il nome della macchina virtuale inizia con AZ e BZ in entrambe le sottoscrizioni.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Nel corpo della richiesta, se si vuole gestire un set specifico di macchine virtuali nella sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare una VM.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Scenario di avvio e arresto sequenziato

In un ambiente che include due o più componenti su più macchine virtuali Azure Resource Manager in un'architettura di applicazione distribuita, il supporto della sequenza in cui i componenti vengono avviati e arrestati nell'ordine è importante.

1. Dall'elenco di app per la logica, per configurare l'avvio sequenziato, selezionare **ststv2_vms_Sequenced_start**. Per configurare l'arresto sequenziato, selezionare **ststv2_vms_Sequenced_stop**.

1. Selezionare **progettazione app** per la logica nel riquadro a sinistra.

1. Dopo aver visualizzato Progettazione app per la logica, nel riquadro di progettazione selezionare **ricorrenza** per configurare la pianificazione dell'app per la logica. Per informazioni sulle opzioni di ricorrenza specifiche, vedere [pianificare attività ricorrenti](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurare la frequenza di ricorrenza per l'app per la logica":::

1. Nel riquadro della finestra di progettazione selezionare **funzione-prova** per configurare le impostazioni di destinazione. Nel corpo della richiesta, se si vuole gestire le macchine virtuali in tutti i gruppi di risorse nella sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Specificare più sottoscrizioni nella `subscriptions` matrice con ogni valore separato da una virgola, come nell'esempio seguente.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Nel corpo della richiesta, se si vuole gestire le macchine virtuali per gruppi di risorse specifici, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare un gruppo di risorse.

    Questo esempio illustra anche l'esclusione di una macchina virtuale in base al percorso delle risorse rispetto all'esempio per l'avvio/arresto pianificato, che ha usato caratteri jolly.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Nel corpo della richiesta, se si vuole gestire un set specifico di macchine virtuali all'interno di una sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare una VM.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scenario di arresto automatico

Start/Stop VM V2 (anteprima) consente di gestire il costo di esecuzione di Azure Resource Manager e VM classiche nella sottoscrizione valutando i computer che non vengono usati durante i periodi non di punta, ad esempio dopo le ore, e li arresta automaticamente se l'utilizzo del processore è inferiore a una percentuale specificata.

Le seguenti proprietà di avviso delle metriche nel corpo della richiesta supportano la personalizzazione:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Per altre informazioni sul funzionamento degli avvisi delle metriche di monitoraggio di Azure e su come configurarli, vedere gli [avvisi sulle metriche in monitoraggio di Azure](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Dall'elenco di app per la logica, selezionare **ststv2_vms_AutoStop** per configurare l'arresto automatico.

1. Selezionare **progettazione app** per la logica nel riquadro a sinistra.

1. Dopo aver visualizzato Progettazione app per la logica, nel riquadro di progettazione selezionare **ricorrenza** per configurare la pianificazione dell'app per la logica. Per informazioni sulle opzioni di ricorrenza specifiche, vedere [pianificare attività ricorrenti](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Configurare la frequenza di ricorrenza per l'app per la logica":::

1. Nel riquadro della finestra di progettazione selezionare **funzione-prova** per configurare le impostazioni di destinazione. Nel corpo della richiesta, se si vuole gestire le macchine virtuali in tutti i gruppi di risorse nella sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Nel corpo della richiesta, se si vuole gestire le macchine virtuali per gruppi di risorse specifici, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare un gruppo di risorse.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Nel corpo della richiesta, se si vuole gestire un set specifico di macchine virtuali nella sottoscrizione, modificare il corpo della richiesta come illustrato nell'esempio seguente. Ogni percorso della risorsa specificato deve essere separato da una virgola. Se necessario, è possibile specificare una VM.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come monitorare lo stato delle macchine virtuali di Azure gestite dalla funzionalità avvia/arresta VM V2 (anteprima) ed eseguire altre attività di gestione, vedere l'articolo [gestire le VM di avvio/arresto](manage.md) .