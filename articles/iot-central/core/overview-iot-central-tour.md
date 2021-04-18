---
title: Presentazione dell'interfaccia utente di Azure IoT Central | Microsoft Docs
description: Acquisire familiarità con le aree chiave dell'interfaccia Azure IoT Central interfaccia utente che si usa per creare, gestire e usare la soluzione IoT.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 564e7d3d2353661d0a69cbd15ad8b281defacf87
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600569"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Presentazione dell'interfaccia utente di Azure IoT Central

Questo articolo illustra come Azure IoT Central'interfaccia utente. È possibile usare l'interfaccia utente per creare, gestire e usare un'IoT Central e i dispositivi connessi.

## <a name="iot-central-homepage"></a>Home page di IoT Central

La [IoT Central home](https://aka.ms/iotcentral-get-started) page è la posizione in cui sono disponibili altre informazioni sulle novità e sulle funzionalità più recenti disponibili in IoT Central, creare nuove applicazioni e visualizzare e avviare le applicazioni esistenti.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Home page di IoT Central":::

### <a name="create-an-application"></a>Creare un'applicazione

Nella sezione Compilazione è possibile esplorare l'elenco dei modelli di IoT Central rilevanti per il settore o iniziare da zero usando un modello di app personalizzato.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Pagina di compilazione di IoT Central":::

Per altre informazioni, vedere l'argomento di avvio rapido [Creare un'applicazione di Azure IoT Central](quick-deploy-iot-central.md).

### <a name="launch-your-application"></a>Avviare l'applicazione

Avviare l'applicazione IoT Central passando all'URL scelto durante la creazione dell'app. È anche possibile visualizzare un elenco di tutte le applicazioni a cui è possibile accedere nella pagina di [gestione app di IoT Central](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Gestione app IoT Central":::

## <a name="navigate-your-application"></a>Esplorare l'applicazione

Una volta all'interno dell'applicazione IoT, usare il riquadro sinistro per accedere a varie funzionalità. È possibile espandere o comprimere il riquadro sinistro selezionando l'icona con tre linee nella parte superiore del riquadro:

> [!NOTE]
> Le voci visualizzate nel riquadro sinistro dipendono dal ruolo utente. Altre informazioni sulla [gestione di utenti e ruoli](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="riquadro sinistro":::

  :::column-end:::
  :::column span="2":::
     **I dashboard** visualizzano tutti i dashboard personali e dell'applicazione. 
     
     **I** dispositivi consentono di gestire tutti i dispositivi.

     **I gruppi di** dispositivi consentono di visualizzare e creare raccolte di dispositivi specificati da una query. I gruppi di dispositivi vengono usati tramite l'applicazione per eseguire operazioni bulk.

     **Regole** consente di creare e modificare le regole per monitorare i dispositivi. Le regole vengono valutate in base ai dati del dispositivo e attivano azioni personalizzabili.

     **Analytics** espone funzionalità avanzate per analizzare le tendenze cronologiche e correlare varie telemetrie dai dispositivi.

     **Processi** consente di gestire i dispositivi con scalabilità eseguendo operazioni in blocco.

     **I modelli** di dispositivo consentono di creare e gestire le caratteristiche dei dispositivi che si connettono all'applicazione.

     **L'esportazione** dati consente di configurare un'esportazione continua in servizi esterni, ad esempio l'archiviazione e le code.

     **L'amministrazione** consente di gestire le impostazioni, la personalizzazione, la fatturazione, gli utenti e i ruoli dell'applicazione.

     **App personali** consente di tornare al gestore IoT Central app.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Ricerca, guida, tema e supporto

Il menu superiore viene visualizzato in ogni pagina:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central barra degli strumenti":::

* Per cercare i dispositivi, immettere un **valore Di** ricerca.
* Per cambiare la lingua o il tema dell'interfaccia utente, scegliere l'icona **Settings** (Impostazioni). Altre informazioni sulla [gestione delle preferenze dell'applicazione](howto-manage-preferences.md)
* Per accedere alla guida e al supporto tecnico, fare clic sul menu a discesa **Help** (Guida) per visualizzare un elenco di risorse. È possibile [ottenere informazioni sull'applicazione](./howto-get-app-info.md) dal collegamento **Informazioni sull'app**. Nelle applicazioni del piano tariffario gratuito le risorse di supporto includono l'accesso alla [live chat](howto-show-hide-chat.md).
* Per disconnettersi dall'applicazione, scegliere l'icona **Account**.

È possibile scegliere tra un tema scuro o un tema chiaro per l'interfaccia utente:

> [!NOTE]
> L'opzione per scegliere il tema chiaro o scuro non è disponibile se l'amministratore ha configurato un tema personalizzato per l'applicazione.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Screenshot della IoT Central scegliere un tema.":::

### <a name="dashboard"></a>Dashboard

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Screenshot di IoT Central Dashboard.":::

* Il dashboard è la prima pagina visualizzata quando si accede all'applicazione IoT Central applicazione. È possibile creare e personalizzare più dashboard dell'applicazione. Altre informazioni sull'[aggiunta di riquadri al dashboard](howto-add-tiles-to-your-dashboard.md)

* È anche possibile creare dashboard personali per monitorare ciò che interessa. Per altre informazioni, vedere la procedura dettagliata [Creare dashboard personali di Azure IoT Central](howto-create-personal-dashboards.md).

### <a name="devices"></a>Dispositivi

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Screenshot della pagina Dispositivi.":::

Questa pagina mostra i dispositivi nell'applicazione IoT Central raggruppati per modello _di dispositivo_. 

* Un modello di dispositivo consente di definire un tipo di dispositivo in grado di connettersi all'applicazione.
* Per dispositivo si intende un dispositivo simulato o reale presente nell'applicazione.

Per altre informazioni, vedere la Guida introduttiva [Monitorare i dispositivi](./quick-monitor-devices.md). 

### <a name="device-groups"></a>Gruppi di dispositivi

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Pagina Gruppo di dispositivi":::

Questa pagina consente di creare e visualizzare i gruppi di dispositivi nell'IoT Central applicazione. È possibile usare i gruppi di dispositivi per eseguire operazioni in blocco nell'applicazione o per analizzare i dati. Per altre informazioni, vedere l'articolo [Usare gruppi di dispositivi nell'applicazione Azure IoT Central](tutorial-use-device-groups.md).

### <a name="rules"></a>Regole
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Screenshot della pagina Regole.":::

Questa pagina consente di visualizzare e creare regole basate sui dati del dispositivo. Quando una regola viene attivata, può attivare una o più azioni, ad esempio inviare un messaggio di posta elettronica o richiamare un webhook. Per altre informazioni, vedere l'esercitazione [Configurazione di regole](tutorial-create-telemetry-rules.md). 

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Screenshot della pagina Analytics.":::

Analytics espone funzionalità avanzate per analizzare le tendenze cronologiche e correlare varie telemetrie dai dispositivi. Per altre informazioni, vedere l'articolo [Creare analisi per l'applicazione Azure IoT Central](howto-create-analytics.md).

### <a name="jobs"></a>Processi

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Pagina Processi":::

Questa pagina consente di visualizzare e creare processi che possono essere usati per operazioni di gestione dei dispositivi in blocco nei dispositivi. È possibile aggiornare le proprietà del dispositivo, le impostazioni e i comandi di esecuzione nei gruppi di dispositivi. Per altre informazioni, vedere l'articolo [Eseguire un processo](howto-run-a-job.md).

### <a name="device-templates"></a>Modelli di dispositivo

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Screenshot dei modelli di dispositivo.":::

Nella pagina modelli di dispositivo è possibile visualizzare e creare modelli di dispositivo nell'applicazione. Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Esportazione dati

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Esportazione dati":::

L'esportazione dei dati consente di configurare flussi di dati in sistemi esterni. Per altre informazioni, vedere l'articolo [Esportare i dati in Azure IoT Central](./howto-export-data.md).

### <a name="administration"></a>Amministrazione

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Screenshot di Amministrazione IoT.":::

La pagina Amministrazione consente di configurare e personalizzare l'applicazione IoT Central. In tale pagina è possibile modificare nome dell'applicazione, URL e temi, gestire utenti e ruoli, creare token API ed esportare l'applicazione. Per altre informazioni, vedere l'articolo sull'[amministrazione dell'applicazione Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Azure IoT Central e aver acquisito familiarità con il layout dell'interfaccia utente, i passaggi successivi consigliati consistono nel completare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).
