---
title: Connetti migliore Mobile Threat Defense (MTD) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore dati MTD (BETTER Mobile Threat Defense) per eseguire il pull dei log MTD in Sentinel di Azure. Visualizzare i dati MTD nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541560"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Connetti il tuo MTD (Mobile Threat Defense) migliore ad Azure Sentinel

> [!IMPORTANT]
> Il connettore MTD (Mobile Threat Defense) migliore è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore MTD (Mobile Threat Defense) migliore consente di connettere facilmente tutti i log della soluzione MTD Security migliori con Azure Sentinel, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra BETTER Mobile Threat Defense e Azure Sentinel si avvale dell'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Configurare e connettere migliore Mobile Threat Defense

MIGLIORI MTD possono integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Better Mobile Threat Defense (MTD) (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire i passaggi nella pagina del connettore e in [Questa pagina dalla migliore documentazione di MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) per finalizzare l'integrazione in una console di MTD migliore.

    Quando viene richiesto di immettere l' **ID dell'area di lavoro** e i valori di **chiave primaria** , copiarli dalla pagina del connettore Sentinel di Azure e INCOLLARLI nella configurazione MTD migliore.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID area di lavoro e chiave primaria}":::

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **CustomLogs** , in una o più delle tabelle seguenti:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Per eseguire una query sui log MTD migliori nelle regole di analisi, sulle query di caccia o altrove in Sentinel di Azure, immettere uno dei nomi di tabella precedenti nella parte superiore della finestra di query.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere BETTER Mobile Threat Defense (MTD) a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
