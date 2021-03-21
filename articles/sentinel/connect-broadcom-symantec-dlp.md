---
title: Connettere i dati Broadcom Symantec Data Loss Prevention (DLP) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare Broadcom Symantec DLP Data Connector per eseguire il pull dei log DLP Symantec in Sentinel di Azure. Visualizzare i dati di Symantec DLP nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700883"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Connetti la prevenzione della perdita dei dati (DLP) Broadcom Symantec ad Azure Sentinel

> [!IMPORTANT]
> Broadcom Symantec DLP Connector è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo Broadcom Symantec DLP a Sentinel di Azure. Broadcom Symantec DLP Data Connector consente di connettere facilmente i log DLP Symantec con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Questa funzionalità consente di ottenere informazioni più dettagliate sulle informazioni dell'organizzazione, su dove si sposta e migliora le funzionalità operative di sicurezza. L'integrazione tra Broadcom Symantec DLP e Azure Sentinel si avvale di syslog con formato CEF, di un server d'utilità di log basato su Linux e dell'agente Log Analytics. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Inviare i log Broadcom Symantec DLP ad Azure Sentinel

Per ottenere i log in Azure Sentinel, configurare l'appliance Broadcom Symantec DLP per inviare messaggi syslog in formato CEF a un server di inoltrare log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **Broadcom Symantec DLP (Preview)** , quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Per istruzioni e spiegazioni più dettagliate, vedere [Step 1: deploy the log spedizioniere](connect-cef-agent.md) nella documentazione di Azure Sentinel.

    1. Inferiore a **2. Inviare i log di Symantec DLP a un agente syslog** : seguire le istruzioni di Broadcom per [configurare Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Questa configurazione deve includere gli elementi seguenti:
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta-TCP 514 (se consigliato altrimenti, assicurarsi di apportare la modifica parallela nel daemon syslog nel server di inoltri di log)
        - Formato log-CEF
        - Tipi di log: tutti disponibili o tutti appropriati

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. [Seguire questa procedura](https://aka.ms/sentinel-symantecdlp-parser) per creare l'alias della funzione **SymantecDLP** kusto.

Quindi, per eseguire una query su dati Broadcom Symantec DLP in Log Analytics, immettere nella `SymantecDLP` parte superiore della finestra query.

Vedere la scheda **passaggi successivi** nella pagina Connector per alcuni esempi di query utili.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Symantec DLP ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
