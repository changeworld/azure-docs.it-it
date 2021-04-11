---
title: Connetti i dati di Sophos cloud Optix ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare Sophos cloud Optix Connector per eseguire il pull dei <PRODUCT NAME> log in Sentinel di Azure. Visualizzazione <PRODUCT NAME> dei dati nelle cartelle di lavoro, creazione di avvisi e miglioramento dell'analisi.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700872"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Connettere Sophos cloud Optix ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Sophos cloud Optix è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Sophos cloud Optix Connector consente di connettere facilmente tutti i log della soluzione Sophos cloud Optix Security con la sentinella di Azure, di visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi.  Questa funzionalità offre informazioni più dettagliate sul comportamento di sicurezza e conformità del cloud dell'organizzazione e migliora le funzionalità operative di sicurezza del cloud. L'integrazione tra Sophos cloud Optix e Azure Sentinel si avvale dell'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Configurare e connettere Sophos cloud Optix

Sophos cloud Optix può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **Sophos cloud Optix (anteprima)**.

1. Selezionare **Apri connettore pagina**.

1. Copiare e salvare l' **ID dell'area di lavoro** e la **chiave primaria** dalla pagina del connettore.

1. Seguire le istruzioni di Sophos per l' [integrazione con Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (a partire dal terzo passaggio).

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** sotto la sezione **log personalizzati** , nella tabella *SophosCloudOptix_CL* .

Per eseguire una query su Sophos cloud Optix data, immettere `SophosCloudOptix_CL` nella finestra query. Vedere la scheda **passaggi successivi** nella pagina del connettore e la [documentazione di Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)per alcuni esempi di query utili.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Sophos cloud Optix ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
