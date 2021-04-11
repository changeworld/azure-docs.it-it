---
title: Monitorare l'integrità di un'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di operatore o amministratore, monitorare l'integrità complessiva dei dispositivi connessi all'applicazione IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5e462397196d2fd0fd716801d9106929a8cb6a6b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061452"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Monitorare l'integrità complessiva di un'applicazione IoT Central

> [!NOTE]
> Le metriche sono disponibili solo per le applicazioni IoT Central versione 3. Per informazioni su come controllare la versione dell'applicazione, vedere [informazioni sull'applicazione](./howto-get-app-info.md).

*Questo articolo si applica a operatori e amministratori.*

Questo articolo illustra come usare il set di metriche fornito da IoT Central per valutare l'integrità dei dispositivi connessi all'applicazione IoT Central e l'integrità delle esportazioni di dati in esecuzione.

Le metriche sono abilitate per impostazione predefinita per l'applicazione IoT Central e sono accessibili dall' [portale di Azure](https://portal.azure.com/). La [piattaforma dati di monitoraggio di Azure espone queste metriche](../../azure-monitor/essentials/data-platform-metrics.md) e offre diversi modi per interagire con loro. Ad esempio, è possibile usare i grafici nell'portale di Azure, un'API REST o query in PowerShell o nell'interfaccia della riga di comando di Azure.

### <a name="trial-applications"></a>Applicazioni di valutazione

Le applicazioni che usano il piano di valutazione gratuita non hanno una sottoscrizione di Azure associata e quindi non supportano le metriche di monitoraggio di Azure. È possibile [convertire un'applicazione in un piano tariffario standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e ottenere l'accesso a queste metriche.

## <a name="view-metrics-in-the-azure-portal"></a>Visualizzare le metriche nell'portale di Azure

I passaggi seguenti presuppongono che sia presente un' [applicazione IoT Central](./quick-deploy-iot-central.md) con alcuni [dispositivi connessi](./tutorial-connect-device.md) o un' [esportazione dei dati](howto-export-data.md)in esecuzione.

Per visualizzare IoT Central metriche nel portale:

1. Passare alla risorsa dell'applicazione IoT Central nel portale. Per impostazione predefinita, le risorse di IoT Central si trovano in un gruppo di risorse denominato **IOTC**.
1. Per creare un grafico dalle metriche dell'applicazione, selezionare **metriche** nella sezione **monitoraggio** .

![Metriche di Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Autorizzazioni portale di Azure

L'accesso alle metriche nel portale di Azure è gestito dal [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Usare il portale di Azure per aggiungere utenti al IoT Central applicazione/gruppo di risorse/sottoscrizione per concedere loro l'accesso. È necessario aggiungere un utente nel portale anche se è già stato aggiunto all'applicazione IoT Central. Usare i [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md) per il controllo degli accessi con granularità fine.

## <a name="iot-central-metrics"></a>Metriche di IoT Central

Per un elenco delle metriche attualmente disponibili per IoT Central, vedere [metriche supportate con monitoraggio di Azure](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Metriche e fatture

Le metriche possono essere diverse dai numeri visualizzati nella fattura di Azure IoT Central. Questa situazione si verifica per diversi motivi, ad esempio:

- IoT Central [piani tariffari standard](https://azure.microsoft.com/pricing/details/iot-central/) includono due dispositivi e variano le quote dei messaggi gratuitamente. Mentre gli elementi gratuiti sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- IoT Central genera automaticamente un ID del dispositivo di test per ogni modello di dispositivo nell'applicazione. Questo ID dispositivo è visibile nella pagina **Gestisci dispositivo di test** per un modello di dispositivo. I generatori di soluzioni possono scegliere di [convalidare i modelli di dispositivo](./overview-iot-central.md#create-device-templates) prima di pubblicarli generando codice che usa questi ID del dispositivo di test. Anche se questi dispositivi sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- Sebbene le metriche possano mostrare un subset di comunicazione da dispositivo a cloud, tutte le comunicazioni tra il dispositivo e il cloud vengono [conteggiate come un messaggio per la fatturazione](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i modelli di applicazione, il passaggio successivo suggerito consiste nell'apprendere come [gestire IOT Central dalla portale di Azure](howto-manage-iot-central-from-portal.md).