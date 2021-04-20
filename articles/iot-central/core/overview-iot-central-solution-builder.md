---
title: Compilazione di soluzioni per Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. Questo articolo offre una panoramica della creazione di soluzioni integrate con IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728558"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central del generatore di soluzioni

*Le informazioni di questo articolo sono destinate ai generatori di soluzioni.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa guida è per i generatori di soluzioni che usano IoT Central per creare soluzioni integrate. Un IoT Central'applicazione consente di gestire i dispositivi, analizzare i dati di telemetria dei dispositivi e integrarli con altri servizi back-end.

Generatore di soluzioni:

- Configura dashboard e visualizzazioni nell'interfaccia IoT Central'interfaccia utente Web.
- Usa le regole e gli strumenti di analisi predefiniti per ricavare informazioni aziendali dettagliate dai dispositivi connessi.
- Usa le funzionalità di esportazione dei dati e regole per integrare IoT Central con altri servizi back-end.

## <a name="configure-dashboards-and-views"></a>Configurare dashboard e visualizzazioni

Un'IoT Central può avere uno o più dashboard che gli operatori usano per visualizzare e interagire con l'applicazione. I generatori di soluzioni possono personalizzare il dashboard predefinito e creare dashboard specializzati:

- Per visualizzare alcuni esempi di dashboard personalizzati, vedere [Modelli incentrati sul settore.](concepts-app-templates.md#industry-focused-templates)
- Per altre informazioni sui dashboard, vedere Creare e [gestire più dashboard e](howto-create-personal-dashboards.md) Configurare il dashboard [dell'applicazione.](howto-add-tiles-to-your-dashboard.md)

Quando un dispositivo si connette a un IoT Central, il dispositivo viene associato a un modello di dispositivo per il tipo di dispositivo. Un modello di dispositivo ha visualizzazioni personalizzabili che un operatore usa per gestire i singoli dispositivi. Gli sviluppatori di soluzioni possono creare e personalizzare le visualizzazioni disponibili per un tipo di dispositivo. Per altre informazioni, vedere [Aggiungere visualizzazioni.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Usare regole e analisi predefinite

Uno sviluppatore di soluzioni può aggiungere regole a un'IoT Central che esegue azioni personalizzabili. Le regole valutano le condizioni, in base ai dati provenienti da un dispositivo, per determinare quando eseguire un'azione. Per altre informazioni sulle regole, vedere:

- [Esercitazione: Creare una regola e configurare le notifiche nell'applicazione Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Creare webhook per le regole in Azure IoT Central](howto-create-webhooks.md)
- [Raggruppare più azioni da eseguire da una o più regole](howto-use-action-groups.md)

IoT Central funzionalità di analisi incorporate che un operatore può usare per analizzare il flusso di dati dai dispositivi connessi. Per altre informazioni, vedere [Come usare l'analisi per analizzare i dati del dispositivo.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

In qualità di generatore di soluzioni, è possibile usare le funzionalità di esportazione dei dati e regole in IoT Central per l'integrazione con altri servizi. Per altre informazioni, vedere:

- [Esportare dati IoT in destinazioni cloud usando l'esportazione dei dati](howto-export-data.md)
- [Trasformare i dati per IoT Central](howto-transform-data.md)
- [Usare i flussi di lavoro per integrare l'applicazione Azure IoT Central con altri servizi cloud](howto-configure-rules-advanced.md)
- [Estendere Azure IoT Central con regole personalizzate usando Analisi di flusso, Funzioni di Azure e SendGrid](howto-create-custom-rules.md)
- [Estendere Azure IoT Central con analisi personalizzate mediante Azure Databricks](howto-create-custom-analytics.md)
- [Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di IoT Central, vedere le guide introduttive, a partire da [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).
