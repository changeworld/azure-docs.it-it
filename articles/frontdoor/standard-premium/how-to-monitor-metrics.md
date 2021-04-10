---
title: Metriche di monitoraggio per Azure front door standard/Premium
description: Questo articolo descrive le metriche di monitoraggio standard/Premium di Azure front door.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557327"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Monitoraggio in tempo reale in Azure front door standard/Premium

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Azure front door standard/Premium è integrato con monitoraggio di Azure ed è dotato di 11 metriche che consentono di monitorare lo standard/Premium di Azure front door in tempo reale per tenere traccia, risolvere i problemi ed eseguire il debug dei problemi.  

Misure standard/Premium di Azure front door e invia le metriche in intervalli di 60 secondi. Le metriche possono richiedere fino a 3 minuti per essere visualizzate nel portale. Le metriche possono essere visualizzate in grafici o griglia di propria scelta e sono accessibili tramite il portale, PowerShell, l'interfaccia della riga di comando e l'API. Per altre informazioni, vedere [metriche di monitoraggio di Azure](../../azure-monitor/essentials/data-platform-metrics.md).  

Le metriche predefinite sono gratuite. È possibile abilitare metriche aggiuntive per un costo aggiuntivo. 

È possibile configurare gli avvisi per ogni metrica, ad esempio una soglia per 4XXErrorRate o 5XXErrorRate. Quando la percentuale di errori supera la soglia, viene attivato un avviso come configurato. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/alerts/alerts-metric.md). 

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Metriche supportate in Azure front door standard/Premium

| Metriche  | Descrizione | Dimensioni |
| ------------- | ------------- | ------------- |
| Percentuale riscontri byte | Percentuale di uscita dalla cache AFD, calcolata rispetto al totale in uscita. </br> **Percentuale di riscontri in byte** = (in uscita da Edge-uscita dall'origine)/egress da Edge. </br> **Scenari esclusi dal calcolo della percentuale di riscontri in byte**:</br> 1. non è possibile configurare in modo esplicito nessuna cache tramite il motore regole o il comportamento della memorizzazione nella cache della stringa di query. </br> 2. configurare in modo esplicito la direttiva di controllo della cache con nessun archivio o cache privata. </br>3. la percentuale di riscontri in byte può essere bassa se la maggior parte del traffico viene trasmessa all'origine anziché gestita dalla memorizzazione nella cache in base alle configurazioni o agli scenari. | Endpoint |
| RequestCount | Il numero di richieste client gestite dalla rete CDN. | Endpoint, paese client, area client, stato HTTP, gruppo stato HTTP |
| ResponseSize | Numero di richieste client gestite da AFD. |Endpoint, paese client, area client, stato HTTP, gruppo stato HTTP |
| TotalLatency | Tempo totale dalla richiesta client ricevuta dalla rete CDN **fino all'invio dell'ultimo byte di risposta dalla rete CDN al client**. |Endpoint, paese client, area client, stato HTTP, gruppo stato HTTP |
| RequestSize | Numero di byte inviati come richieste dai client a AFD. | Endpoint, paese client, area client, stato HTTP, gruppo stato HTTP |
| 4XX% ErrorRate | Percentuale di tutte le richieste client per cui il codice di stato della risposta è 4XX. | Endpoint, paese client, area client |
| 5XX% ErrorRate | Percentuale di tutte le richieste client per cui il codice di stato della risposta è 5XX. | Endpoint, paese client, area client |
| OriginRequestCount  | Numero di richieste inviate da AFD a Origin | Endpoint, origine, stato HTTP, gruppo di stato HTTP |
| OriginLatency | Tempo calcolato dal momento in cui la richiesta è stata inviata da AFD Edge al back-end fino a quando AFD ha ricevuto l'ultimo byte di risposta dal back-end. | Endpoint, origine |
| OriginHealth% | Percentuale di probe di integrità riusciti da AFD a Origin.| Origine, gruppo di origine |
| Conteggio richieste WAF | Richiesta WAF corrispondente. | Azione, nome regola, nome criterio |

## <a name="access-metrics-in-azure-portal"></a>Metriche di accesso in portale di Azure

1. Scegliere **tutte le risorse** dal menu portale di Azure  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. In **monitoraggio** selezionare **metriche**:

3. In **metriche** selezionare la metrica da aggiungere:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Screenshot della pagina metrica." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Selezionare **Aggiungi filtro** per aggiungere un filtro:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Screenshot dell'aggiunta di filtri alle metriche." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Selezionare **applica suddivisione** per suddividere i dati in base a dimensioni diverse:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Screenshot dell'aggiunta di dimensioni alle metriche." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Selezionare **nuovo grafico** per aggiungere un nuovo grafico:

## <a name="configure-alerts-in-azure-portal"></a>Configurare gli avvisi in portale di Azure

1. Configurare gli avvisi in Azure front door standard/Premium (anteprima) selezionando gli avvisi di **monitoraggio**  >>  .

1. Selezionare **nuova regola di avviso** per le metriche elencate nella sezione metrica.

L'avviso verrà addebitato in base al monitoraggio di Azure. Per altre informazioni sugli avvisi, vedere [avvisi di monitoraggio di Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [report standard e Premium di Azure front door](how-to-reports.md).
- Informazioni sui [log di Azure front door standard/Premium](how-to-logs.md).