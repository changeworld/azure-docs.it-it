---
title: Panoramica del modello di acquisto vCore
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Il modello di acquisto vCore consente di ridimensionare in modo indipendente le risorse di calcolo e archiviazione, abbinare le prestazioni locali e ottimizzare il prezzo per database SQL di Azure e Istanza gestita di SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779184"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Panoramica del modello vCore - database SQL di Azure e Istanza gestita di SQL di Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Il modello di acquisto del core virtuale (vCore) usato da database SQL di Azure e Istanza gestita di SQL di Azure offre diversi vantaggi:

- Limiti di calcolo, memoria, I/O e archiviazione più elevati.
- Controllare la generazione dell'hardware per soddisfare meglio i requisiti di calcolo e memoria del carico di lavoro.
- Sconti sui prezzi per [Vantaggio Azure Hybrid (AHB)](../azure-hybrid-benefit.md) e [Istanza riservata (RI).](reserved-capacity-overview.md)
- Maggiore trasparenza nei dettagli hardware che favoriscono il calcolo, semplificando la pianificazione delle migrazioni da distribuzioni locali.

## <a name="service-tiers"></a>Livelli di servizio

Le opzioni del livello di servizio nel modello vCore includono per utilizzo generico, business critical e Hyperscale. Il livello di servizio definisce in genere l'architettura di archiviazione, i limiti di spazio e I/O e le opzioni di continuità aziendale correlate alla disponibilità e al ripristino di emergenza.

|-|**Utilizzo generico**|**Business Critical**|**Hyperscale**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione orientate al budget, bilanciate e scalabili. |Offre alle applicazioni aziendali la massima resilienza agli errori usando diverse repliche isolate e offre le prestazioni di I/O più elevate per ogni replica di database.|La maggior parte dei carichi di lavoro aziendali con requisiti di archiviazione e scalabilità in lettura altamente scalabili.  Offre maggiore resilienza agli errori consentendo la configurazione di più repliche di database isolate. |
|Archiviazione|Usa l'archiviazione remota.<br/>**Calcolo con provisioning del database SQL**:<br/>5 GB - 4 TB<br/>**Calcolo serverless**:<br/>5 GB - 3 TB<br/>**SQL Istanza gestita**: 32 GB - 8 TB |Usa l'archiviazione SSD locale.<br/>**Calcolo con provisioning del database SQL**:<br/>5 GB - 4 TB<br/>**Sql Istanza gestita**:<br/>32 GB - 4 TB |Estensione automatica flessibile dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Usa l'archiviazione SSD locale per la cache del pool di buffer locale e l'archiviazione dei dati locale. Usa l'archiviazione remota di Azure come archivio dati finale a lungo termine. |
|IOPS e velocità effettiva (approssimativa)|**Database SQL:** vedere Limiti delle risorse per [database singoli e](resource-limits-vcore-single-databases.md) pool [elastici.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Istanza gestita:** vedere [Panoramica dei limiti Istanza gestita di SQL di Azure risorse.](../managed-instance/resource-limits.md#service-tier-characteristics)|Vedere Limiti delle risorse per [database singoli e](resource-limits-vcore-single-databases.md) pool [elastici.](resource-limits-vcore-elastic-pools.md)|Hyperscale è un'architettura a più livelli con memorizzazione nella cache a più livelli. Le operazioni di I/O al secondo e la velocità effettiva effettive dipenderanno dal carico di lavoro.|
|Disponibilità|1 replica, nessuna replica con scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](read-scale-out.md),<br/>disponibilità elevata con ridondanza della zona|1 replica in lettura/scrittura e 0-4 [repliche con scalabilità in lettura](read-scale-out.md)|
|Backup|Archiviazione con ridondanza geografica e accesso in lettura [(RA-GRS),](../../storage/common/geo-redundant-design.md)da 1 a 35 giorni (7 giorni per impostazione predefinita)|[RA-GRS,](../..//storage/common/geo-redundant-design.md)1-35 giorni (7 giorni per impostazione predefinita)|Backup basati su snapshot nell'archiviazione remota di Azure. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscono sulle prestazioni di I/O di calcolo. I ripristini sono veloci e non sono un'operazione di dimensioni dei dati (che può richiedere minuti anziché ore o giorni).|
|In memoria|Non supportato|Supportato|Non supportato|
|||


### <a name="choosing-a-service-tier"></a>Scelta di un livello di servizio

Per informazioni sulla selezione di un livello di servizio per un determinato carico di lavoro, vedere gli articoli seguenti:

- [Quando scegliere il livello per utilizzo generico servizio](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello business critical di servizio](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio Hyperscale](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Livelli di calcolo

Le opzioni del livello di calcolo nel modello vCore includono i livelli di calcolo con provisioning e serverless.


### <a name="provisioned-compute"></a>Calcolo con provisioning

Il livello di calcolo con provisioning fornisce una quantità specifica di risorse di calcolo di cui viene eseguito il provisioning continuo indipendentemente dall'attività del carico di lavoro e fattura la quantità di calcolo di cui è stato effettuato il provisioning a un prezzo fisso all'ora.


### <a name="serverless-compute"></a>Calcolo serverless

Il [livello di calcolo serverless](serverless-tier-overview.md) ridimensiona automaticamente le risorse di calcolo in base all'attività del carico di lavoro e addebita la quantità di calcolo usata al secondo.



## <a name="hardware-generations"></a>Generazioni di hardware

Le opzioni di generazione dell'hardware nel modello vCore includono gen 4/5, serie M, serie Fsv2 e serie DC. La generazione di hardware definisce in genere i limiti di calcolo e memoria e altre caratteristiche che influiscono sulle prestazioni del carico di lavoro.

### <a name="gen4gen5"></a>Gen4/Gen5

- L'hardware Gen4/Gen5 offre risorse di calcolo e memoria bilanciate ed è adatto per la maggior parte dei carichi di lavoro di database che non hanno requisiti di memoria superiore, vCore superiore o vCore singolo più veloce, come indicato da Fsv2-series o serie M.

Per le aree in cui è disponibile Gen4/Gen5, vedere [Disponibilità di Gen4/Gen5.](#gen4gen5-1)

### <a name="fsv2-series"></a>Serie Fsv2

- La serie Fsv2 è un'opzione hardware ottimizzata per il calcolo che offre bassa latenza della CPU e velocità di clock elevata per i carichi di lavoro più impegnativi della CPU.
- A seconda del carico di lavoro, la serie Fsv2 può offrire più prestazioni della CPU per ogni vCore rispetto a Gen5 e le dimensioni di 72 vCore possono offrire prestazioni di CPU maggiori per un costo inferiore a 80 vCore in Gen5. 
- Fsv2 offre meno memoria e tempdb per vCore rispetto ad altri hardware, pertanto i carichi di lavoro sensibili a tali limiti possono prendere in considerazione la serie Gen5 o M.  

La serie Fsv2 in è supportata solo nel per utilizzo generico livello. Per le aree in cui è disponibile la serie Fsv2, vedere [Disponibilità della serie Fsv2](#fsv2-series-1).

### <a name="m-series"></a>Serie M

- La serie M è un'opzione hardware ottimizzata per la memoria per carichi di lavoro che richiedono più memoria e limiti di calcolo più elevati rispetto a quelli forniti da Gen5.
- La serie M offre 29 GB per vCore e fino a 128 vCore, con un aumento del limite di memoria rispetto a Gen5 di 8x a quasi 4 TB.

La serie M è supportata solo nel livello business critical e non supporta la ridondanza della zona.  Per le aree in cui è disponibile la serie M, vedere [Disponibilità della serie M](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Tipi di offerta di Azure supportati dalla serie M

Per accedere alla serie M, la sottoscrizione deve essere un tipo di offerta a pagamento che include pagamento in base al Contratto Enterprise (EA).  Per un elenco completo dei tipi di offerte di Azure supportati dalla serie M, vedere [Offerte correnti senza limiti di spesa.](https://azure.microsoft.com/support/legal/offer-details)

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Serie DC

> [!NOTE]
> La serie DC è attualmente in **anteprima pubblica.**

- L'hardware della serie DC usa processori Intel con tecnologia Software Guard Extensions (Intel SGX).
- La serie DC è necessaria per Always Encrypted [con enclave](/sql/relational-databases/security/encryption/always-encrypted-enclaves)sicuri, che non è supportato con altre configurazioni hardware.
- La serie DC è progettata per carichi di lavoro che elaborano dati sensibili e richiedono funzionalità di elaborazione di query riservate, fornite da Always Encrypted con enclave sicuri.
- L'hardware della serie DC offre risorse di calcolo e memoria bilanciate.

La serie DC è supportata solo per il calcolo con provisioning (Serverless non è supportato) e non supporta la ridondanza della zona. Per le aree in cui è disponibile la serie DC, vedere [Disponibilità della serie DC](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Tipi di offerta di Azure supportati dalla serie DC

Per accedere alla serie DC, la sottoscrizione deve essere un tipo di offerta a pagamento che include pagamento in base al Contratto Enterprise (EA).  Per un elenco completo dei tipi di offerta di Azure supportati dalla serie DC, vedere [Offerte correnti senza limiti di spesa.](https://azure.microsoft.com/support/legal/offer-details)

### <a name="compute-and-memory-specifications"></a>Specifiche di calcolo e memoria


|Generazione dell'hardware  |Calcolo  |Memoria  |
|:---------|:---------|:---------|
|Quarta generazione     |- Processori Intel® E5-2673 v3 (Haswell) a 2,4 GHz<br>- Effettuare il provisioning di un massimo di 24 vCore (1 vCore = 1 core fisico)  |- 7 GB per vCore<br>- Provisioning fino a 168 GB|
|Quinta generazione     |**Calcolo con provisioning**<br>- Processori Intel® E5-2673 v4 (Broadwell) a 2,3 GHz, Intel® SP-8160 (Skylake) e \* Intel® 8272CL (Cascade Lake) da 2,5 GHz \*<br>- Effettuare il provisioning di un massimo di 80 vCore (1 vCore = 1 hyper-thread)<br><br>**Calcolo serverless**<br>- Processori Intel® E5-2673 v4 (Broadwell) a 2,3 GHz e Intel® SP-8160 (Skylake)*<br>- Scalabilità automatica fino a 40 vCore (1 vCore = 1 hyper-thread)|**Calcolo con provisioning**<br>- 5,1 GB per vCore<br>- Provisioning fino a 408 GB<br><br>**Calcolo serverless**<br>- Scalabilità automatica fino a 24 GB per vCore<br>- Scalabilità automatica fino a 120 GB al massimo|
|Serie Fsv2     |- Processori Intel® 8168 (Skylake)<br>- Con una velocità di clock turbo all core sostenuta di 3,4 GHz e una velocità massima del turbo clock singolo core di 3,7 GHz.<br>- Provisioning fino a 72 vCore (1 vCore = 1 hyper-thread)|- 1,9 GB per vCore<br>- Provisioning fino a 136 GB|
|Serie M     |- Processori Intel® E7-8890 v3 a 2,5 GHz e Intel® 8280M a 2,7 GHz (Cascade Lake)<br>- Provisioning fino a 128 vCore (1 vCore = 1 hyper-thread)|- 29 GB per vCore<br>- Provisioning fino a 3,7 TB|
|Serie DC     | - Processori Intel XEON E-2288G<br>- Con estensione Intel Software Guard (Intel SGX))<br>- Effettuare il provisioning di un massimo di 8 vCore (1 vCore = 1 core fisico) | 4,5 GB per vCore |

\* Nella vista [sys.dm_user_db_resource_governance a](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) gestione dinamica, la generazione di hardware per i database che usano processori Intel® SP-8160 (Skylake) viene visualizzata come Gen6, mentre la generazione di hardware per i database che usano Intel® 8272CL (Cascade Lake) viene visualizzata come Gen7. I limiti delle risorse per tutti i database Gen5 sono gli stessi indipendentemente dal tipo di processore (Broadwell, Skylake o Cascade Lake).

Per altre informazioni sui limiti delle risorse, vedere Limiti delle risorse per database singoli [(vCore)](resource-limits-vcore-single-databases.md)o Limiti delle risorse per i pool [elastici (vCore).](resource-limits-vcore-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Selezione di una generazione di hardware

Nel portale di Azure è possibile selezionare la generazione dell'hardware per un database o un pool nel database SQL al momento della creazione oppure è possibile modificare la generazione hardware di un database o di un pool esistente.

**Per selezionare una generazione di hardware durante la creazione di un database SQL o di un pool**

Per informazioni dettagliate, vedere [Creare un database SQL](single-database-create-quickstart.md).

Nella scheda **Informazioni di base** selezionare il collegamento Configura **database** nella sezione Calcolo **e** archiviazione e quindi selezionare il collegamento **Modifica configurazione:**

  ![configurare il database](./media/service-tiers-vcore/configure-sql-database.png)

Selezionare la generazione di hardware desiderata:

  ![selezionare l'hardware](./media/service-tiers-vcore/select-hardware.png)


**Per modificare la generazione dell'hardware di un database SQL o di un pool esistente**

Per un database, nella pagina Panoramica selezionare il **collegamento Piano** tariffario:

  ![modifica dell'hardware](./media/service-tiers-vcore/change-hardware.png)

Per un pool, nella pagina Panoramica selezionare **Configura**.

Seguire i passaggi per modificare la configurazione e selezionare la generazione dell'hardware come descritto nei passaggi precedenti.

**Per selezionare una generazione di hardware durante la creazione di un Istanza gestita**

Per informazioni dettagliate, vedere [Creare un'istanza di SQL Istanza gestita](../managed-instance/instance-create-quickstart.md).

Nella scheda **Informazioni di base** selezionare il collegamento Configura **database** nella sezione **Calcolo e** archiviazione e quindi selezionare la generazione di hardware desiderata:

  ![configurare sql Istanza gestita](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Per modificare la generazione dell'hardware di un'istanza di SQL Istanza gestita**

# <a name="the-azure-portal"></a>[Il portale di Azure](#tab/azure-portal)

Nella pagina sql Istanza gestita selezionare il **collegamento Piano tariffario** nella sezione Impostazioni

![modificare l'hardware Istanza gestita SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Nella pagina Piano tariffario sarà possibile modificare la generazione dell'hardware come descritto nei passaggi precedenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare lo script di PowerShell seguente:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Per altri dettagli, vedere [il comando Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance)

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Per altri dettagli, vedere [il comando az sql mi update.](/cli/azure/sql/mi#az_sql_mi_update)

---

### <a name="hardware-availability"></a>Disponibilità hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

L'hardware di generazione 4 [viene gradualmente sfasato](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) e non è più disponibile per le nuove distribuzioni. Tutti i nuovi database devono essere distribuiti nell'hardware gen5.

Gen5 è disponibile in tutte le aree pubbliche in tutto il mondo.

#### <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è disponibile nelle aree seguenti: Australia centrale, Australia centrale 2, Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Asia orientale, Stati Uniti orientali, Francia centrale, India centrale, Corea centrale, Corea meridionale, Europa settentrionale, Sudafrica settentrionale, Asia sud-orientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali 2.


#### <a name="m-series"></a>Serie M

La serie M è disponibile nelle aree seguenti: Stati Uniti orientali, Europa settentrionale, Europa occidentale, Stati Uniti occidentali 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Serie DC

> [!NOTE]
> La serie DC è attualmente in **anteprima pubblica.**

La serie DC è disponibile nelle aree seguenti: Canada centrale, Canada orientale, Stati Uniti orientali, Europa settentrionale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali.

Se è necessaria la serie DC in un'area attualmente non supportata, inviare un [ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) di supporto seguendo le istruzioni riportate in Richiedere aumenti della quota per database SQL di Azure [e SQL Istanza gestita](quota-increase-request.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere: 
- [Creazione di un database SQL usando il portale di Azure](single-database-create-quickstart.md)
- [Creazione di un Istanza gestita SQL tramite il portale di Azure](../managed-instance/instance-create-quickstart.md)

Per informazioni dettagliate sui prezzi, [vedere la database SQL di Azure prezzi.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Per informazioni dettagliate sulle dimensioni di calcolo e archiviazione specifiche disponibili nei livelli di servizio utilizzo generico e business critical, vedere:

- [Limiti delle risorse basate su vCore per database SQL di Azure](resource-limits-vcore-single-databases.md).
- [Limiti delle risorse basate su vCore per le risorse in pool database SQL di Azure](resource-limits-vcore-elastic-pools.md).
- [Limiti delle risorse basate su vCore per Istanza gestita di SQL di Azure](../managed-instance/resource-limits.md).
