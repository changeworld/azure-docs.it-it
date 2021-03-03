---
title: Configurare Azure Defender per la soluzione basata su agenti it
description: Informazioni su come configurare la raccolta dati in Azure Defender per la soluzione basata su agenti it
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733296"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Configurare Azure Defender per la soluzione basata su agenti it  

Questo articolo descrive come configurare la raccolta dati in Azure Defender per la soluzione basata su agenti.

## <a name="configure-data-collection"></a>Configurare la raccolta dei dati

Per configurare la raccolta dati in Azure Defender per la soluzione basata su agenti Internet: 

1. Passare alla portale di Azure e selezionare l'hub delle cose a cui è associato il Defender 

1.  ****   Scegliere **Impostazioni** dal menu sicurezza. 

1. Selezionare **raccolta dati**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Selezionare raccolta dati dalle impostazioni del menu sicurezza.":::

## <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP 

Per garantire la sicurezza della soluzione Internet delle cose, gli indirizzi IP delle connessioni in ingresso e in uscita per i dispositivi Internet delle cose, IoT Edge e gli hub Internet vengono raccolti e archiviati per impostazione predefinita. Queste informazioni sono essenziali e vengono usate per rilevare una connettività anomala da origini di indirizzi IP sospetti. Ad esempio, quando vengono eseguiti tentativi che tentano di stabilire connessioni da un'origine di indirizzo IP di una botnet nota o da un'origine di indirizzo IP esterna alla georilevazione. Il Defender per il servizio Internet, offre la flessibilità necessaria per abilitare e disabilitare la raccolta dei dati degli indirizzi IP in qualsiasi momento. 

Per abilitare o disabilitare la raccolta di dati degli indirizzi IP: 

1. Aprire l'hub Internet e quindi selezionare **Impostazioni**   dal menu **sicurezza**   . 

1. Selezionare la schermata **raccolta dati**   e modificare la georilevazione e le impostazioni di gestione degli indirizzi IP in base alle proprie esigenze. 

## <a name="log-analytics-creation"></a>Creazione di Log Analytics 

Defender for Internet è possibile archiviare gli avvisi di sicurezza, le raccomandazioni e i dati di sicurezza non elaborati nell'area di lavoro Log Analytics. Per impostazione predefinita, la Log Analytics inserimento nell'hub Internet è **disattivata** nella soluzione Defender for Internet. È possibile, ad esempio, connettere il Defender per le cose a un'area di lavoro di analisi dei log e archiviare anche i dati di sicurezza. 

Per impostazione predefinita, nell'area di lavoro Log Analytics sono archiviati due tipi di informazioni per Defender:
 
- Avvisi di sicurezza.

- Raccomandazioni. 

È possibile scegliere di aggiungere l'archiviazione di un tipo di informazioni aggiuntivo come `raw events` . 

> [!Note] 
> L'archiviazione  `raw events`   in log Analytics comporta costi di archiviazione aggiuntivi. 

Per abilitare l'uso di Log Analytics con micro Agent: 

1. Passare alla   >  **raccolta dei dati** di configurazione dell'area di lavoro e impostare l'interruttore  **su on**. 

1. Crea una nuova area di lavoro Log Analytics o ne alleghi una esistente. 

1. Verificare che l'opzione **accesso ai dati di sicurezza non elaborata**   sia selezionata.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Verificare che l'accesso ai dati di sicurezza non elaborati sia selezionato.":::

1. Selezionare  **Salva**.

Ogni mese, i primi 5 gigabyte di dati inseriti, per cliente nel servizio Log Analytics di Azure, sono gratuiti. Ogni gigabyte di dati inseriti nell'area di lavoro di Azure Log Analytics viene conservato senza costi aggiuntivi per i primi 31 giorni. Per ulteriori informazioni sui prezzi, vedere [log Analytics prezzi](https://azure.microsoft.com/pricing/details/monitor/). 

Per modificare la configurazione dell'area di lavoro di Log Analytics: 

1. Nell'hub Internet delle cose scegliere  **Impostazioni** dal menu **sicurezza** . 

1. Selezionare la schermata **raccolta dati**   e modificare la configurazione dell'area di lavoro di log Analytics impostazioni in base alle esigenze. 

Per accedere agli avvisi nell'area di lavoro Log Analytics dopo la configurazione:

1. Selezionare un avviso in Defender per l'intero.

1. Selezionare **esaminare gli avvisi nell'area di lavoro log Analytics**.

Per accedere agli avvisi nell'area di lavoro Log Analytics dopo la configurazione:

1. Selezionare una raccomandazione in Defender per l'it.

1. Selezionare **analisi consigli nell'area di lavoro log Analytics**. 
 
Per ulteriori informazioni sull'esecuzione di query sui dati di Log Analytics, vedere [Introduzione alle query in log Analytics](../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Disattiva Defender per le cose 

Per attivare o disattivare un Defender per il servizio Internet in uno specifico hub Internet: 

1. Nell'hub Internet delle cose scegliere  **Impostazioni** dal menu **sicurezza** .

1. Selezionare la schermata **raccolta dati**   e modificare la configurazione dell'area di lavoro di log Analytics impostazioni in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi 

Passare all'articolo successivo per [configurare la soluzione](quickstart-configure-your-solution.md).