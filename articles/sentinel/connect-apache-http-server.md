---
title: Connettere il server HTTP Apache ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Apache HTTP Server per eseguire il pull dei log Apache in Sentinel di Azure. Visualizzare i dati Apache nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096855"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Connettere il server HTTP Apache ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Apache HTTP Server è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il server HTTP Apache ad Azure Sentinel. Il connettore Apache HTTP Server consente di inserire facilmente i log del server HTTP Apache in Sentinel di Azure, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, eseguire query per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Apache HTTP Server e Azure Sentinel usa l'elaborazione di file locali da parte dell'agente Log Analytics.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di scrittura per l'area di lavoro di Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configurare e integrare i log di Apache HTTP Server tramite Log Analytics Agent

Configurare il server HTTP Apache per inviare i file di log all'area di lavoro di Azure tramite l'agente di Log Analytics.
Configurare Log Analytics Agent per la lettura dei file di log di Apache HTTP Server.

1. Seguire le istruzioni in https://httpd.apache.org/docs/2.4/logs.html per impostare il percorso dei file di log in Apache HTTP Server.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati** , quindi selezionare **Apache HTTP Server (anteprima)**.

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni nella pagina del server HTTP Apache.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Apache HTTP Server ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
