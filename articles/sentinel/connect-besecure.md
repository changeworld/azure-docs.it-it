---
title: Connettersi oltre la sicurezza proteggere i dati in Sentinel di Azure | Microsoft Docs
description: Informazioni su come usare il connettore di dati di sicurezza per proteggere i log di sicurezza in Azure Sentinel. Visualizzare i dati protetti in cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541164"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Connetti i tuoi confini di sicurezza a Sentinel di Azure

> [!IMPORTANT]
> Il connettore protetto da Beyond Security è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore di sicurezza da oltre la sicurezza consente di connettere facilmente tutti i log della soluzione di sicurezza più sicura con la sentinella di Azure, per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra la protezione e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Configurare e connettere con sicurezza

lo stato di protezione può essere integrato ed esportare i log direttamente in Sentinel di Azure.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta **Data Connector** selezionare **Beyond Security desecure (Preview)** e quindi aprire la **pagina Connector**.

1. Attenersi alla procedura seguente per configurare la soluzione di sicurezza per inviare i risultati dell'analisi, lo stato di analisi e i log audit trail ad Azure Sentinel.

    **Accedere al menu di integrazione:**
    1. Fare clic sull'opzione di menu "more"

    1. Selezione server

    1. Seleziona integrazione

    1. Abilitare Azure Sentinel 

    **Fornire sicurezza con le impostazioni di Sentinel di Azure:**

      Copiare i valori di *ID area di lavoro* e *chiave primaria* dalla pagina connettore di Sentinel di Azure, incollarli nella configurazione di protezione e fare clic su **modifica**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID area di lavoro e chiave primaria}":::

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **CustomLogs** , in una o più delle tabelle seguenti:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Per eseguire una query sui log di sicurezza nelle regole di analisi, le query di caccia, le indagini o altrove in Sentinel di Azure, immettere uno dei nomi di tabella precedenti nella parte superiore della finestra di query.

## <a name="validate-connectivity"></a>Convalidare la connettività
Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere la protezione a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
