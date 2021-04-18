---
title: Monitorare l'integrità di un'Azure IoT Central di | Microsoft Docs
description: In qualità di operatore o amministratore, monitorare l'integrità complessiva dei dispositivi connessi all'IoT Central app.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: df89d53e6b5043c1ef3caa1c92f2abaae542d6ec
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599010"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Monitorare l'integrità complessiva di un IoT Central app

> [!NOTE]
> Le metriche sono disponibili solo per le applicazioni IoT Central versione 3. Per informazioni su come controllare la versione dell'applicazione, vedere [Informazioni sull'applicazione.](./howto-get-app-info.md)

*Questo articolo si applica a operatori e amministratori.*

Questo articolo illustra come usare il set di metriche fornito da IoT Central per valutare l'integrità dei dispositivi connessi all'applicazione IoT Central e l'integrità delle esportazioni di dati in esecuzione.

Le metriche sono abilitate per impostazione predefinita per l IoT Central applizione e si accede da portale di Azure [.](https://portal.azure.com/) La [Monitoraggio di Azure data platform espone queste](../../azure-monitor/essentials/data-platform-metrics.md) metriche e offre diversi modi per interagire con esse. Ad esempio, è possibile usare grafici nel portale di Azure, un'API REST o query in PowerShell o nell'interfaccia della riga di comando di Azure.

### <a name="trial-applications"></a>Applicazioni di valutazione

Le applicazioni che usano il piano di valutazione gratuito non hanno una sottoscrizione di Azure associata e quindi non supportano Monitoraggio di Azure metriche. È possibile [convertire un'applicazione in un piano tariffario standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e ottenere l'accesso a queste metriche.

## <a name="view-metrics-in-the-azure-portal"></a>Visualizzare le metriche nel portale di Azure

I passaggi seguenti presuppongono che si [abbia un'IoT Central con](./quick-deploy-iot-central.md) alcuni dispositivi [connessi](./tutorial-connect-device.md) o un'esportazione dati [in esecuzione.](howto-export-data.md)

Per visualizzare IoT Central metriche nel portale:

1. Passare alla risorsa IoT Central dell'applicazione nel portale. Per impostazione predefinita, IoT Central risorse si trovano in un gruppo di risorse denominato **IOTC**.
1. Per creare un grafico dalle metriche dell'applicazione, selezionare **Metriche** nella **sezione** Monitoraggio.

![Metriche di Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>portale di Azure autorizzazioni

L'accesso alle metriche nel portale di Azure è gestito dal controllo degli accessi in base [al ruolo di Azure.](../../role-based-access-control/overview.md) Usare il portale di Azure per aggiungere utenti all'applicazione IoT Central/gruppo di risorse/sottoscrizione per concedere loro l'accesso. È necessario aggiungere un utente nel portale anche se è già stato aggiunto all'IoT Central applicazione. Usare [i ruoli predefiniti di Azure per](../../role-based-access-control/built-in-roles.md) un controllo di accesso più granulare.

## <a name="iot-central-metrics"></a>IoT Central metriche

Per un elenco delle metriche attualmente disponibili per l'IoT Central, vedere [Metriche supportate con](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)Monitoraggio di Azure .

### <a name="metrics-and-invoices"></a>Metriche e fatture

Le metriche possono differire dai numeri visualizzati nella fattura Azure IoT Central fatturazione. Questa situazione si verifica per diversi motivi, ad esempio:

- IoT Central piani [tariffari standard](https://azure.microsoft.com/pricing/details/iot-central/) includono due dispositivi e quote di messaggi variabili gratuitamente. Anche se gli elementi gratuiti sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- IoT Central automaticamente un ID dispositivo di test per ogni modello di dispositivo nell'applicazione. Questo ID dispositivo è visibile nella pagina **Gestisci dispositivo di test** per un modello di dispositivo. I generatori di soluzioni possono scegliere di convalidare i modelli di dispositivo prima di pubblicarli generando codice che usa questi ID dispositivo di test. Anche se questi dispositivi sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- Anche se le metriche possono mostrare un subset della comunicazione da dispositivo a cloud, tutte le comunicazioni tra il dispositivo e il cloud vengono conteggiati come un messaggio per [la fatturazione.](https://azure.microsoft.com/pricing/details/iot-central/)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i modelli di [applicazione,](howto-manage-iot-central-from-portal.md)il passaggio successivo consigliato consiste nell'apprendere come gestire i IoT Central dalla portale di Azure .