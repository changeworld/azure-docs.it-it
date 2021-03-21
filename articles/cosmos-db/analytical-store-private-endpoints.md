---
title: Configurare endpoint privati per Azure Cosmos DB archivio analitico.
description: Informazioni su come configurare endpoint privati gestiti per Azure Cosmos DB archivio analitico per limitare l'accesso alla rete.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048506"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Configurare endpoint privati per Azure Cosmos DB archivio analitico
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In questo articolo si apprenderà come configurare endpoint privati gestiti per Azure Cosmos DB archivio analitico. Se si utilizza l'archivio transazionale, vedere [l'articolo relativo agli endpoint privati per l'archivio transazionale](how-to-configure-private-endpoints.md) . Usando endpoint privati gestiti, è possibile limitare l'accesso alla rete di Azure Cosmos DB archivio analitico alla rete virtuale gestita da Azure sinapsi. Gli endpoint privati gestiti stabiliscono un collegamento privato all'archivio analitico.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Abilita Endpoint privato per l'archivio analitico

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Configurare un'area di lavoro di Azure sinapsi Analytics con una rete virtuale gestita

[Creare un'area di lavoro in Azure sinapsi Analytics con data-exfiltration abilitato.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Con la [protezione Exfiltration](../synapse-analytics/security/workspace-data-exfiltration-protection.md), è possibile garantire che gli utenti malintenzionati non possano copiare o trasferire dati dalle risorse di Azure in posizioni esterne all'ambito dell'organizzazione.

Quando è attivata la protezione dati exfiltration per un'area di lavoro di Azure sinapsi Analytics, sono applicabili le restrizioni di accesso seguenti:

* Se si usa Azure Spark per l'analisi delle sinapsi di Azure, l'accesso è consentito solo agli endpoint privati gestiti approvati per Azure Cosmos DB archivio analitico.

* Se si usano i pool SQL Server sinapsi, è possibile eseguire una query su qualsiasi account Azure Cosmos DB usando il collegamento sinapsi di Azure. Tuttavia, le richieste di scrittura che [creano tabelle esterne come Select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) sono consentite solo agli endpoint privati di gestione approvati nella rete virtuale dell'area di lavoro.

> [!NOTE]
> Dopo la creazione dell'area di lavoro, non è possibile modificare la configurazione della rete virtuale gestita e del exfiltration di dati.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Aggiungere un endpoint privato gestito per Azure Cosmos DB archivio analitico

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Dal portale di Azure passare all'area di lavoro di sinapsi Analytics e aprire il riquadro **Overview (panoramica** ).

1. Avviare sinapsi studio passando al riquadro **Introduzione** e selezionare **Apri** in **Open sinapsi Studio**.

1. In sinapsi Studio aprire la scheda **Gestisci** .

1. Passare a **endpoint privati gestiti** e selezionare **nuovo**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Creare un nuovo endpoint privato per l'archivio analitico." border="true":::

1. Selezionare il tipo di account **Azure Cosmos DB (API SQL)** > **continuare**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selezionare Azure Cosmos DB API SQL per creare un endpoint privato." border="true":::

1. Compilare il modulo **nuovo endpoint privato gestito** con i dettagli seguenti:

   * **Nome** : nome dell'endpoint privato gestito. Questo nome non può essere aggiornato dopo la creazione.
   * **Descrizione** : specificare una descrizione descrittiva per identificare l'endpoint privato.
   * **Sottoscrizione di Azure** : selezionare un account Azure Cosmos DB dall'elenco degli account disponibili nelle sottoscrizioni di Azure.
   * **Nome account Azure Cosmos DB** : selezionare un account di Azure Cosmos DB esistente di tipo SQL o MongoDB.
   * **Target Sub-resouce** : selezionare una delle opzioni seguenti: **Analytical**: se si vuole aggiungere l'endpoint privato per Azure Cosmos DB archivio analitico.
     **SQL** (o **MongoDB**): se si vuole aggiungere l'endpoint dell'account di OLTP o transazionale.

   > [!NOTE]
   > È possibile aggiungere endpoint privati sia per l'archivio transazionale che per quelli analitici allo stesso account Azure Cosmos DB in un'area di lavoro di Azure sinapsi Analytics. Se si vuole solo eseguire query analitiche, è possibile eseguire solo il mapping dell'endpoint privato analitico.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Scegliere analitica per la sottorisorsa di destinazione." border="true":::

1. Dopo la creazione, passare al nome dell'endpoint privato e selezionare **Gestisci approvazioni in portale di Azure**.

1. Passare all'account di Azure Cosmos DB, selezionare l'endpoint privato e selezionare **approva**.

1. Tornare all'area di lavoro di sinapsi Analytics e fare clic su **Aggiorna** nel riquadro **endpoint privati gestiti** . Verificare che l'endpoint privato sia in stato **approvato** .

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Verificare che l'endpoint privato sia approvato." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Usare Apache Spark per Azure sinapsi Analytics

Se è stata creata un'area di lavoro di Azure sinapsi con la protezione dati exfiltration attivata, l'accesso in uscita da sinapsi Spark a Azure Cosmos DB account verrà bloccato per impostazione predefinita. Inoltre, se il Azure Cosmos DB dispone già di un endpoint privato esistente, l'accesso a sinapsi Spark verrà bloccato.

Per consentire l'accesso ai dati Azure Cosmos DB:

* Se si usa il collegamento sinapsi di Azure per eseguire query Azure Cosmos DB dati, aggiungere un endpoint privato **analitico** gestito per l'account Azure Cosmos DB.

* Se si usano scritture/letture in batch e/o scritture/letture in streaming nell'archivio transazionale, aggiungere un endpoint gestito *SQL* o *MongoDB* privato per l'account Azure Cosmos DB. Inoltre, è necessario impostare *ConnectionMode* su *gateway* , come illustrato nel frammento di codice seguente:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Uso di pool SQL Server sinapsi

I pool SQL senza server di sinapsi usano funzionalità multi-tenant non distribuite nella rete virtuale gestita. Se l'account Azure Cosmos DB dispone di un endpoint privato esistente, l'accesso all'account non verrà bloccato dal pool SQL senza server, a causa dei controlli di isolamento rete sull'account Azure Cosmos DB.

Per configurare l'isolamento di rete per questo account da un'area di lavoro sinapsi:

1. Consentire all'area di lavoro sinapsi di accedere all'account di Azure Cosmos DB specificando l' `NetworkAclBypassResourceId` impostazione per l'account.

   **Tramite PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Utilizzare l'interfaccia della riga di comando di Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB account e l'area di lavoro di Azure sinapsi Analytics devono trovarsi nello stesso tenant di Azure Active Directory (AD).

2. È ora possibile accedere all'account da pool SQL senza server, usando le query T-SQL sul collegamento sinapsi di Azure. Tuttavia, per garantire l'isolamento di rete per i dati nell'archivio analitico, è necessario aggiungere un endpoint privato gestito **analitico** per l'account. In caso contrario, i dati nell'archivio analitico non verranno bloccati dall'accesso pubblico.

> [!IMPORTANT]
> Se si usa il collegamento sinapsi di Azure ed è necessario l'isolamento di rete per i dati nell'archivio analitico, è necessario eseguire il mapping dell'account Azure Cosmos DB nell'area di lavoro sinapsi usando un endpoint privato gestito **analitico** .

## <a name="next-steps"></a>Passaggi successivi

* Introduzione all' [esecuzione di query sull'archivio analitico con Azure sinapsi Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Introduzione all' [esecuzione di query sull'archivio analitico con i pool SQL Server sinapsi di Azure](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
