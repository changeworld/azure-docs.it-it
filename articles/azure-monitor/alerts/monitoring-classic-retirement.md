---
title: Aggiornamento di avvisi classici & monitoraggio in monitoraggio di Azure
description: Descrizione del ritiro dei servizi e delle funzionalità di monitoraggio classici, illustrati in precedenza in portale di Azure in avvisi (versione classica).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041094"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio

Monitoraggio di Azure è uno stack di monitoraggio unificato che supporta ' una metrica ' è uno avvisi ' tra le risorse di Azure. Per altre informazioni, vedere questo [post di Blog](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Le nuove piattaforme di monitoraggio e avviso di Azure sono state progettate per essere più veloci, più intelligenti ed estendibili, in modo da mantenere la crescita dei cloud computing e della filosofia cloud intelligente di Microsoft.

Con la nuova piattaforma di monitoraggio e avviso di Azure, gli avvisi classici in monitoraggio di Azure vengono ritirati per gli utenti del cloud pubblico, anche se sono ancora in uso limitato fino al **31 maggio 2021**. Gli avvisi classici per il cloud di Azure per enti pubblici e Azure Cina 21Vianet si ritireranno il **29 febbraio 2024**.

 ![Avviso classico nel portale di Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Gli utenti sono invitati a iniziare a usare la nuova piattaforma e ricreare al suo interno gli avvisi.

> [!IMPORTANT]
> Le regole di avviso classiche create nel log attività non verranno deprecate o spostate. È possibile accedere a tutte le regole di avviso classiche create nel log attività e usarle nel loro stato corrente dalla nuova pagina Monitoraggio di Azure - Avvisi. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi del log attività con Monitoraggio di Azure](./alerts-activity-log.md). Analogamente, è possibile accedere agli avvisi sull'integrità dei servizi e usarli nel loro stato corrente dalla nuova sezione Integrità dei servizi. Per informazioni dettagliate, vedere gli [avvisi per le notifiche sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Metriche e avvisi unificati per le risorse di Azure

Nel 2018 marzo abbiamo rilasciato la nuova generazione di avvisi per le risorse di Azure. La nuova piattaforma metrica e gli avvisi sono più veloci e offrono maggiore granularità usando le dimensioni. Le dimensioni consentono di sezionare e filtrare in base a combinazione di valori, condizione o operazione specifica. Gli avvisi delle metriche più recenti usano i gruppi di azioni che consentono più notifiche e azioni di automazione. Vedere altre informazioni sulla [gestione degli avvisi delle metriche con monitoraggio di Azure](./alerts-metric.md).

Le nuove metriche per le risorse di Azure sono disponibili come:

- **Metriche della piattaforma di Monitoraggio di Azure Standard**, che forniscono popolari metriche precompilate di diversi servizi e prodotti di Azure. Per altre informazioni, vedere l'articolo sulle [metriche supportate in Monitoraggio di Azure](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e quello sugli [avvisi delle metriche supportati in Monitoraggio di Azure](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Metriche personalizzate di Monitoraggio di Azure**, che forniscono metriche da origini definite dall'utente, tra cui l'agente di Diagnostica di Azure. Per altre informazioni, vedere l'articolo sulle [metriche personalizzate in Monitoraggio di Azure](../essentials/metrics-custom-overview.md). Usando le metriche personalizzate, è anche possibile pubblicare le metriche raccolte dall'[agente di Diagnostica di Microsoft Azure](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) e dall'[agente InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Ritiro della piattaforma classica di monitoraggio e avviso

Come affermato in precedenza, il monitoraggio e gli avvisi classici precedenti vengono ritirati per gli utenti del cloud pubblico. Include la chiusura di API correlate, portale di Azure interfaccia e servizi, anche se ancora in uso limitato fino al **31 maggio 2021**. Gli avvisi classici per il cloud di Azure per enti pubblici e Azure Cina 21Vianet si ritireranno il **29 febbraio 2024**.

In particolare, l'ambito di ritiro è per le metriche classiche attualmente disponibili nella [sezione avvisi (versione classica)](./alerts-classic.overview.md) di portale di Azure e accessibili come risorse [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules) .

Ciò significa:

- Il servizio di monitoraggio e avviso classico verrà ritirato e non sarà più disponibile per la creazione di nuove regole di avviso.
- Eventuali regole di avviso che continuano a esistere negli avvisi (versione classica) continueranno a essere eseguite e ad attivare le notifiche.
- Verrà eseguita la migrazione della maggior parte delle regole di avviso classiche. Il processo avverrà in modo fluido, senza tempi di inattività e senza alcuna perdita nella copertura del monitoraggio per i clienti.
- Le notifiche attivate includeranno la nuova struttura di payload. La destinazione deve essere adattata per funzionare con la nuova struttura.
- Alcune [regole di avviso classiche che non possono essere migrate automaticamente](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) e richiedono azioni manuali da parte degli utenti per continuare l'esecuzione.

> [!IMPORTANT]
> Monitoraggio di Azure ha implementato uno [strumento per la migrazione volontaria](alerts-using-migration-tool.md) delle regole di avviso classiche alla nuova piattaforma. Le regole rimanenti verranno migrate automaticamente dopo il ritiro del servizio. I clienti devono assicurarsi che il payload della regola di avviso per l'utilizzo di automazione classico sia adattato per gestire il nuovo payload da [metriche e avvisi unificati per altre risorse di Azure](#unified-metrics-and-alerts-for-azure-resources), dopo la migrazione delle regole di avviso classiche. Per ulteriori informazioni, vedere [preparazione per la migrazione delle regole di avviso classiche](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Prezzi per le regole di avviso migrate

Viene implementato uno strumento di migrazione che consente di eseguire la migrazione degli [avvisi classici](./alerts-classic.overview.md) di monitoraggio di Azure alla nuova esperienza degli avvisi. Le regole di avviso migrate e i corrispondenti gruppi di azioni migrati (email, webhook o LogicApp) rimarranno gratuiti. La funzionalità con gli avvisi classici, inclusa la possibilità di modificare la soglia, il tipo di aggregazione e la granularità di aggregazione continuerà a essere disponibile gratuitamente con la regola di avviso migrata. Tuttavia, se si modifica la regola di avviso migrata in modo da usare una delle nuove funzionalità della piattaforma avvisi, le notifiche o i tipi di azione, viene applicato un addebito corrispondente. Per altre informazioni sui prezzi per le regole di avviso e le notifiche, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Di seguito sono riportati alcuni esempi di casi in cui verrà addebitato un addebito per la regola di avviso:

- Tutte le regole di avviso nuove (non sottoposte a migrazione) create oltre le unità gratuite, nella nuova piattaforma di Monitoraggio di Azure
- Tutti i dati inseriti e conservati oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutti i test Web multitest eseguiti da Application Insights
- Tutte le metriche personalizzate archiviate oltre le unità gratuite incluse in Monitoraggio di Azure
- Eventuali regole di avviso migrate modificate per l'uso di funzionalità di avviso di metrica più recenti, ad esempio frequenza, più risorse/dimensioni, [soglie dinamiche](../alerts/alerts-dynamic-thresholds.md), modifica di risorse/segnali e così via.
- Tutti i gruppi di azioni migrati che vengono modificati per usare notifiche più recenti o tipi di azione quali SMS, chiamata vocale e integrazione con ITSM.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul nuovo [servizio unificato di Monitoraggio di Azure](../overview.md).
* Leggere le informazioni sui nuovi [avvisi di Azure](./alerts-overview.md).