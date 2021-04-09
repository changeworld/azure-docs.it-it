---
title: Connettere i dati secRMM di squadra Technologies ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di secRMM in squadra Technologies a Sentinel di Azure.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632902"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Connetti i dati di squadra Technologies secRMM ad Azure Sentinel 

Il connettore squadra Technologies secRMM consente di connettere facilmente i log della soluzione di sicurezza di squadra Technologies secRMM con Azure Sentinel. Consente di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questo connettore fornisce informazioni dettagliate sugli eventi di archiviazione rimovibili USB. L'integrazione tra squadra Technologies secRMM e Azure Sentinel si avvale dell'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Configurare e connettere le tecnologie squadra secRMM 

Squadra Technologies secRMM può integrare ed esportare i log direttamente in Sentinel di Azure.
1. Nel portale di Azure Sentinel fare clic su connettori dati e selezionare squadra Technologies secRMM e quindi aprire la pagina del connettore.

2. Seguire la procedura descritta nella [Guida all'onboarding delle tecnologie squadra per Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) per ottenere i dati SecRMM di squadra in Sentinel di Azure.   

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **CustomLogs** , nella `secRMM_CL` tabella.

Per eseguire una query sui log secRMM di squadra Technologies, immettere il nome della tabella nella parte superiore della finestra della query.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere squadra Technologies secRMM ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
