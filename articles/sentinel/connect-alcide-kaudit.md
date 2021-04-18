---
title: Connettere i dati di Alcide kAudit Azure Sentinel| Microsoft Docs
description: Informazioni su come connettere i dati di Alcide kAudit Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600405"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Connettere Alcide kAudit a Azure Sentinel

> [!IMPORTANT]
> Il connettore dati Alcide kAudit in Azure Sentinel è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) consente di identificare i comportamenti anomali di Kubernetes e concentrarsi sulle violazioni e gli eventi imprevisti di Kubernetes riducendo al tempo stesso i tempi di rilevamento. Questo articolo illustra come connettere la soluzione Alcide kAudit a Azure Sentinel. Il connettore dati di Alcide kAudit consente di inserire facilmente i dati di log kAudit in Azure Sentinel, in modo che sia possibile visualizzarli nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra Alcide kAudit e Azure Sentinel usa l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area Azure Sentinel lavoro.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro.

## <a name="configure-and-connect-alcide-kaudit"></a>Configurare e connettere Alcide kAudit

Alcide kAudit può esportare i log direttamente Azure Sentinel.

1. Nel portale Azure Sentinel dati fare clic **su Connettori dati** nel menu di spostamento.

1. Selezionare **Alcide kAudit** dalla raccolta e quindi fare clic sul **pulsante Open connector page (Apri pagina connettore).**

1. Seguire le istruzioni dettagliate fornite nella Guida all'installazione di [Alcide kAudit.](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)

1. Quando viene richiesto l'ID area di lavoro e la chiave primaria, è possibile copiarli dalla pagina del connettore dati di Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ID area di lavoro e chiave primaria":::

## <a name="find-your-data"></a>Trovare i dati

Dopo aver stabilito una connessione riuscita, i dati vengono visualizzati in **Log** sotto i tipi di dati seguenti in **CustomLogs**:

- **alcide_kaudit_detections_1_CL** - Rilevamenti kAudit di Alcide 
- **alcide_kaudit_activity_1_CL** - Log attività di Alcide kAudit
- **alcide_kaudit_selections_count_1_CL** - Conteggi delle attività kAudit di Alcide
- **alcide_kaudit_selections_details_1_CL** - Dettagli dell'attività di Alcide kAudit

Per usare lo schema pertinente nei log per Alcide kAudit, cercare i tipi di dati indicati in precedenza.

La visualizzazione dei log in Log Analytics può richiedere fino a 20 minuti.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Alcide kAudit a Azure Sentinel. Per sfruttare al meglio le funzionalità integrate in questo connettore dati, fare clic sulla **scheda Passaggi** successivi nella pagina del connettore dati. Sono disponibili alcune query di esempio pronte per iniziare a trovare informazioni utili.

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
