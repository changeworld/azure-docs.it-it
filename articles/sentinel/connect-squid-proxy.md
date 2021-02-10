---
title: Connettere i dati proxy di Squid ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati proxy Squid per eseguire il pull dei log del proxy Squid in Sentinel di Azure. Visualizzazione dei dati proxy di Squid nelle cartelle di lavoro, creazione di avvisi e miglioramento dell'analisi.
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
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090446"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>Connettere il proxy di Squid ad Azure Sentinel

> [!IMPORTANT]
> Il connettore del proxy Squid è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo proxy Squid ad Azure Sentinel. Il connettore di dati proxy Squid consente di connettere facilmente i log di Squid con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra il proxy di Squid e Azure Sentinel usa l'elaborazione di file locali da parte dell'agente Log Analytics.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>Inoltro dei log del proxy Squid all'agente Log Analytics  

Configurare il proxy Squid per inviare i file di log all'area di lavoro di Azure tramite l'agente di Log Analytics.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **Squid (anteprima)** e quindi **aprire la pagina del connettore**.

1. Seguire le istruzioni nella pagina del connettore del **proxy Squid** :

    1. Installare e caricare l'agente per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale).

    1. Configurare i log da raccogliere

        - Nelle impostazioni avanzate dell'area di lavoro aggiungere un tipo di log personalizzato, caricare un file di esempio e configurare come indicato.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, in **log personalizzati**, nella `SquidProxy_CL` tabella.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il proxy Squid ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
