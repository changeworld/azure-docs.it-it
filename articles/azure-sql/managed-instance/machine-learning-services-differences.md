---
title: Differenze principali per Machine Learning Services
description: Questo articolo descrive le differenze principali tra Machine Learning Services in Azure SQL Istanza gestita e SQL Server Machine Learning Services.
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
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599545"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Differenze principali tra Machine Learning Services in Istanza gestita di SQL di Azure e SQL Server

In questo articolo vengono descritte alcune delle principali differenze di funzionalità tra [Machine Learning Services in Azure SQL istanza gestita](machine-learning-services-overview.md) e [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Lingue supportate

Machine Learning Services sia in SQL Istanza gestita che SQL Server supportano Python e R [Extensibility Framework](/sql/machine-learning/concepts/extensibility-framework). Le differenze principali nei Istanza gestita SQL sono:

- Sono supportati solo Python e R. Non è possibile aggiungere linguaggi esterni quali Java.

- Le versioni iniziali di Python e R sono diverse:

  | Piattaforma                   | Versione runtime di Python           | Versioni di runtime R                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Istanza gestita di SQL di Azure | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 e 3.7.2 (CU22 e versioni successive) | 3.3.3 e 3.5.2 (CU22 e versioni successive)     |
  | SQL Server 2016            | Non disponibile                    | 3.2.2 e 3.5.2 (SP2 CU14 e versioni successive) |

## <a name="python-and-r-packages"></a>Pacchetti Python e R

Non è disponibile alcun supporto in SQL Istanza gestita per i pacchetti che dipendono da Runtime esterni (ad esempio, Java) o devono accedere alle API del sistema operativo per l'installazione o l'utilizzo.

Per ulteriori informazioni sulla gestione dei pacchetti Python e R, vedere:

- [Ottenere informazioni sui pacchetti Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Ottenere informazioni sui pacchetti R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Governance delle risorse

In SQL Istanza gestita non è possibile limitare le risorse R tramite [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)e i pool di risorse esterni non sono supportati.

Per impostazione predefinita, le risorse R sono impostate su un massimo del 20% delle risorse di SQL Istanza gestita disponibili quando è abilitata l'estendibilità. Per modificare questa percentuale predefinita, creare un ticket di supporto di Azure all'indirizzo [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

L'estendibilità è abilitata con i seguenti comandi SQL (SQL Istanza gestita verrà riavviato e non sarà disponibile per alcuni secondi):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Per disabilitare l'estendibilità e il ripristino del 100% delle risorse di memoria e CPU per SQL Server, usare i comandi seguenti:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

Le risorse totali disponibili per SQL Istanza gestita dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Errore di memoria insufficiente

L'utilizzo della memoria dipende dalla quantità usata negli script R e dal numero di query parallele in esecuzione. Se la memoria disponibile per R non è sufficiente, verrà ricevuto un messaggio di errore. I messaggi di errore comuni sono:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Se si riceve uno di questi errori, è possibile risolverlo scalando il database a un livello di servizio superiore.

## <a name="sql-managed-instance-pools"></a>Pool di Istanza gestita SQL

Machine Learning Services non è attualmente supportata nei [pool di istanza gestita SQL di Azure (anteprima)](instance-pools-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Vedere Panoramica, [Machine Learning Services in istanza gestita SQL di Azure](machine-learning-services-overview.md).
- Per informazioni su come usare Python in Machine Learning Services, vedere [eseguire script Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Per informazioni sull'uso di R in Machine Learning Services, vedere [eseguire script r](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
