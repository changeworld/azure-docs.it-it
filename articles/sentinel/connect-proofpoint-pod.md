---
title: Connettere i dati di sicurezza della posta elettronica di Proofpoint su richiesta ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore dati di sicurezza della posta elettronica di Proofpoint su richiesta per eseguire il pull dei log di sicurezza della posta elettronica in Sentinel di Azure. Visualizzare i dati di sicurezza di POD email nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: bdc9cbf942b88af93befa34f73eb3a90469cfcef
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873502"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Connetti la soluzione di sicurezza della posta elettronica di Proofpoint on demand ad Azure Sentinel

> [!IMPORTANT]
> Il connettore di sicurezza della posta elettronica di Proofpoint on demand è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere l'appliance di sicurezza della posta elettronica di Proofpoint on demand ad Azure Sentinel. POD Data Connector consente di connettere facilmente i log POD con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.  L'integrazione tra la sicurezza della posta elettronica di Proofpoint on demand e Azure Sentinel usa l'API WebSocket.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Per creare una app per le funzioni, è necessario disporre delle autorizzazioni di lettura e scrittura per le funzioni di Azure. [Altre informazioni su funzioni di Azure](../azure-functions/index.yml).

- È necessario disporre delle credenziali API WebSocket seguenti: ProofpointClusterID, ProofpointToken. [Altre informazioni sull'API WebSocket](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Configurare e connettere la sicurezza della posta elettronica di Proofpoint su richiesta

La sicurezza della posta elettronica di Proofpoint on demand può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Proofpoint on demand posta elettronica sicurezza (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire i passaggi descritti nella sezione **configurazione** della pagina del connettore.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, in *log personalizzati*, nelle tabelle seguenti:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 60 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere la sicurezza della posta elettronica di Proofpoint on demand ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.