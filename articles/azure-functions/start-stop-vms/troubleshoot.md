---
title: Risolvere i problemi di avvio/arresto di macchine virtuali (anteprima)
description: Questo articolo illustra come risolvere i problemi riscontrati con la funzionalità di avvio/arresto di macchine virtuali (anteprima) per le macchine virtuali di Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111797"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Risolvere i problemi comuni relativi all'avvio/arresto di macchine virtuali (anteprima)

Questo articolo fornisce informazioni sulla risoluzione dei problemi e sulla risoluzione dei problemi che possono verificarsi durante il tentativo di installare e configurare le macchine virtuali di avvio/arresto (anteprima). Per informazioni generali, vedere [Start/Stop VM Overview](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Convalida generale e risoluzione dei problemi

In questa sezione viene illustrato come risolvere i problemi generali relativi agli scenari di pianificazione e come identificare la causa principale.

### <a name="azure-dashboard"></a>Dashboard di Azure

È possibile iniziare esaminando il dashboard condiviso di Azure. Il dashboard condiviso di Azure distribuito come parte di Start/Stop VM V2 (anteprima) è un modo rapido e semplice per verificare lo stato di ogni operazione eseguita sulle macchine virtuali. Per visualizzare tutte le operazioni recenti eseguite nelle macchine virtuali, fare riferimento al riquadro **azioni recentemente tentate nel riquadro VM** . Si verifica una latenza, circa cinque minuti, per visualizzare i dati nel report durante il pull dei dati dalla risorsa Application Insights.

### <a name="logic-apps"></a>App per la logica

A seconda delle app per la logica abilitate per supportare lo scenario di avvio/arresto, è possibile esaminare la cronologia di esecuzione per identificare il motivo per cui lo scenario di avvio/arresto pianificato non è stato completato correttamente per una o più macchine virtuali di destinazione. Per informazioni su come esaminarlo in dettaglio, vedere [cronologia di esecuzione delle app](../../logic-apps/monitor-logic-apps.md#review-runs-history)per la logica.

### <a name="azure-storage"></a>Archiviazione di Azure

È possibile esaminare i dettagli per le operazioni eseguite nelle macchine virtuali scritte nella tabella **requestsstoretable** nell'account di archiviazione di Azure usato per avviare/arrestare le VM V2 (anteprima). Per visualizzare tali record, seguire questa procedura.

1. Passare all'account di archiviazione nel portale di Azure e nell'account selezionare * * Storage Explorer (anteprima) nel riquadro a sinistra.
1. Selezionare **tabelle** e quindi selezionare **requeststoretable**.
1. Ogni record della tabella rappresenta l'azione di avvio/arresto eseguita su una macchina virtuale di Azure basata sull'ambito di destinazione definito nello scenario dell'app per la logica. È possibile filtrare i risultati in base a una qualsiasi delle proprietà del record (ad esempio, TIMESTAMP, ACTION o TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Funzioni di Azure

È possibile esaminare i dettagli più recenti della chiamata per le funzioni di Azure responsabili dell'avvio e dell'arresto dell'esecuzione della macchina virtuale. Esaminare prima di tutto il flusso di esecuzione.

Il flusso di esecuzione per lo scenario **pianificato** e **sequenziato** è controllato dalla stessa funzione. Lo schema del payload è quello che determina quale scenario viene eseguito. Per lo scenario **pianificato** , il flusso di esecuzione è- **scheduled** HTTP > **VirtualMachineRequestOrchestrator** Queue > **VirtualMachineRequestExecutor** Queue.

Dall'app per la logica, la funzione http **pianificata** viene richiamata con lo schema del payload. Una volta ricevuta la richiesta, la funzione http **pianificata** Invia le informazioni alla funzione della coda dell'agente di **orchestrazione** , che a sua volta crea diverse code per ogni macchina virtuale per eseguire l'azione.

Per visualizzare i dettagli della chiamata, seguire questa procedura.

1. Nella portale di Azure passare a funzioni di **Azure**.
1. Selezionare l'app per le funzioni per avviare/arrestare le VM V2 (anteprima) dall'elenco.
1. Selezionare **funzioni** dal riquadro a sinistra.
1. Nell'elenco vengono visualizzate diverse funzioni associate a ogni scenario. Consente di selezionare la funzione http **pianificata** .
1. Selezionare **monitoraggio** dal riquadro a sinistra.
1. Selezionare la traccia di esecuzione più recente per visualizzare i dettagli della chiamata e la sezione del messaggio per la registrazione dettagliata.
1. Ripetere gli stessi passaggi per ogni funzione descritta nell'ambito della revisione del flusso di esecuzione precedente.

Per altre informazioni sul monitoraggio di funzioni di Azure, vedere analizzare i dati di [telemetria di funzioni di Azure in Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul monitoraggio di funzioni di Azure e app per la logica:

* [Monitorare funzioni di Azure](../../azure-functions/functions-monitoring.md).

* [Come configurare il monitoraggio per funzioni di Azure](../../azure-functions/configure-monitoring.md).

* [Monitorare le app](../../logic-apps/monitor-logic-apps.md)per la logica.