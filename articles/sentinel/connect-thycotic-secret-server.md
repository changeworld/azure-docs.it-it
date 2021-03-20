---
title: Connettere il server Thycotic Secret ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Thycotic Secret Server per eseguire il pull dei log di Thycotic Secret Server in Sentinel di Azure. Visualizzare i dati del server Thycotic Secret nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567938"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Connettere il server Thycotic Secret ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Thycotic Secret Server è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo Thycotic Secret Server a Sentinel di Azure. Thycotic Secret Server Data Connector consente di connettere facilmente i log del server Thycotic Secret con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Thycotic e Azure Sentinel usa il connettore dati CEF per analizzare e visualizzare correttamente i messaggi syslog del server Secret.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro.

- Il server Secret di Thycotic deve essere configurato per esportare i log tramite syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Inviare i log del server Secret Thycotic ad Azure Sentinel

Per ottenere i log in Azure Sentinel, configurare il server Secret Thycotic per l'invio di messaggi syslog in formato CEF al server di inoltrare log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Thycotic Secret Server (anteprima)** e quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Per istruzioni e spiegazioni più dettagliate, vedere [Step 1: deploy the log spedizioniere](connect-cef-agent.md) nella documentazione di Azure Sentinel.

    1. Inferiore a **2. Per l'invio dei log CEF (Common Event Format) all'agente syslog** , seguire le istruzioni di Thycotic per [configurare Secret Server](https://thy.center/ss/link/syslog). Questa configurazione deve includere gli elementi seguenti:
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta- **TCP 514** (se consigliato altrimenti, assicurarsi di apportare la modifica parallela nel daemon syslog nel server di inoltri di log)
        - Formato log-CEF
        - Tipi di log: tutti disponibili

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Per eseguire query sui dati del server Thycotic Secret in Log Analytics, copiare il codice seguente nella finestra di query, applicando gli altri filtri quando si sceglie:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Vedere la scheda **passaggi successivi** nella pagina del connettore per alcune cartelle di lavoro e esempi di query utili.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Thycotic Secret Server a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.