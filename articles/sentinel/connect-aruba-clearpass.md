---
title: Connettere i dati di Aruba ClearPass ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Aruba ClearPass per eseguire il pull dei log di Aruba ClearPass in Sentinel di Azure. Visualizzare i dati di Aruba ClearPass nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745702"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Connettere il ClearPass di Aruba ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Aruba ClearPass è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo ClearPass di Aruba ad Azure Sentinel. Il connettore di dati Aruba ClearPass consente di connettere facilmente i log di Aruba ClearPass a Sentinel di Azure, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, eseguire query per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Aruba ClearPass e Azure Sentinel usa syslog con formato CEF, un server d'utilità di log basato su Linux e l'agente di Log Analytics. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Inviare i log di Aruba ClearPass ad Azure Sentinel

Per ottenere i log in Azure Sentinel, configurare l'appliance Aruba ClearPass per l'invio di messaggi syslog in formato CEF a un server di inoltrato di log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Aruba ClearPass (anteprima)** e quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Per istruzioni e spiegazioni più dettagliate, vedere [Step 1: deploy the log spedizioniere](connect-cef-agent.md) nella documentazione di Azure Sentinel.

    1. Inferiore a **2. Per inviare i log di Aruba ClearPass a un agente syslog** , seguire le istruzioni di Aruba per [configurare ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Questa configurazione deve includere gli elementi seguenti:
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta-TCP 514 (se consigliato altrimenti, assicurarsi di apportare la modifica parallela nel daemon syslog nel server di inoltri di log)
        - Formato log-CEF
        - Tipi di log: tutti disponibili o tutti appropriati

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. [Seguire questa procedura](https://aka.ms/sentinel-arubaclearpass-parser) per creare l'alias della funzione **ArubaClearPass** kusto.

Quindi, per eseguire query sui dati di Aruba ClearPass in Log Analytics, immettere nella `ArubaClearPass` parte superiore della finestra della query.

Vedere la scheda **passaggi successivi** nella pagina Connector per alcuni esempi di query utili.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Aruba ClearPass ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
