---
title: Connettere i dati del cloud del servizio Salesforce ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati cloud del servizio Salesforce per eseguire il pull dei log di Salesforce in Sentinel di Azure. Visualizzare i dati di Salesforce nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: 152fee2e4e72cee6a7bc1c768c0a8ca7b031ec39
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878919"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Connettere il cloud del servizio Salesforce ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Cloud del servizio Salesforce è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere la soluzione cloud del servizio Salesforce a Sentinel di Azure. Il connettore di dati cloud del servizio Salesforce consente di connettere facilmente i dati di Salesforce con Azure Sentinel, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Salesforce Service Cloud e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Per creare una app per le funzioni, è necessario disporre delle autorizzazioni di lettura e scrittura per le funzioni di Azure. [Altre informazioni su funzioni di Azure](../azure-functions/index.yml).

- È necessario disporre delle credenziali API REST di Salesforce seguenti: **nome utente API Salesforce**, **password API Salesforce**, **token di sicurezza Salesforce**, **chiave utente Salesforce**, **segreto utente Salesforce**. [Altre informazioni sull'API REST di Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Configurare e connettere il cloud del servizio salesforce

Salesforce Service Cloud può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Salesforce Service Cloud (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire i passaggi descritti nella sezione **configurazione** della pagina del connettore.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **CustomLogs** , nella `SalesforceServiceCloud_CL` tabella.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Salesforce Service Cloud ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.