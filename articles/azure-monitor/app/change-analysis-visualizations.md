---
title: Visualizzazioni per l'analisi delle modifiche dell'applicazione-monitoraggio di Azure
description: Informazioni su come usare le visualizzazioni nell'analisi delle modifiche dell'applicazione in monitoraggio di Azure.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655852"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualizzazioni per l'analisi delle modifiche dell'applicazione (anteprima)

## <a name="standalone-ui"></a>Interfaccia utente autonoma

In monitoraggio di Azure è disponibile un riquadro autonomo per l'analisi delle modifiche che consente di visualizzare tutte le modifiche con informazioni dettagliate sulle dipendenze dell'applicazione e sulle risorse.

Cercare Change Analysis nella barra di ricerca portale di Azure per avviare l'esperienza.

![Screenshot della ricerca dell'analisi delle modifiche in portale di Azure](./media/change-analysis/search-change-analysis.png)

Tutte le risorse in una sottoscrizione selezionata vengono visualizzate con le modifiche apportate nelle ultime 24 ore. Tutte le modifiche vengono visualizzate con valore precedente e nuovo valore per fornire informazioni dettagliate a colpo d'occhio.

![Screenshot del pannello di analisi delle modifiche in portale di Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Fare clic su una modifica per visualizzare il frammento di Gestione risorse completo e altre proprietà.

![Schermata dei dettagli delle modifiche](./media/change-analysis/change-details.png)

Per commenti e suggerimenti, usare il pulsante Invia commenti e suggerimenti o l'indirizzo di posta elettronica changeanalysisteam@microsoft.com .

![Screenshot del pulsante Commenti nella scheda analisi modifiche](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Supporto per più sottoscrizioni

L'interfaccia utente supporta la selezione di più sottoscrizioni per visualizzare le modifiche alle risorse. Usare il filtro della sottoscrizione:

![Screenshot del filtro di sottoscrizione che supporta la selezione di più sottoscrizioni](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analisi delle modifiche dell'applicazione nello strumento diagnostica e risoluzione dei problemi

L'analisi delle modifiche dell'applicazione è un detector autonomo negli strumenti per la diagnosi e la risoluzione dei problemi dell'app Web. Viene anche aggregato in **arresti anomali dell'applicazione** e **rilevatori di app Web**. Quando si immette lo strumento diagnostica e Risolvi i problemi, il provider di risorse **Microsoft. ChangeAnalysis** verrà registrato automaticamente. Seguire queste istruzioni per abilitare il rilevamento delle modifiche in-Guest per l'app Web.

1. Selezionare **disponibilità e prestazioni**.

    ![Screenshot delle opzioni di risoluzione dei problemi di disponibilità e prestazioni](./media/change-analysis/availability-and-performance.png)

2. Selezionare **le modifiche apportate all'applicazione**. La funzionalità è disponibile anche negli **arresti anomali dell'applicazione**.

   ![Screenshot del pulsante "arresti anomali dell'applicazione"](./media/change-analysis/application-changes.png)

3. Il collegamento porta all'interfaccia utente dell'analisi delle modifiche dell'applicazione con ambito nell'app Web. Se il rilevamento delle modifiche nell'app Web in-Guest non è abilitato, seguire il banner per ottenere le modifiche alle impostazioni di file e app.

   ![Screenshot delle opzioni "arresti anomali dell'applicazione"](./media/change-analysis/enable-changeanalysis.png)

4. Attivare l' **analisi delle modifiche** e selezionare **Salva**. Lo strumento Visualizza tutte le app Web in un piano di servizio app. È possibile usare l'opzione livello piano per attivare l'analisi delle modifiche per tutte le app Web in un piano.

    ![Screenshot dell'interfaccia utente "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)

5. I dati delle modifiche sono disponibili anche in selezione **app Web** e rilevamenti **arresti anomali dell'applicazione** . Verrà visualizzato un grafico che riepiloga il tipo di modifiche nel tempo insieme ai dettagli relativi a tali modifiche. Per impostazione predefinita, le modifiche apportate nelle ultime 24 ore vengono visualizzate per facilitare i problemi immediatamente.

     ![Screenshot della visualizzazione delle differenze delle modifiche](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Strumento di diagnostica e risoluzione dei problemi
L'analisi delle modifiche è disponibile come scheda Insight nello strumento diagnostica e risolvi problemi. Se si verificano problemi in una risorsa e sono state rilevate modifiche nelle ultime 72 ore, nella scheda Insights verrà visualizzato il numero di modifiche. Se si fa clic sul collegamento Visualizza dettagli modifica, viene visualizzata la visualizzazione filtrata dall'interfaccia utente autonoma Change Analysis.

![Screenshot della visualizzazione delle informazioni sulle modifiche nello strumento diagnostica e risoluzione dei problemi.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostica e risoluzione dei problemi della macchina virtuale

Passare allo strumento diagnostica e risoluzione dei problemi per una macchina virtuale.  Passare a **strumenti per la risoluzione dei problemi**, esplorare la pagina e selezionare **analizza modifiche recenti** per visualizzare le modifiche apportate alla macchina virtuale.

![Screenshot della macchina virtuale diagnosticare e risolvere i problemi](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analizzatore modifiche in strumenti di risoluzione dei problemi](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Cronologia modifiche log attività

La funzionalità [Visualizza cronologia modifiche](../essentials/activity-log.md#view-change-history) nel log attività chiama il back-end del servizio di analisi delle modifiche dell'applicazione per ottenere le modifiche associate a un'operazione. **Cronologia delle modifiche** usata per chiamare direttamente il [grafo delle risorse di Azure](../../governance/resource-graph/overview.md) , ma lo scambio del back-end per chiamare l'analisi delle modifiche dell'applicazione in modo che le modifiche restituite includano le modifiche a livello di risorsa da [Azure Resource Graph](../../governance/resource-graph/overview.md), le proprietà delle risorse da [Azure Resource Manager](../../azure-resource-manager/management/overview.md)e le modifiche in-Guest dai servizi di PaaS, ad esempio app Web Affinché il servizio di analisi delle modifiche dell'applicazione sia in grado di analizzare le modifiche apportate alle sottoscrizioni degli utenti, è necessario registrare un provider di risorse. La prima volta che si immette la scheda **cronologia modifiche** , lo strumento inizierà automaticamente a registrare il provider di risorse **Microsoft. ChangeAnalysis** . Dopo la registrazione, le modifiche da **Azure Resource Graph** saranno disponibili immediatamente e copriranno gli ultimi 14 giorni. Le modifiche apportate da altre origini saranno disponibili dopo circa 4 ore dopo l'onboarding della sottoscrizione.

![Integrazione cronologia modifiche log attività](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integrazione di VM Insights

Gli utenti che hanno abilitato [VM Insights](../vm/vminsights-overview.md) possono visualizzare le modifiche apportate alle macchine virtuali che potrebbero causare picchi in un grafico delle metriche, ad esempio CPU o memoria. I dati delle modifiche sono integrati nella barra di spostamento sul lato di VM Insights. L'utente può visualizzare se sono state apportate modifiche nella macchina virtuale e selezionare Verifica modifiche per visualizzare i dettagli delle modifiche nell'interfaccia utente autonoma di analisi **delle** modifiche dell'applicazione.

[![Integrazione di VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi nell'analisi delle modifiche](change-analysis-troubleshoot.md)
