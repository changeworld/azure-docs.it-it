---
title: Compilazione di soluzioni per Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. Questo articolo fornisce una panoramica della creazione di soluzioni integrate con IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417803"
---
# <a name="iot-central-solution-builder-guide"></a>Guida al generatore di soluzioni IoT Central

*Le informazioni di questo articolo sono destinate ai generatori di soluzioni.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa guida è destinata ai generatori di soluzioni che usano IoT Central per creare soluzioni integrate. Un'applicazione IoT Central consente di gestire i dispositivi, analizzare i dati di telemetria del dispositivo e integrarsi con altri servizi back-end.

Un generatore di soluzioni:

- Configura dashboard e visualizzazioni nell'interfaccia utente Web di IoT Central.
- Usa le regole e gli strumenti di analisi predefiniti per derivare informazioni aziendali dai dispositivi connessi.
- Usa le funzionalità di esportazione e regole dei dati per integrare IoT Central con altri servizi back-end.

## <a name="configure-dashboards-and-views"></a>Configurare dashboard e visualizzazioni

Un'applicazione IoT Central può disporre di uno o più dashboard utilizzati dagli operatori per visualizzare e interagire con l'applicazione. Come generatore di soluzioni, è possibile personalizzare il dashboard predefinito e creare dashboard specializzati:

- Per visualizzare alcuni esempi di dashboard personalizzati, vedere [modelli mirati al settore](concepts-app-templates.md#industry-focused-templates).
- Per altre informazioni sui dashboard, vedere [creare e gestire più dashboard](howto-create-personal-dashboards.md) e [configurare il dashboard dell'applicazione](howto-add-tiles-to-your-dashboard.md).

Quando un dispositivo si connette a una IoT Central, il dispositivo è associato a un modello di dispositivo per il tipo di dispositivo. Un modello di dispositivo ha visualizzazioni personalizzabili che un operatore usa per gestire i singoli dispositivi. Gli sviluppatori di soluzioni possono creare e personalizzare le visualizzazioni disponibili per un tipo di dispositivo. Per altre informazioni, vedere [aggiungere visualizzazioni](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Usare le regole e le analisi predefinite

Uno sviluppatore di soluzioni può aggiungere regole a un'applicazione IoT Central che esegue azioni personalizzabili. Le regole valutano le condizioni, in base ai dati provenienti da un dispositivo, per determinare quando eseguire un'azione. Per ulteriori informazioni sulle regole, vedere:

- [Esercitazione: Creare una regola e configurare le notifiche nell'applicazione Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Creare webhook per le regole in Azure IoT Central](howto-create-webhooks.md)
- [Raggruppare più azioni da eseguire da una o più regole](howto-use-action-groups.md)

IoT Central dispone di funzionalità di analisi predefinite che possono essere utilizzate da un operatore per analizzare il flusso di dati dai dispositivi connessi. Per altre informazioni, vedere [come usare Analytics per analizzare i dati del dispositivo](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

Come generatore di soluzioni, è possibile usare le funzionalità di esportazione e delle regole dei dati in IoT Central per l'integrazione con altri servizi. Per altre informazioni, vedere:

- [Esportare i dati delle cose nelle destinazioni cloud usando l'esportazione dei dati](howto-export-data.md)
- [Usare i flussi di lavoro per integrare l'applicazione Azure IoT Central con altri servizi cloud](howto-configure-rules-advanced.md)
- [Estendere Azure IoT Central con regole personalizzate usando Analisi di flusso, Funzioni di Azure e SendGrid](howto-create-custom-rules.md)
- [Estendere Azure IoT Central con analisi personalizzate mediante Azure Databricks](howto-create-custom-analytics.md)
- [Visualizzare e analizzare i dati di Azure IoT Central in un dashboard di Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di IoT Central, vedere le guide introduttive, a partire da [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).
