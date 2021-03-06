---
title: Ottenere informazioni dettagliate con backup Center
description: Informazioni su come analizzare le tendenze cronologiche e ottenere informazioni più dettagliate sui backup con il centro di backup.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506013"
---
# <a name="obtain-insights-using-backup-center"></a>Ottenere informazioni dettagliate con backup Center

Per analizzare le tendenze cronologiche e ottenere informazioni più approfondite sui backup, Backup Center fornisce un'interfaccia per il backup dei [report](configure-reports.md), che usa i [log di monitoraggio di Azure](../azure-monitor/logs/data-platform-logs.md) e le [cartelle di lavoro di Azure](../azure-monitor/visualize/workbooks-overview.md). I report di backup offrono le funzionalità seguenti:

- Allocazione e previsione dello spazio di archiviazione cloud utilizzato.

- Controllo delle attività dii backup e ripristino.

- Identificazione delle tendenze principali a diversi livelli di granularità.

- Ottenere visibilità e informazioni approfondite sulle opportunità di ottimizzazione dei costi per i backup.

## <a name="supported-scenarios"></a>Scenari supportati

- I report di backup non sono attualmente disponibili per il backup del server database di Azure per PostgreSQL.

- Per un elenco dettagliato degli scenari supportati e non supportati, vedere la [matrice di supporto](backup-center-support-matrix.md) .

## <a name="get-started"></a>Introduzione

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Configurare gli insiemi di credenziali per l'invio di dati a un'area di lavoro Log Analytics

[Informazioni su come configurare le impostazioni di diagnostica su larga scala per gli insiemi di credenziali](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Visualizzare i report di backup nel portale di backup Center

Selezionando la voce di menu **backup Reports** in Backup Center vengono aperti i report. Scegliere una o più aree di lavoro Log Analytics per visualizzare e analizzare le informazioni chiave sui backup.

![Report di backup nel centro di backup](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Di seguito sono riportate le visualizzazioni disponibili:

1. **Riepilogo** : usare questa scheda per ottenere una panoramica di alto livello del patrimonio di backup. [Scopri di più](./configure-reports.md#summary)

2. **Elementi di backup** : usare questa scheda per visualizzare informazioni e tendenze sull'archiviazione cloud usata a livello di elemento di backup. [Scopri di più](./configure-reports.md#backup-items)

3. **Utilizzo** : usare questa scheda per visualizzare i parametri di fatturazione principali per i backup. [Scopri di più](./configure-reports.md#usage)

4. **Processi** : usare questa scheda per visualizzare le tendenze con esecuzione prolungata nei processi, ad esempio il numero di processi non riusciti al giorno e le cause principali dell'errore del processo. [Scopri di più](./configure-reports.md#jobs)

5. **Criteri** : usare questa scheda per visualizzare le informazioni su tutti i criteri attivi, ad esempio il numero di elementi associati e l'archiviazione cloud totale utilizzata dagli elementi di cui è stato eseguito il backup in base a un determinato criterio. [Scopri di più](./configure-reports.md#policies)

6. **Ottimizza** : usare questa scheda per ottenere visibilità sulle possibili opportunità di ottimizzazione dei costi per i backup. [Scopri di più](./configure-reports.md#optimize)

7. **Conformità ai criteri** : utilizzare questa scheda per ottenere una visibilità specifica se a ogni istanza di backup è stato associato almeno un backup al giorno. [Scopri di più](./configure-reports.md#policy-adherence)

È inoltre possibile configurare messaggi di posta elettronica per tutti i report utilizzando la funzionalità [report di posta elettronica](backup-reports-email.md) .

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare e gestire i backup](backup-center-monitor-operate.md)
- [Governare la proprietà di backup](backup-center-govern-environment.md)
- [Eseguire azioni con backup Center](backup-center-actions.md)