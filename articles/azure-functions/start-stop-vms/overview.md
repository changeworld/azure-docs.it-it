---
title: Panoramica di avvio/arresto di macchine virtuali V2 (anteprima)
description: Questo articolo descrive la versione due della funzionalità di avvio/arresto di macchine virtuali (anteprima), che avvia o arresta Azure Resource Manager e le macchine virtuali classiche in base a una pianificazione.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: aac50f085d3ad63fb0183c19cf7d9aaa679e3d04
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111943"
---
# <a name="startstop-vms-v2-preview-overview"></a>Panoramica di avvio/arresto di macchine virtuali V2 (anteprima)

La funzionalità avvia/arresta VM V2 (anteprima) avvia o arresta macchine virtuali di Azure in più sottoscrizioni. Avvia o arresta le macchine virtuali di Azure nelle pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite [applicazione Azure informazioni dettagliate](../../azure-monitor/app/app-insights-overview.md)e invia notifiche facoltative tramite i [gruppi di azioni](../../azure-monitor/alerts/action-groups.md). La funzionalità può gestire sia Azure Resource Manager VM che macchine virtuali classiche per la maggior parte degli scenari.

Questa nuova versione di Start/Stop VM V2 (anteprima) offre un'opzione di automazione a basso costo decentralizzata per i clienti che vogliono ottimizzare i costi delle macchine virtuali. Offre tutte le stesse funzionalità della [versione originale](../../automation/automation-solution-vm-management.md) disponibile con automazione di Azure, ma è progettata per sfruttare la tecnologia più recente in Azure.

## <a name="overview"></a>Panoramica

Avviare/arrestare le VM V2 (anteprima) è riprogettato e non dipende dai log di automazione di Azure o di monitoraggio di Azure, come richiesto dalla [versione precedente](../../automation/automation-solution-vm-management.md). Questa versione si basa su [funzioni di Azure](../../azure-functions/functions-overview.md) per gestire l'avvio e l'arresto dell'esecuzione della macchina virtuale.

Un'identità gestita viene creata in Azure Active Directory (Azure AD) per questa applicazione funzioni di Azure e consente l'avvio/arresto di macchine virtuali V2 (anteprima) per accedere facilmente ad altre risorse protette da Azure AD, ad esempio le app per la logica e le macchine virtuali di Azure. Per altre informazioni sulle identità gestite in Azure AD, vedere [Identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Viene creata una funzione di endpoint trigger HTTP per supportare gli scenari di pianificazione e sequenza inclusi nella funzionalità, come illustrato nella tabella seguente.

|Nome |Trigger |Descrizione |
|-----|--------|------------|
|AlertAvailabilityTest |Timer |Questa funzione esegue il test di disponibilità per assicurarsi che la funzione primaria **AutoStopVM** sia sempre disponibile.|
|AutoStop |HTTP |Questa funzione supporta lo scenario **autostop** , ovvero la funzione del punto di ingresso chiamata dall'app per la logica.|
|AutoStopAvailabilityTest |Timer |Questa funzione esegue il test di disponibilità per assicurarsi che la funzione primaria **autostop** sia sempre disponibile.|
|AutoStopVM |HTTP |Questa funzione viene attivata automaticamente dall'avviso della macchina virtuale quando la condizione di avviso è true.|
|CreateAutoStopAlertExecutor |Coda |Questa funzione ottiene le informazioni sul payload dalla funzione **autostop** per creare l'avviso sulla macchina virtuale.|
|Scheduled |HTTP |Questa funzione è per lo scenario pianificato e sequenziato (differenziato per lo schema del payload). Si tratta della funzione del punto di ingresso chiamata dall'app per la logica e accetta il payload per elaborare l'operazione di avvio o arresto della macchina virtuale. |
|ScheduledAvailabilityTest |Timer |Questa funzione esegue il test di disponibilità per assicurarsi che la funzione primaria **pianificata** sia sempre disponibile.|
|VirtualMachineRequestExecutor |Coda |Questa funzione esegue l'operazione di avvio e arresto effettiva nella macchina virtuale.|
|VirtualMachineRequestOrchestrator |Coda |Questa funzione ottiene le informazioni sul payload dalla funzione **pianificata** e orchestra le richieste di avvio e arresto della macchina virtuale.|

Ad esempio, la funzione trigger http **pianificata** viene utilizzata per gestire gli scenari di pianificazione e di sequenza. Analogamente, la funzione trigger http **autostop** gestisce lo scenario di arresto automatico.

Le funzioni trigger basate su coda sono necessarie per supportare questa funzionalità. Tutti i trigger basati su timer vengono usati per eseguire il test di disponibilità e per monitorare lo stato del sistema.

 [App](../../logic-apps/logic-apps-overview.md) per la logica di Azure viene usato per configurare e gestire le pianificazioni di avvio e arresto per la macchina virtuale eseguire un'azione chiamando la funzione usando un payload JSON. Per impostazione predefinita, durante la distribuzione iniziale viene creato un totale di cinque app per la logica per gli scenari seguenti:

- Le azioni pianificate per l'avvio e l'arresto sono basate su una pianificazione specificata rispetto a Azure Resource Manager e VM classiche. **ststv2_vms_Scheduled_start** e **ststv2_vms_Scheduled_stop** configurare l'avvio e l'arresto pianificati.

- Sequenced: le azioni di avvio e arresto sono basate su una pianificazione destinata alle macchine virtuali con tag di sequenziazione predefiniti. Sono supportati solo due tag denominati: **sequencestart** e **sequencestop**. **ststv2_vms_Sequenced_start** e **ststv2_vms_Sequenced_stop** configurare l'avvio e l'arresto sequenziate.

    > [!NOTE]
    > Questo scenario supporta solo Azure Resource Manager macchine virtuali.

- Autostop: questa funzionalità viene usata solo per eseguire un'azione di arresto sulle macchine virtuali Azure Resource Manager e classiche in base all'utilizzo della CPU. Può anche essere un' *azione* basata su pianificazione, che crea avvisi sulle macchine virtuali e in base alla condizione, l'avviso viene attivato per eseguire l'azione di arresto. **ststv2_vms_AutoStop** configura la funzionalità di arresto automatico.

Ogni azione di avvio/arresto supporta l'assegnazione di una o più sottoscrizioni, gruppi di risorse o un elenco di macchine virtuali.

Un account di archiviazione di Azure, richiesto dalle funzioni, viene usato anche da Start/Stop VM V2 (Preview) per due scopi:

   - Usa l'archiviazione tabelle di Azure per archiviare i metadati dell'operazione di esecuzione, ovvero l'azione avvia/arresta macchina virtuale.

   - Usa l'archiviazione code di Azure per supportare i trigger basati sulla coda di funzioni di Azure.

Tutti i dati di telemetria, ovvero i log di traccia dell'esecuzione dell'app per le funzioni, vengono inviati all'istanza di Application Insights connessa. È possibile visualizzare i dati di telemetria archiviati in Application Insights da un set di visualizzazioni predefinite presentate in un dashboard di [Azure](../../azure-portal/azure-portal-dashboards.md)condiviso.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Avviare/arrestare il dashboard di stato condiviso delle VM":::

Le notifiche tramite posta elettronica vengono inviate anche come risultato delle azioni eseguite sulle VM.

## <a name="new-releases"></a>Nuove versioni

Quando viene rilasciata una nuova versione di Start/Stop VM V2 (anteprima), l'istanza viene aggiornata automaticamente senza dover eseguire la ridistribuzione manuale.

## <a name="supported-scoping-options"></a>Opzioni di ambito supportate

### <a name="subscription"></a>Subscription

L'ambito di una sottoscrizione può essere usato quando è necessario eseguire l'azione di avvio e interruzione su tutte le macchine virtuali in un'intera sottoscrizione ed è possibile selezionare più sottoscrizioni, se necessario.  

È anche possibile specificare un elenco di macchine virtuali da escludere, che verranno ignorate dall'azione. È anche possibile usare caratteri jolly per specificare tutti i nomi che possono essere ignorati simultaneamente.

### <a name="resource-group"></a>Resource group

L'ambito di un gruppo di risorse può essere usato quando è necessario eseguire l'azione di avvio e interruzione su tutte le VM specificando uno o più nomi di gruppi di risorse e in una o più sottoscrizioni.

È anche possibile specificare un elenco di macchine virtuali da escludere, che verranno ignorate dall'azione. È anche possibile usare caratteri jolly per specificare tutti i nomi che possono essere ignorati simultaneamente.

### <a name="vmlist"></a>VMList

La specifica di un elenco di macchine virtuali può essere usata quando è necessario eseguire l'azione di avvio e interruzione su un set specifico di macchine virtuali e su più sottoscrizioni. Questa opzione non supporta la specifica di un elenco di macchine virtuali da escludere.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

- All'account sono state concesse le autorizzazioni di [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) nella sottoscrizione.

- Start/Stop VM V2 (anteprima) è disponibile in tutte le aree globali di Azure elencate nella pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) per funzioni di Azure. Per il cloud di Azure per enti pubblici, è disponibile solo nell'area Virginia del governo degli Stati Uniti.

## <a name="next-steps"></a>Passaggi successivi

Per distribuire questa funzionalità, vedere [distribuire le VM di avvio/arresto](deploy.md) (anteprima).