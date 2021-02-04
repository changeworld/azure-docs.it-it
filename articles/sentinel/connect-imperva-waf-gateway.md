---
title: Connettere il dispositivo WAF del gateway impervertito ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore WAF del gateway impervertito per eseguire il pull dei log di WAF in Sentinel di Azure. Visualizzare i dati WAF di inpervertito nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3094d20a921f9aa13e111e7af60955ce934b91db
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566805"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Connettere il dispositivo WAF del gateway impervertito ad Azure Sentinel

> [!IMPORTANT]
> Il connettore gateway WAF è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere l'appliance del gateway WAF impervertito ad Azure Sentinel. Il connettore dati del gateway perpervertito WAF consente di connettere facilmente i log del gateway WAF improprio con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra il gateway WAF e la sentinella di Azure si avvale di syslog in formato CEF, di un server d'utilità di log basato su Linux e dell'agente Log Analytics.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Inviare i log del gateway WAF a Sentinel di Azure

Per ottenere i log in Azure Sentinel, configurare l'appliance del gateway WAF impervertito per inviare i messaggi syslog in formato CEF a un server di inoltramento del log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **WAF Gateway (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Per istruzioni e spiegazioni più dettagliate, vedere [Step 1: deploy the log spedizioniere](connect-cef-agent.md) nella documentazione di Azure Sentinel.

    1. Inferiore a **2. Log CEF (Common Event Format) per l'agente syslog** : per questo connettore è necessario creare un' **interfaccia azione** e un **set di azioni** nella console di gestione **SecureSphere MX** . Seguire le istruzioni di impervertito per [abilitare la registrazione degli avvisi del gateway WAF in Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Questa configurazione deve includere gli elementi seguenti:
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta-TCP 514
        - Formato log-CEF
        - Tipi di log: tutti disponibili

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Per eseguire una query sui dati del gateway WAF inpervertito in Log Analytics, copiare quanto segue nella finestra di query, applicando altri filtri quando si sceglie:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Per esempi di query più utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere il gateway WAF a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
