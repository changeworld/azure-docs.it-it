---
title: Vantaggio Azure Hybrid
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Usare licenze SQL Server esistenti per database SQL di Azure e sql Istanza gestita sconti.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779202"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Vantaggio Azure Hybrid - database SQL di Azure & SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Nel livello di calcolo con provisioning del modello di acquisto basato su vCore è possibile scambiare le licenze esistenti con tariffe scontate per database SQL di Azure e Istanza gestita di SQL di Azure usando Vantaggio Azure Hybrid [.](https://azure.microsoft.com/pricing/hybrid-benefit/) Questo vantaggio di Azure consente di risparmiare fino al 30% o anche superiore nel database SQL & SQL Istanza gestita usando le licenze di SQL Server con Software Assurance. La [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) contiene una calcolatrice che consente di determinare i risparmi.  Si noti che Vantaggio Azure Hybrid non si applica a database SQL di Azure serverless.

> [!NOTE]
> La modifica Vantaggio Azure Hybrid non richiede tempi di inattività.

![Struttura dei prezzi di vcore](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Scegliere un modello di licenza

Con Vantaggio Azure Hybrid è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza SQL Server esistente per il motore di database di SQL Server stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante che per la licenza SQL Server (prezzi inclusi nella licenza).

È possibile scegliere o modificare il modello di licenza nel portale di Azure: 
- Per i nuovi database, durante la creazione selezionare **Configura database** **nella** scheda Informazioni di base e selezionare l'opzione per risparmiare denaro.
- Per i database esistenti, **selezionare Configura** nel menu **Impostazioni** e selezionare l'opzione per risparmiare denaro.

È anche possibile configurare un database nuovo o esistente usando una delle API seguenti:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per impostare o aggiornare il tipo di licenza tramite PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Per impostare o aggiornare il tipo di licenza usando l'API REST:

- [Database - Creare o aggiornare](/rest/api/sql/databases/createorupdate)
- [Databases - Update](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Domande relative a Vantaggio Azure Hybrid

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Il Vantaggio Azure Hybrid per SQL Server include diritti di doppio uso della licenza?

Si può disporre dei diritti di doppio uso della licenza per 180 giorni, per garantire che le migrazioni vengano eseguite senza problemi. Dopo tale periodo di 180 giorni, è possibile usare solo la licenza SQL Server nel cloud nel database SQL. Non si hanno più diritti di utilizzo doppio in locale e nel cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Qual è la differenza tra il Vantaggio Azure Hybrid per SQL Server e la mobilità delle licenze?

Microsoft offre vantaggi per la mobilità delle licenze SQL Server clienti con Software Assurance. In questo modo è possibile riassegnare le licenze ai server condivisi di un partner. È possibile usare questo vantaggio in Azure IaaS e AWS EC2.

Il Vantaggio Azure Hybrid per SQL Server differisce dalla mobilità delle licenze per due aspetti principali:

- Offre vantaggi economici per lo spostamento di carichi di lavoro altamente virtualizzati in Azure. SQL Server Enterprise Edition i clienti possono ottenere quattro core in Azure nello SKU per utilizzo generico per ogni core di cui sono proprietari in locale per applicazioni altamente virtualizzate. La mobilità delle licenze non consente vantaggi di costo speciali per lo spostamento di carichi di lavoro virtualizzati nel cloud.
- Fornisce una destinazione PaaS in Azure (SQL Istanza gestita) altamente compatibile con SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quali sono i diritti specifici inclusi nel Vantaggio Azure Hybrid per SQL Server?

I clienti del database SQL e Istanza gestita SQL hanno i diritti seguenti associati a Vantaggio Azure Hybrid per SQL Server:

|Footprint delle licenze|Che cosa Vantaggio Azure Hybrid per SQL Server ottenere?|
|---|---|
|Clienti di SQL Server Enterprise Edition con Software Assurance|<li>Può pagare la tariffa di base su Hyperscale, per utilizzo generico o business critical SKU</li><br><li>1 core in locale = 4 core nello SKU hyperscale</li><br><li>1 core in locale = 4 core nello SKU del livello Utilizzo generico</li><br><li>1 core in locale = 1 core nello SKU del livello Business Critical</li>|
|Clienti di SQL Server Standard Edition con Software Assurance|<li>Può pagare la tariffa di base su Hyperscale, per utilizzo generico o business critical SKU</li><br><li>1 core in locale = 1 core nello SKU hyperscale</li><br><li>1 core in locale = 1 core nello SKU del livello Utilizzo generico</li><br><li>4 core in locale = 1 core in business critical SKU</li>|
|||


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla scelta di un Azure SQL di distribuzione, vedere [Scegliere l'opzione di distribuzione appropriata in Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Per un confronto tra le funzionalità del database SQL e Istanza gestita SQL, vedere Database [SQL & SQL Istanza gestita funzionalità](database/features-comparison.md).
