---
title: Monitorare operazioni, eventi e contatori per un servizio di bilanciamento del carico pubblico
titleSuffix: Azure Load Balancer
description: Informazioni su come abilitare la registrazione per Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: fcfd3da30ef9ace723b4204f5924591b1e2717f8
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503166"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Log di monitoraggio di Azure per Azure Load Balancer Standard

È possibile usare diversi tipi di log di monitoraggio di Azure per gestire e risolvere i problemi di Azure Load Balancer Standard. I log possono essere trasmessi a un hub eventi o a un'area di lavoro Log Analytics. È possibile estrarre tutti i log dall'archivio BLOB di Azure e visualizzarli in strumenti come Excel e Power BI. 

I tipi di log sono:

* **Log attività:** È possibile visualizzare tutte le attività inviate alle sottoscrizioni di Azure, insieme al relativo stato. Per altre informazioni, vedere [visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md). I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure. Questi log sono disponibili sia per la Load Balancer di base di Azure che per la Load Balancer Standard.
* **Metriche di Load Balancer standard:** È possibile usare questo log per eseguire una query sulle metriche esportate come log per Load Balancer Standard. Questi log sono disponibili solo per Load Balancer Standard.

> [!IMPORTANT]
> Il probe di integrità e i registri eventi di avviso Load Balancer non sono attualmente funzionali e sono elencati nei [problemi noti per Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Azure Resource Manager. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. Abilitare la registrazione di eventi e probe di integrità per iniziare a raccogliere i dati disponibili tramite tali log. Eseguire la procedura descritta di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](./quickstart-load-balancer-standard-public-portal.md) , se non se ne ha già uno.
1. Nel portale selezionare gruppi di **risorse**.
2. Selezionare **\<resource-group-name>** la posizione in cui si trova il servizio di bilanciamento del carico.
3. Selezionare il servizio di bilanciamento del carico in uso.
4. Selezionare impostazioni di diagnostica del **log attività**  >  .
5. Nel riquadro **Impostazioni** di diagnostica, in **impostazioni di diagnostica**, selezionare **+ Aggiungi impostazione di diagnostica**.
6. Nel riquadro di creazione **impostazioni di diagnostica** immettere **MyLBDiagnostics** nella casella **nome** .
7. Sono disponibili tre opzioni per le **impostazioni di diagnostica**. È possibile scegliere uno, due o tutti e tre e configurare ognuno per i requisiti:

   * **Archivia in un account di archiviazione**. Per questo processo è necessario un account di archiviazione già creato. Per creare un account di archiviazione, vedi [Creare un account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal).
     1. Selezionare la casella **di controllo archivia in un account di archiviazione** .
     2. Selezionare **Configura** per aprire il riquadro **selezionare un account di archiviazione** .
     3. Nell'elenco a discesa **sottoscrizione** selezionare la sottoscrizione in cui è stato creato l'account di archiviazione.
     4. Nell'elenco a discesa **account di archiviazione** selezionare il nome dell'account di archiviazione.
     5. Selezionare **OK**.

   * **Streaming in un hub eventi**. Per questo processo è necessario un hub eventi già creato. Per creare un hub eventi, vedere [Guida introduttiva: creare un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
     1. Selezionare la casella di controllo **Streaming in un hub eventi**.
     2. Selezionare **Configura** per aprire il riquadro **Seleziona Hub eventi** .
     3. Nell'elenco a discesa **Subscription (sottoscrizione** ) selezionare la sottoscrizione in cui è stato creato l'hub eventi.
     4. Nell'elenco a discesa **selezionare lo spazio dei nomi dell'hub eventi** selezionare lo spazio dei nomi.
     5. Nell'elenco a discesa **selezionare il nome dei criteri dell'hub eventi** selezionare il nome.
     6. Selezionare **OK**.

   * **Invia a log Analytics**. È necessario che sia già stata creata e configurata un'area di lavoro di log Analytics per questo processo. Per creare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).
     1. Selezionare la casella di controllo **Invia a Log Analytics**.
     2. Nell'elenco a discesa **sottoscrizione** selezionare la sottoscrizione in cui si trova l'area di lavoro log Analytics.
     3. Nell'elenco a discesa **area di lavoro log Analytics** selezionare l'area di lavoro.

8. Nella sezione **metrica** del riquadro **impostazioni di diagnostica** , selezionare la casella di controllo **AllMetrics** .

9. Verificare che tutto sia corretto e quindi selezionare **Salva** nella parte superiore del riquadro di creazione **impostazioni di diagnostica** .

## <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

Il log attività viene generato per impostazione predefinita. È possibile configurarlo in modo che venga esportato a livello di sottoscrizione [seguendo le istruzioni riportate in questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Per altre informazioni su questi log, leggere l'articolo [visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md) .

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure:** Recuperare le informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o l'portale di Azure. Le [operazioni di controllo con gestione risorse](../azure-resource-manager/management/view-activity-logs.md) articolo forniscono istruzioni dettagliate per ogni metodo.
* **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Usando l' [integrazione dei log di controllo di Azure per Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/), è possibile analizzare i dati con dashboard preconfigurati. In alternativa, è possibile personalizzare le visualizzazioni in base alle esigenze.

## <a name="view-and-analyze-metrics-as-logs"></a>Visualizzare e analizzare le metriche come log
Usando la funzionalità di esportazione in monitoraggio di Azure, è possibile esportare le metriche del Load Balancer. Queste metriche genereranno una voce di log per ogni intervallo di campionamento di un minuto.

L'esportazione da metrica a log è abilitata a livello di singola risorsa. Per abilitare i log seguenti:

1. Passare al riquadro **impostazioni di diagnostica** .
1. Filtrare in base al gruppo di risorse e quindi selezionare l'istanza di Load Balancer per cui si vuole abilitare l'esportazione delle metriche. 
1. Quando la pagina delle impostazioni di diagnostica per Load Balancer è attiva, selezionare **AllMetrics** per esportare le metriche idonee come log.

Per le limitazioni relative all'esportazione delle metriche, vedere la sezione [limitazioni](#limitations) di questo articolo.

Dopo aver abilitato **AllMetrics** nelle impostazioni di diagnostica di Load Balancer standard, se si usa un hub eventi o log Analytics area di lavoro, questi log verranno popolati nella tabella **AzureMonitor** . 

Se si sta eseguendo l'esportazione nella risorsa di archiviazione, connettersi all'account di archiviazione e recuperare le voci di log JSON per i log di eventi e probe di integrità. Dopo aver scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati. 

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare gli [strumenti di conversione di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
Quando le informazioni di diagnostica vengono trasmesse a un hub eventi, è possibile usarle per l'analisi centralizzata dei log in uno strumento SIEM partner con l'integrazione di monitoraggio di Azure. Per altre informazioni, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Invia a Log Analytics
È possibile inviare le informazioni di diagnostica per le risorse in Azure direttamente a un'area di lavoro Log Analytics. In tale area di lavoro è possibile eseguire query complesse sulle informazioni per la risoluzione dei problemi e l'analisi. Per altre informazioni, vedere [raccogliere i log delle risorse di Azure in un'area di lavoro log Analytics in monitoraggio di Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Limitazioni
La funzionalità di esportazione da metrica a log per Azure Load Balancer presenta le limitazioni seguenti:
* Le metriche vengono attualmente visualizzate tramite nomi interni quando vengono esportate come log. È possibile trovare il mapping nella tabella seguente.
* La dimensionalità delle metriche non viene mantenuta. Con le metriche, ad esempio **DipAvailability** (stato del probe di integrità), non sarà possibile suddividerle o visualizzarle in base all'indirizzo IP back-end.
* Le metriche per le porte SNAT usate e le porte SNAT allocate non sono attualmente disponibili per l'esportazione come log.

## <a name="next-steps"></a>Passaggi successivi
* [Esaminare le metriche disponibili per il servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Creare e testare le query seguendo le istruzioni di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
