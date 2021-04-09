---
title: Connettere i dati di Juniper Networks SRX ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare Juniper SRX Data Connector per eseguire il pull dei log Juniper SRX in Azure Sentinel. Visualizzare i dati di Juniper SRX nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530638"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Connettere il firewall Juniper SRX ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Juniper SRX è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo del firewall Juniper SRX ad Azure Sentinel. Juniper SRX Data Connector consente di connettere facilmente i log SRX con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Juniper SRX e Azure Sentinel usa syslog.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- La soluzione Juniper SRX deve essere configurata per esportare i log tramite syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Inviare i log di Juniper SRX all'agente syslog  

Configurare Juniper SRX per l'invio dei messaggi syslog all'area di lavoro di Azure Sentinel tramite l'agente syslog.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **Juniper SRX (Preview)** , quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni nella pagina del connettore **Juniper SRX** :

    1. Installare e caricare l'agente per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale).

    1. Configurare i log da raccogliere

        - Selezionare le funzionalità e i livelli di gravità nella configurazione degli agenti dell'area di lavoro.

    1. Configurare e connettere Juniper SRX

        - Seguire queste istruzioni per configurare Juniper SRX per l'invio di syslog.
            - [Log del traffico (log dei criteri di sicurezza)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Log di sistema](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Per il server remoto, usare l'indirizzo IP del computer Linux in cui è stato installato l'agente Linux.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in syslog.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Juniper SRX ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
