---
title: Connettere Azure Defender per le cose a Sentinel di Azure | Microsoft Docs
description: Informazioni su come connettere Azure Defender (in precedenza il Centro sicurezza di Azure) per i dati di Internet delle cose in Sentinel di Azure.
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
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631442"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Connetti i tuoi dati da Azure Defender (noto in precedenza come Centro sicurezza di Azure) per le cose ad Azure Sentinel 


> [!IMPORTANT]
> Defender for Internet Data Connector è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare il connettore Defender for Internet per trasmettere in streaming tutti i difensori per gli eventi Internet in Sentinel di Azure. 

Questa integrazione consente alle organizzazioni di rilevare rapidamente gli attacchi multifase che spesso attraversano i limiti IT e OT. Inoltre, il Defender per l'integrazione delle cose con le funzionalità di orchestrazione, automazione e risposta della sicurezza di Sentinel di Azure consente la risposta e la prevenzione automatizzate mediante PlayBook predefiniti 
## <a name="prerequisites"></a>Prerequisiti

- Autorizzazioni di **lettura** e **scrittura** per l'area di lavoro in cui viene distribuito Azure Sentinel
- Il **Defender per** l'IT deve essere **abilitato** negli hub Internet rilevanti
- È necessario disporre delle autorizzazioni di **collaboratore** per la **sottoscrizione** che si vuole connettere

## <a name="connect-to-defender-for-iot"></a>Connetti a Defender per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi selezionare **Defender for** Internet (potrebbe essere ancora chiamato Centro sicurezza di Azure per Internet) dalla raccolta.

1. Nella parte inferiore del riquadro destro fare clic su **Apri pagina connettore**. 

1. Fare clic su **Connetti** accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel. 
    - Se Defender for Internet non è abilitato in almeno un hub Internet all'interno di una sottoscrizione, verrà visualizzato un messaggio di errore. Per rimuovere l'errore, abilitare Defender per le cose all'interno dell'hub Internet.

1. È possibile decidere se si desidera che gli avvisi da Defender per gli eventi per generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola di analisi predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati. Questa regola può essere modificata o modificata in **analisi**  >  **regole attive**.

> [!NOTE]
> L'aggiornamento dell'elenco di **sottoscrizioni** può richiedere più di 10 secondi dopo aver apportato modifiche alla connessione. 

## <a name="log-analytics-alert-view"></a>Visualizzazione avvisi Log Analytics

Per utilizzare lo schema pertinente in Log Analytics per visualizzare il Defender per gli avvisi di Internet delle cose:

1. Aprire **logs**  >  **SecurityInsights**  >  **SecurityAlert** o cercare **SecurityAlert**. 

2. Filtrare per visualizzare solo il Defender per gli avvisi generati da un sacco di elementi usando il filtro KQL seguente:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Note servizio

Dopo la connessione di una **sottoscrizione**, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Defender per le cose a sentinella di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
