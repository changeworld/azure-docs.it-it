---
title: Azure Defender per SQL - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 28ec6659430cfdbc81533f05863ccb0ddc560e32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508033"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introduzione ad Azure Defender per SQL

Azure Defender per SQL include due piani di Azure Defender che estendono il [pacchetto di sicurezza dei dati](../azure-sql/database/azure-defender-for-sql.md) del Centro sicurezza di Azure per proteggere i database e i relativi dati in qualunque posizione si trovino. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|**Azure Defender per server di database SQL di Azure** - Disponibile a livello generale<br>**Azure Defender per server SQL nei computer** - Disponibile a livello generale |
|Prezzi:|I due piani che formano **Azure Defender per SQL** sono soggetti alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Versioni di SQL protette:|[SQL in macchine virtuali di Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Server SQL con abilitazione di Azure Arc](/sql/sql-server/azure-arc/overview)<br>Server SQL locali in computer Windows senza Azure Arc<br>[Database singoli](../azure-sql/database/single-database-overview.md) e [pool elastici](../azure-sql/database/elastic-pool-overview.md) di Azure SQL<br>[Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Pool SQL dedicato di Azure Synapse Analytics (in precedenza SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![Sì](./media/icons/yes-icon.png) Governo cinese (**parziale**: sottoinsieme di avvisi e valutazioni delle vulnerabilità per server SQL. Le protezioni da minacce comportamentali non sono disponibili.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Cosa è possibile proteggere con Azure Defender per SQL?

**Azure Defender per SQL** è costituito da due piani di Azure Defender distinti:

- **Azure Defender per server di database SQL di Azure** protegge:
    - [Database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md)
    - [Istanza gestita di database SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Pool SQL dedicato in Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender per server SQL nei computer** estende le protezioni per i server SQL nativi di Azure in modo da supportare completamente gli ambienti ibridi e proteggere i server SQL (tutte le versioni supportate) ospitati in Azure, in altri ambienti cloud e anche in computer locali:
    - [SQL Server in macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - SQL Server locali:
        - [SQL Server con abilitazione di Azure Arc (anteprima)](/sql/sql-server/azure-arc/overview)
        - [SQL Server in esecuzione in computer Windows senza Azure Arc](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quali sono i vantaggi di Azure Defender per SQL?

Questi due piani includono funzionalità per l'identificazione e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per i database:

- [Valutazione della vulnerabilità](../azure-sql/database/sql-vulnerability-assessment.md): servizio di analisi che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Le analisi di valutazione forniscono una panoramica dello stato di sicurezza dei computer SQL e i dettagli dei risultati relativi alla sicurezza.

- [Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md): servizio di rilevamento che monitora costantemente i server SQL per individuare minacce come attacchi SQL injection, attacchi di forza bruta e abuso dei privilegi. Questo servizio fornisce avvisi di sicurezza orientati all'azione nel centro Sicurezza di Azure, con i dettagli dell'attività sospetta, indicazioni su come attenuare le minacce e opzioni per proseguire le indagini con Azure Sentinel. 
    > [!TIP]
    > Per un elenco degli avvisi di sicurezza per SQL Server, vedere la [guida di riferimento sugli avvisi](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quali tipi di avvisi vengono forniti da Azure Defender per SQL?

Gli avvisi di sicurezza arricchiti con intelligence sulle minacce vengono attivati quando si verificano:

- **Potenziali attacchi SQL injection**, incluse le vulnerabilità rilevate quando le applicazioni generano un'istruzione SQL non corretta nel database
- **Accesso anomalo al database e modelli di query**, come ad esempio un numero insolitamente elevato di tentativi di accesso non riusciti con credenziali diverse (tentativo di attacco di forza bruta)
- **Attività di database sospette**, ad esempio un utente legittimo che accede a SQL Server da un computer violato che comunicava con un server di C&C per attività di crypto-mining

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati e raccomandazioni su come analizzare e risolvere le minacce.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per SQL. Per usare i servizi che sono stati descritti:

- Usare Azure Defender per SQL Server nei computer per [analizzare le vulnerabilità di SQL Server](defender-for-sql-usage.md)