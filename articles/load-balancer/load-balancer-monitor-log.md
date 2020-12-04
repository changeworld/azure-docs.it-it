---
title: Monitorare operazioni, eventi e contatori per Load Balancer Basic pubblico
titleSuffix: Azure Load Balancer
description: Informazioni su come abilitare la registrazione per il Azure Load Balancer
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572780"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Log di monitoraggio di Azure per il Azure Load Balancer standard

È possibile usare diversi tipi di log in Azure per gestire e risolvere i problemi di bilanciamento del carico standard. I log possono essere trasmessi a un hub eventi o a un'area di lavoro Log Analytics. Tutti i log possono essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI.  L'elenco seguente contiene altre informazioni sui diversi tipi di log.

* **Log attività:** È possibile usare [Visualizza log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md) per visualizzare tutte le attività inviate alle sottoscrizioni di Azure e il relativo stato. I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure. Questi log sono disponibili per i bilanciamenti del carico Basic e standard.
* **Metriche di Load Balancer standard:** È possibile usare questo log per eseguire query sulle metriche esportate come log per la Azure Load Balancer standard. Questi log sono disponibili solo per i bilanciamenti del carico standard.

> [!IMPORTANT]
> **Il probe di integrità e i registri eventi di avviso Load Balancer non sono attualmente funzionali e sono elencati nei [problemi noti per il Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni su questi modelli di distribuzione, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Abilitazione della registrazione

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. Abilitare la registrazione di eventi e probe di integrità per iniziare a raccogliere i dati disponibili tramite tali log. Eseguire questa procedura per abilitare la registrazione.

Accedere al [portale di Azure](https://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](./quickstart-load-balancer-standard-public-portal.md) , se non se ne ha già uno.

1. Nel portale fare clic su **gruppi di risorse**.
2. Selezionare **\<resource-group-name>** la posizione in cui si trova il servizio di bilanciamento del carico.
3. Selezionare il servizio di bilanciamento del carico in uso.
4. Selezionare impostazioni di diagnostica del **log attività**  >  **Diagnostic settings**.
5. Nel riquadro **Impostazioni** di diagnostica, in **impostazioni di diagnostica**, selezionare **+ Aggiungi impostazione di diagnostica**.
6. Nel riquadro di creazione **impostazioni di diagnostica** immettere **MyLBDiagnostics** nel campo **nome** .
7. Sono disponibili tre opzioni per le **impostazioni di diagnostica**.  È possibile scegliere uno, due o tutti e tre e configurare ognuno per i requisiti:
   * **Archivia in un account di archiviazione**
   * **Streaming in un hub eventi**
   * **Invia a Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione
    Per questo processo è necessario un account di archiviazione già creato.  Per creare un account di archiviazione, vedere [creare un account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Selezionare la casella di controllo accanto a **archivia in un account di archiviazione**.
    2. Selezionare **Configura** per aprire il riquadro **selezionare un account di archiviazione** .
    3. Selezionare la **sottoscrizione** in cui è stato creato l'account di archiviazione nella casella a discesa.
    4. Selezionare il nome dell'account di archiviazione in **account di archiviazione** nella casella di riepilogo a discesa.
    5. Selezionare OK.

    ### <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
    Per questo processo è necessario un hub eventi già creato.  Per creare un hub eventi, vedere [Guida introduttiva: creare un hub eventi usando portale di Azure](../event-hubs/event-hubs-create.md)

    1. Selezionare la casella di controllo accanto a **Stream a un hub eventi**
    2. Selezionare **Configura** per aprire il riquadro **Seleziona Hub eventi** .
    3. Selezionare la **sottoscrizione** in cui è stato creato l'hub eventi nella casella a discesa.
    4. Nella casella a discesa **selezionare spazio dei nomi dell'hub eventi** .
    5. Nella casella a discesa **selezionare Nome criterio Hub eventi** .
    6. Selezionare OK.

    ### <a name="send-to-log-analytics"></a>Invia a Log Analytics
    È necessario che sia già stata creata e configurata un'area di lavoro di log Analytics per questo processo.  Per creare un'area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md)

    1. Selezionare la casella di controllo accanto a **Invia a log Analytics**.
    2. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro log Analytics nella casella a discesa.
    3. Selezionare l' **area di lavoro log Analytics** nella casella a discesa.


8.  Sotto la sezione **metrica** del riquadro **impostazioni di diagnostica** , selezionare la casella di controllo accanto a: **AllMetrics**

9. Verificare che tutti gli elementi siano corretti e fare clic su **Salva** nella parte superiore del riquadro crea **impostazioni di diagnostica** .

## <a name="activity-log"></a>Log attività

Il log attività viene generato per impostazione predefinita. Può essere configurato per essere esportato a livello di sottoscrizione [seguendo le istruzioni in questo articolo](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Per altre informazioni su questi log, leggere l'articolo [visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati di Log attività con uno dei metodi seguenti:

* **Strumenti di Azure:** Recuperare le informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure (CLI), l'API REST di Azure o l'portale di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Usando l' [integrazione dei log di controllo di Azure per Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/), è possibile analizzare i dati con dashboard preconfigurati oppure personalizzare le visualizzazioni in base alle esigenze.

## <a name="metrics-as-logs"></a>Metriche come log
Usando le metriche per registrare la funzionalità di esportazione fornita da monitoraggio di Azure, è possibile esportare le metriche del Load Balancer. Queste metriche genereranno una voce di log per ogni intervallo di campionamento di un minuto.

La metrica per l'esportazione di log è abilitata per ogni livello di risorsa. È possibile abilitare questi log selezionando il pannello impostazioni di diagnostica, filtrando in base al gruppo di risorse e selezionando la Load Balancer per cui si vuole abilitare l'esportazione delle metriche. Quando la pagina impostazioni di diagnostica Load Balancer è attiva, selezionare AllMetrics per esportare le metriche idonee come log.

Vedere la sezione [limitazioni](#limitations) di questo articolo per le limitazioni dell'esportazione delle metriche.

### <a name="view-and-analyze-metrics-as-logs"></a>Visualizzare e analizzare le metriche come log
Dopo aver abilitato AllMetrics nelle impostazioni di diagnostica dell'Load Balancer Standard, se si usa un hub eventi o Log Analytics area di lavoro, questi log verranno popolati nella tabella AzureMonitor. Se si esporta nella risorsa di archiviazione, connettersi all'account di archiviazione e recuperare le voci di log JSON per i log di eventi e di probe di integrità. Una volta scaricati i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, Power BI o qualsiasi altro strumento di visualizzazione dei dati. 

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi
Quando le informazioni di diagnostica vengono trasmesse a un hub eventi, possono essere usate per l'analisi centralizzata dei log in uno strumento SIEM di terze parti con l'integrazione di monitoraggio di Azure. Per altre informazioni, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Invia a Log Analytics
Le risorse in Azure possono avere le informazioni di diagnostica inviate direttamente a un'area di lavoro Log Analytics in cui è possibile eseguire query complesse sulle informazioni per la risoluzione dei problemi e l'analisi.  Per altre informazioni, vedere [raccogliere i log delle risorse di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Limitazioni
Esistono attualmente le seguenti limitazioni quando si usa la funzionalità Metrica per l'esportazione dei log per i bilanciamenti del carico:
* Le metriche vengono attualmente visualizzate usando i nomi interni quando vengono esportate come log. è possibile trovare il mapping nella tabella seguente.
* La dimensionalità delle metriche non viene mantenuta. Ad esempio, con le metriche come DipAvailability (stato del probe di integrità), non sarà possibile suddividere o visualizzare in base all'indirizzo IP back-end
* Le metriche SNAT e le porte SNAT allocate usate non sono attualmente disponibili per l'esportazione come log

## <a name="next-steps"></a>Passaggi successivi
* [Esaminare le metriche disponibili per l'Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Creare e testare le query seguendo le istruzioni di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Inviare commenti e suggerimenti su questo articolo o Load Balancer funzionalità usando i collegamenti seguenti
