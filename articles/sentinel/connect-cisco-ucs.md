---
title: Connettere i dati di Cisco Unified Computing System (UCS) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare Cisco UCS Data Connector per eseguire il pull dei log UCS Cisco in Sentinel di Azure. Visualizzare i dati di Cisco UCS nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530672"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Connettere Cisco Unified Computing System (UCS) ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Cisco UCS è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere l'appliance Cisco Unified Computing System (UCS) ad Azure Sentinel. Cisco UCS Data Connector consente di connettere facilmente i log UCS con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Cisco UCS e Azure Sentinel usa syslog.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario configurare la soluzione Cisco UCS per esportare i log tramite syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Inviare i log UCS Cisco all'agente syslog  

Configurare Cisco UCS per l'invio dei messaggi syslog all'area di lavoro di Azure Sentinel tramite l'agente syslog.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **Cisco UCS (anteprima)** e quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni nella pagina del connettore **Cisco UCS** :

    1. Installare e caricare l'agente per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale).

    1. Configurare i log da raccogliere

        - Selezionare le funzionalità e i livelli di gravità nella configurazione degli agenti dell'area di lavoro.

    1. Configurare e connettere Cisco UCS

        - Seguire [queste istruzioni](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) per configurare Cisco UCS per l'invio di syslog. Per il server remoto, usare l'indirizzo IP del computer Linux in cui è stato installato l'agente Linux.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in syslog.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Cisco UCS ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
