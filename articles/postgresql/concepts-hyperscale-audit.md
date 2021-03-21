---
title: Registrazione di controllo-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Concetti per la registrazione di controllo di pgAudit nel database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702114"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Registrazione di controllo nel database di Azure per PostgreSQL-iperscalabilità (CITUS)

Registrazione di controllo delle attività di database in database di Azure per PostgreSQL-iperscalabilità (CITUS) è disponibile tramite l'estensione di controllo PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornisce la registrazione dettagliata della sessione e/o del controllo degli oggetti.

> [!IMPORTANT]
> pgAudit è in anteprima su database di Azure per PostgreSQL-iperscalabilità (CITUS)

Se si vuole che i log a livello di risorsa di Azure per operazioni come il ridimensionamento di calcolo e archiviazione, vedere [log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo
Per impostazione predefinita, le istruzioni di log di pgAudit vengono emesse insieme alle istruzioni di log regolari usando la funzionalità di registrazione standard di Postgres. In database di Azure per PostgreSQL-iperscalabilità (CITUS) è possibile configurare tutti i log da inviare all'archivio dei log di monitoraggio di Azure per l'analisi successiva in Log Analytics. Se si Abilita la registrazione delle risorse di monitoraggio di Azure, i log verranno inviati automaticamente (in formato JSON) ad archiviazione di Azure, Hub eventi e/o log di monitoraggio di Azure, a seconda della scelta effettuata.

## <a name="enabling-pgaudit"></a>Abilitazione di pgAudit

L'estensione pgAudit è preinstallata e abilitata in tutti i nodi del gruppo di server CITUS (iperscalare). Non è necessaria alcuna azione per abilitarla.

## <a name="pgaudit-settings"></a>impostazioni pgAudit

pgAudit consente di configurare la registrazione di controllo di una sessione o di un oggetto. La [registrazione di controllo della sessione](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emette log dettagliati delle istruzioni eseguite. La [registrazione di controllo degli oggetti](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) è un ambito di controllo per relazioni specifiche. È possibile scegliere di configurare uno o entrambi i tipi di registrazione. 

> [!NOTE]
> le impostazioni pgAudit vengono specificate a livello globale e non possono essere specificate a livello di database o di ruolo.
>
> Inoltre, le impostazioni di pgAudit vengono specificate per nodo in un gruppo di server. Per apportare una modifica in tutti i nodi, è necessario applicarla singolarmente a ogni nodo.

È necessario configurare i parametri di pgAudit per avviare la registrazione. La [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornisce la definizione di ogni parametro. Testare prima i parametri e confermare che si sta ottenendo il comportamento previsto.

> [!NOTE]
> `pgaudit.log_client`Se si imposta su on, i log vengono reindirizzati a un processo client, ad esempio PSQL, anziché essere scritti nel file. Questa impostazione viene in genere disabilitata. <br> <br>
> `pgaudit.log_level` viene abilitato solo quando `pgaudit.log_client` è attivo.

> [!NOTE]
> In database di Azure per PostgreSQL-iperscalabilità (CITUS) `pgaudit.log` non può essere impostato usando un `-` collegamento di segno (meno), come descritto nella documentazione di pgAudit. Tutte le classi di istruzioni obbligatorie (lettura, scrittura e così via) devono essere specificate singolarmente.

## <a name="audit-log-format"></a>Formato del log di controllo
Ogni voce di controllo è indicata da `AUDIT:` vicino all'inizio della riga del log. Il formato del resto della voce è descritto in dettaglio nella [documentazione di pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Introduzione
Per iniziare rapidamente, impostare `pgaudit.log` su `WRITE` e aprire i log del server per esaminare l'output. 

## <a name="viewing-audit-logs"></a>Visualizzazione dei log di controllo
Il modo in cui si accede ai log dipende dall'endpoint scelto. Per archiviazione di Azure, vedere l'articolo relativo all' [account di archiviazione dei log](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Per gli hub eventi, vedere l'articolo [flusso di log di Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Per i log di monitoraggio di Azure, i log vengono inviati all'area di lavoro selezionata. I log Postgres usano la modalità di raccolta **AzureDiagnostics** , in modo che possano essere sottoposti a query dalla tabella AzureDiagnostics. I campi della tabella sono descritti di seguito. Per altre informazioni sull'esecuzione di query e avvisi, vedere Panoramica delle [query dei log di monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) .

È possibile utilizzare questa query per iniziare. È possibile configurare gli avvisi in base alle query.

Cerca tutte le voci pgAudit nei log Postgres per un determinato server nell'ultimo giorno
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come configurare la registrazione in database di Azure per PostgreSQL-iperscalabilità (CITUS) e come accedere ai log](howto-hyperscale-logging.md)