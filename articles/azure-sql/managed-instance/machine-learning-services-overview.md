---
title: Machine Learning Services in Istanza gestita di SQL di Azure
description: Questo articolo fornisce una panoramica o Machine Learning Services in Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599562"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services in Istanza gestita di SQL di Azure

Machine Learning Services è una funzionalità di Istanza gestita SQL di Azure che offre l'apprendimento automatico nel database, che supporta gli script Python e R. La funzionalità include i pacchetti Microsoft Python e R per l'analisi predittiva ad alte prestazioni e l'apprendimento automatico. I dati relazionali possono essere usati negli script tramite stored procedure, script T-SQL contenenti istruzioni Python o R oppure codice Python o R contenente T-SQL.

## <a name="what-is-machine-learning-services"></a>Che cos'è Machine Learning Services?

Machine Learning Services in Azure SQL Istanza gestita consente di eseguire script Python e R nel database. È possibile usare questa funzionalità per preparare e pulire i dati, eseguire la progettazione delle caratteristiche e il training, la valutazione e la distribuzione di modelli di Machine Learning all'interno di un database. La funzionalità esegue gli script nella posizione in cui i dati risiedono, eliminando la necessità di trasferire i dati in rete in un altro server.

Usare Machine Learning Services con supporto R/Python in Azure SQL Istanza gestita per:

- **Eseguire script r e Python per la preparazione dei dati e l'elaborazione di dati generici** . è ora possibile trasferire gli script r/Python in Azure SQL istanza gestita dove si trovano i dati, anziché spostare i dati in un altro server per eseguire script r e Python. È possibile eliminare la necessità di spostamento dei dati e problemi associati relativi a latenza, sicurezza e conformità.

- **Training di modelli di machine learning nel database** : è possibile eseguire il training dei modelli usando qualsiasi algoritmo open source. È possibile ridimensionare facilmente il training sull'intero set di dati anziché basarsi sui set di dati di esempio estratti dal database.

- **Distribuire i modelli e gli script nell'ambiente di produzione nelle stored procedure** . gli script e i modelli sottoposti a training possono essere operativi semplicemente incorporati nelle stored procedure T-SQL. Le app che si connettono ad Azure SQL Istanza gestita possono trarre vantaggio da stime e intelligence in questi modelli semplicemente chiamando una stored procedure. È anche possibile usare la funzione di stima T-SQL nativa per rendere operativo i modelli per un punteggio rapido in scenari con punteggio in tempo reale altamente simultanei.

Le distribuzioni di base di Python e R sono incluse in Machine Learning Services. È possibile installare e usare pacchetti e framework open source, come PyTorch, TensorFlow e scikit-learn, oltre ai pacchetti Microsoft [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) e [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) per Python e [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapR](/sql/machine-learning/r/ref-r-olapr) e [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) per R.

## <a name="how-to-enable-machine-learning-services"></a>Come abilitare Machine Learning Services

È possibile abilitare Machine Learning Services in Azure SQL Istanza gestita abilitando l'estendibilità con i seguenti comandi SQL (SQL Istanza gestita verrà riavviato e non sarà disponibile per alcuni secondi):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Per informazioni dettagliate su come questo comando influisca sulle risorse di SQL Istanza gestita, vedere [governance delle risorse](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Abilitare Machine Learning Services in un gruppo di failover

In un [gruppo di failover](failover-group-add-instance-tutorial.md), i database di sistema non vengono replicati nell'istanza secondaria (vedere [limitazioni dei gruppi di failover](../database/auto-failover-group-overview.md#limitations-of-failover-groups) per ulteriori informazioni).

Se il Istanza gestita in uso fa parte di un gruppo di failover, eseguire le operazioni seguenti:

- `sp_configure` `RECONFIGURE` Per abilitare Machine Learning Services, eseguire i comandi e in ogni istanza del gruppo di failover.

- Installare le librerie R/Python in un database utente piuttosto che nel database master.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [differenze principali tra SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Per informazioni su come usare Python in Machine Learning Services, vedere [eseguire script Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Per informazioni sull'uso di R in Machine Learning Services, vedere [eseguire script r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Per ulteriori informazioni su Machine Learning su altre piattaforme SQL, vedere la [documentazione di SQL Machine Learning](/sql/machine-learning/index).
