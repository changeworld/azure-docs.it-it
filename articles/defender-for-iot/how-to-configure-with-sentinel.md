---
title: Configurare Sentinel di Azure per Defender
description: Viene illustrato come configurare Sentinel di Azure per ricevere i dati dalla soluzione Defender for Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2020
ms.author: shhazam
ms.openlocfilehash: 2d82aaadf158e45cb8faaeee0b9b4e0fc80a3420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247336"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>Connetti i tuoi dati da Defender for Internet ad Azure Sentinel 

Usare il connettore Defender for Internet per trasmettere in streaming tutti i difensori per gli eventi Internet in Sentinel di Azure. 

Questa integrazione consente alle organizzazioni di rilevare rapidamente gli attacchi multifase che spesso attraversano i limiti IT e OT. Inoltre, il Defender per l'integrazione delle cose con le funzionalità di orchestrazione, automazione e risposta della sicurezza di Sentinel di Azure consente la risposta e la prevenzione automatizzate mediante PlayBook predefiniti 

## <a name="prerequisites"></a>Prerequisiti

- Autorizzazioni di **lettura** e **scrittura** per l'area di lavoro in cui viene distribuito Azure Sentinel
- Il **Defender per** l'IT deve essere **abilitato** negli hub Internet rilevanti
- È necessario disporre delle autorizzazioni di **collaboratore** per la **sottoscrizione** che si vuole connettere

## <a name="connect-to-defender-for-iot"></a>Connetti a Defender per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi selezionare il **Defender per** le cose da fare (potrebbe essere ancora chiamato Centro sicurezza di Azure per tutto) dalla raccolta.

1. Nella parte inferiore del riquadro destro fare clic su **Apri pagina connettore**.

1. Fare clic su **Connetti** accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel.
    - Se Defender for Internet non è abilitato in almeno un hub Internet all'interno di una sottoscrizione, verrà visualizzato un messaggio di errore. Per rimuovere l'errore, abilitare Defender per le cose all'interno dell'hub Internet.

1. È possibile decidere se si desidera che gli avvisi da Defender per gli eventi per generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola di analisi predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati. Questa regola può essere modificata o modificata in **analisi**  >  **regole attive**.

> [!NOTE]
> L'aggiornamento dell'elenco di **sottoscrizioni** può richiedere più di 10 secondi dopo aver apportato modifiche alla connessione. 

## <a name="log-analytics-alert-view"></a>Visualizzazione avvisi Log Analytics

Per utilizzare lo schema pertinente in Log Analytics per visualizzare il Defender per gli avvisi di Internet delle cose:

1. Aprire **logs**  >  **SecurityInsights**  >  **SecurityAlert** o cercare **SecurityAlert**.

1. Filtrare per visualizzare solo il Defender per gli avvisi generati da un sacco di elementi usando il filtro KQL seguente:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Note servizio

Dopo la connessione di una **sottoscrizione**, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Defender per le cose a sentinella di Azure. Per ulteriori informazioni sul rilevamento delle minacce e sull'accesso ai dati di sicurezza, vedere gli articoli seguenti:

- Informazioni su come usare Sentinel di Azure per [ottenere visibilità dei dati e potenziali minacce](../sentinel/quickstart-get-visibility.md).
- Informazioni su come [accedere ai dati sulla sicurezza](how-to-security-data-access.md)