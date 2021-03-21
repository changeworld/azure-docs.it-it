---
title: Connettere i dati dei log DNS di NXLog di Windows ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore dati dei log DNS di NXLog per effettuare il pull degli eventi DNS di Windows in Sentinel di Azure. Visualizzare i dati DNS di Windows nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745987"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Connettere i log DNS di NXLog Windows ad Azure Sentinel

> [!IMPORTANT]
> Il connettore dei log DNS di NXLog è attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore dei [log DNS di NXLog](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) consente di esportare facilmente tutti gli eventi del server DNS di Windows in Sentinel di Azure in tempo reale, fornendo informazioni approfondite sulle attività Domain Name Server nell'intera organizzazione. Utilizza ETW (High Performance [Event Tracing for Windows)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) per la raccolta di eventi del server DNS di controllo e analitici in tempo reale e supporta l'arricchimento degli eventi con i campi personalizzati. L'integrazione tra i log DNS di NXLog e Azure Sentinel viene facilitata tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Configurare e connettere i log DNS di NXLog

NXLog può essere configurato per inviare eventi in formato JSON direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **NXLog DNS logs** Connector.

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni dettagliate nell'argomento di integrazione della *Guida dell'utente di NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) per configurare l'invio tramite l'API REST.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** sotto la sezione  **log personalizzati** , nella tabella *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare NXLog per inserire i log DNS di Windows in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
