---
title: Connettere Cisco Umbrella ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Cisco Umbrella data per estrarre i dati di ombrelli in Sentinel di Azure. Visualizzare i dati Umbrella nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566898"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Connetti il tuo Cisco Umbrella ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Cisco Umbrella è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore Cisco Umbrella consente di connettere facilmente tutti i log della soluzione Cisco Umbrella Security con la sentinella di Azure, di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Cisco Umbrella e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-cisco-umbrella"></a>Configurare e connettere Cisco Umbrella

Cisco Umbrella può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Cisco Umbrella (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire i passaggi descritti nella sezione **configurazione** della pagina del connettore.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **CustomLogs** , in una o più delle tabelle seguenti:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere i dati di Cisco Umbrella ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
