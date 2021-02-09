---
title: Cartella di lavoro di inattività, SLA e interruzioni-Application Insights
description: Calcola e segnala il contratto di servizio per il test Web tramite un unico riquadro di vetro tra le risorse Application Insights e le sottoscrizioni di Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: 884d65d10ab0360957360efa8bf9dc4cac59949b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989396"
---
# <a name="downtime-sla-and-outages-workbook"></a>Tempi di inattività, SLA e interruzioni della cartella di lavoro

Introduzione di un modo semplice per calcolare e segnalare il contratto di servizio per i test Web tramite un unico riquadro di vetro tra le risorse Application Insights e le sottoscrizioni di Azure. Il report sui tempi di inattività e le interruzioni fornisce potenti query predefinite e visualizzazioni dei dati per migliorare la comprensione della connettività del cliente, dei tempi di risposta tipici dell'applicazione e del tempo di inattività.

Il modello di cartella di lavoro SLA è accessibile tramite la raccolta di cartelle di lavoro nella risorsa Application Insights o tramite la scheda disponibilità selezionando i **report SLA** nella parte superiore.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Screenshot della scheda disponibilità con i report del contratto di contratto evidenziati." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Screenshot della raccolta di cartelle di lavoro con tempi di inattività e interruzioni della cartella di lavoro evidenziati" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flessibilità del parametro

I parametri impostati nella cartella di lavoro influiscono sul resto del report.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Screenshot della scheda parametri di interruzione/manutenzione nella cartella di lavoro interruzioni e interruzioni." lightbox ="./media/sla-report/outages.png":::

`Subscriptions``App Insights Resources` `Web Test` i parametri, e determinano le opzioni per le risorse di alto livello. Questi parametri sono basati sulle query di log Analytics e vengono usati in ogni query del report.

`Failure Threshold` e `Outage Window` consentono di determinare i criteri per un'interruzione del servizio, ad esempio i criteri per l'avviso di disponibilità di App Insights in base al contatore della posizione non riuscita in un periodo selezionato. La soglia tipica è tre posizioni in un intervallo di cinque minuti.

`Maintenance Period` consente di selezionare la frequenza di manutenzione tipica ed `Maintenance Window` è un selettore DateTime per un esempio di periodo di manutenzione. Tutti i dati che si verificano durante il periodo identificato verranno ignorati nei risultati.

`Availability Target 9s` Specifica l'obiettivo 9 di destinazione da due 9 a cinque 9.

## <a name="overview-page"></a>Pagina di panoramica

La pagina di panoramica contiene informazioni di alto livello sul contratto di servizio totale (esclusi i periodi di manutenzione se definiti), le istanze di interruzione end-to-end e il tempo di inattività dell'applicazione. Le istanze di interruzione sono definite da quando un test inizia ad avere esito negativo fino a quando non viene completato in base ai parametri di interruzione. Se un test ha esito negativo alle 8:00 AM e viene nuovamente riavviato alle 10:00 AM, l'intero periodo di dati viene considerato la stessa interruzione.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" GIF della pagina panoramica che mostra la tabella di panoramica per test." lightbox="./media/sla-report/overview.gif":::

È anche possibile esaminare l'interruzione più lunga che si è verificata durante il periodo di Reporting.

Alcuni test sono ricollegabili alla risorsa Application Insights per un'analisi più approfondita, ma ciò è possibile solo nella [risorsa Application Insights basata sull'area di lavoro](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Tempo di inattività, interruzioni ed errori

La scheda interruzioni **e tempi di inattività** contiene informazioni sulle istanze di interruzioni totali e tempo di inattività totale suddivise per test. La scheda **errori per percorso** include una mappa geografica dei percorsi di test non riusciti per identificare le potenziali aree di connessione del problema.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF della scheda interruzioni e tempi di inattività e errore per posizione nella cartella di lavoro tempi di inattività e interruzioni." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Modificare il report

È possibile modificare il report come qualsiasi altra [cartella di lavoro di monitoraggio di Azure](../platform/workbooks-overview.md). È possibile personalizzare le query o le visualizzazioni in base alle esigenze del team.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF della selezione del pulsante modifica per modificare la visualizzazione in un grafico a torta." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

Tutte le query possono essere eseguite in [log Analytics](../log-query/log-analytics-overview.md) e usate in altri report o dashboard. Rimuovere la restrizione del parametro e riutilizzare la query principale.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF della query di log." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Accesso e condivisione

Il report può essere condiviso con i team, la leadership o aggiunto a un dashboard per un ulteriore utilizzo. L'utente deve disporre dell'autorizzazione di lettura/accesso alla risorsa di Application Insights in cui è archiviata la cartella di lavoro effettiva.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Screenshot della condivisione di questo modello." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Passaggi successivi

- [Log Analytics suggerimenti](../log-query/query-optimization.md)per l'ottimizzazione delle query.
- Informazioni su come [creare un grafico nelle cartelle di lavoro](../platform/workbooks-chart-visualizations.md)di.
- Informazioni su come monitorare il sito Web con i [test di disponibilità](monitor-web-app-availability.md).
