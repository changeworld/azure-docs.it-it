---
title: Connettere Trend Micro TippingPoint ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati di Trend Micro TippingPoint per eseguire il pull dei log di TippingPoint SMS in Sentinel di Azure. Visualizzare i dati TippingPoint nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: 5c7491a0e0ba2a3bf604988c613e1fd8937f277d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752173"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Connetti la tua soluzione Trend Micro TippingPoint ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Trend Micro TippingPoint è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere la soluzione Trend Micro TippingPoint Threat Protection al sistema Sentinel di Azure. Trend Micro TippingPoint Data Connector consente di connettere facilmente i log di TippingPoint Security Management System (SMS) con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Inviare i log di Trend Micro TippingPoint ad Azure Sentinel

Per ottenere i log in Azure Sentinel, configurare la soluzione TippingPoint TPS per inviare messaggi syslog in formato CEF a un server di inoltrare log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel. Il connettore usa una funzione del parser per convertire i dati ricevuti in uno schema normalizzato. 

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Trend Micro TippingPoint (anteprima)** e quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Per istruzioni e spiegazioni più dettagliate, vedere [Step 1: deploy the log spedizioniere](connect-cef-agent.md) nella documentazione di Azure Sentinel.

    1. Inferiore a **2. Invia i log di SMS tendenza Micro TippingPoint all'agente syslog** . questa configurazione deve includere gli elementi seguenti:
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta- **TCP 514** (se consigliato altrimenti, assicurarsi di apportare la modifica parallela nel daemon syslog nel server di inoltri di log)
        - Formato di log: **formato CEF ArcSight v 4.2**
        - Tipi di log: tutti disponibili

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Per ottenere i dati di Trend Micro TippingPoint in Log Analytics, viene eseguita una query sulla funzione del parser invece che sulla tabella. Copiare il codice seguente nella finestra della query, applicando altri filtri quando si sceglie:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Per altri esempi di query, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Trend Micro TippingPoint ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
