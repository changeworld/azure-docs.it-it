---
title: Configurare gli endpoint privati per Azure Cosmos DB'archivio analitico.
description: Informazioni su come configurare endpoint privati gestiti per l'archivio analitico Azure Cosmos DB per limitare l'accesso alla rete.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479050"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Configurare le collegamento privato di Azure per l Azure Cosmos DB aserzione analitica
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In questo articolo si apprenderà come configurare endpoint privati gestiti per Azure Cosmos DB archivio analitico. Se si usa l'archivio transazionale, vedere [Endpoint privati per l'archivio transazionale.](how-to-configure-private-endpoints.md) Usando [gli endpoint privati gestiti,](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)è possibile limitare l'accesso alla rete dell'archivio analitico di Azure Cosmos DB a una rete virtuale gestita associata all'area Azure Synapse lavoro. Gli endpoint privati gestiti stabiliscono un collegamento privato all'archivio analitico.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Abilitare un endpoint privato per l'archivio analitico

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Configurare un Azure Synapse Analytics di lavoro con una rete virtuale gestita

[Creare un'area di lavoro Azure Synapse Analytics con l'esfiltrazione dei dati abilitata.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Con [la protezione dall'esfiltrazione](../synapse-analytics/security/workspace-data-exfiltration-protection.md)dei dati, è possibile assicurarsi che utenti malintenzionati non possano copiare o trasferire dati dalle risorse di Azure in posizioni esterne all'ambito dell'organizzazione.

Le restrizioni di accesso seguenti sono applicabili quando la protezione dall'esfiltrazione dei dati è attivata per un'area Azure Synapse Analytics lavoro:

* Se si usa Azure Spark per Azure Synapse Analytics, l'accesso è consentito solo agli endpoint privati gestiti approvati per Azure Cosmos DB archivio analitico.

* Se si usano pool SQL serverless Synapse, è possibile eseguire query su qualsiasi account Azure Cosmos DB usando Azure Synapse collegamento. Tuttavia, le richieste di scrittura che creano tabelle esterne come select [(CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) sono consentite solo agli endpoint privati gestiti approvati nella rete virtuale dell'area di lavoro.

> [!NOTE]
> Non è possibile modificare la configurazione della rete virtuale gestita e dell'esfiltrazione dei dati dopo la creazione dell'area di lavoro.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Aggiungere un endpoint privato gestito per l'Azure Cosmos DB analitico

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Dal riquadro portale di Azure passare all'area Synapse Analytics lavoro e aprire il **riquadro** Panoramica.

1. Avviare Synapse Studio passando al **riquadro** Attività iniziali e selezionare Apri **in** **Apri** Synapse Studio .

1. Nel Synapse Studio aprire la **scheda** Gestisci.

1. Passare a **Endpoint privati gestiti e** selezionare **Nuovo**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Creare un nuovo endpoint privato per l'archivio analitico." border="true":::

1. Selezionare **Azure Cosmos DB tipo di account (API SQL)** > **Continua**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selezionare Azure Cosmos DB'API SQL per creare un endpoint privato." border="true":::

1. Compilare il **modulo Nuovo endpoint privato** gestito con i dettagli seguenti:

   * **Nome:** nome per l'endpoint privato gestito. Questo nome non può essere aggiornato dopo la creazione.
   * **Descrizione:** fornire una descrizione descrittiva per identificare l'endpoint privato.
   * **Sottoscrizione di Azure:** selezionare un account Azure Cosmos DB dall'elenco degli account disponibili nelle sottoscrizioni di Azure.
   * **Azure Cosmos DB account:** selezionare un account Azure Cosmos DB esistente di tipo SQL o MongoDB.
   * **Sub-resouce** di destinazione: selezionare una delle opzioni seguenti: **Analitico**: se si vuole aggiungere l'endpoint privato per l Azure Cosmos DB'archivio analitico.
     **Sql** **(o MongoDB):** se si vuole aggiungere OLTP o l'endpoint dell'account transazionale.

   > [!NOTE]
   > È possibile aggiungere sia endpoint privati dell'archivio transazionale che dell'archivio analitico allo stesso account Azure Cosmos DB in un'area di lavoro Azure Synapse Analytics dati. Se si vogliono eseguire solo query analitiche, è possibile eseguire il mapping solo dell'endpoint privato analitico.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Scegliere analitico per la sottorisorsa di destinazione." border="true":::

1. Dopo la creazione, passare al nome dell'endpoint privato e selezionare **Gestisci approvazioni in portale di Azure**.

1. Passare al proprio account Azure Cosmos DB, selezionare l'endpoint privato e selezionare **Approva**.

1. Tornare all'area Synapse Analytics e fare clic **su Aggiorna** nel riquadro **Endpoint privati** gestiti. Verificare che lo stato dell'endpoint privato **sia Approvato.**

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Verificare che l'endpoint privato sia approvato." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Usare Apache Spark per Azure Synapse Analytics

Se è stata creata un'area di lavoro di Azure Synapse con la protezione dall'esfiltrazione dei dati attivata, l'accesso in uscita da Synapse Spark agli account Azure Cosmos DB verrà bloccato per impostazione predefinita. Inoltre, se l Azure Cosmos DB dispone già di un endpoint privato esistente, a Synapse Spark verrà impedito l'accesso.

Per consentire l'accesso Azure Cosmos DB dati:

* Se si usa un collegamento Azure Synapse per eseguire query Azure Cosmos DB dati, aggiungere un endpoint privato **analitico** gestito per l'account Azure Cosmos DB dati.

* Se si usano operazioni di scrittura/lettura batch e/o operazioni di scrittura/lettura in streaming nell'archivio transazionale, aggiungere un endpoint *privato SQL* o *MongoDB* gestito per l'account Azure Cosmos DB. È anche necessario impostare *connectionMode* su *Gateway,* come illustrato nel frammento di codice seguente:

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

## <a name="using-synapse-serverless-sql-pools"></a>Uso di pool SQL serverless Synapse

I pool SQL serverless synapse usano funzionalità multi-tenant che non vengono distribuite nella rete virtuale gestita. Se l'account Azure Cosmos DB dispone di un endpoint privato esistente, al pool SQL serverless Synapse verrà impedito l'accesso all'account, a causa dei controlli di isolamento della rete nell'account Azure Cosmos DB rete.

Per configurare l'isolamento rete per questo account da un'area di lavoro synapse:

1. Consentire all'area di lavoro Synapse di accedere Azure Cosmos DB'account specificando `NetworkAclBypassResourceId` l'impostazione per l'account.

   **Tramite PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Utilizzare l'interfaccia della riga di comando di Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB'account e Azure Synapse Analytics'area di lavoro devono essere nello stesso tenant Azure Active Directory (AD).

2. È ora possibile accedere all'account da pool SQL serverless, usando query T-SQL Azure Synapse Collegamento. Tuttavia, per garantire l'isolamento della rete per i dati nell'archivio analitico, è necessario aggiungere **un** endpoint privato gestito analitico per questo account. In caso contrario, ai dati nell'archivio analitico non verrà impedito l'accesso pubblico.

> [!IMPORTANT]
> Se si usa un collegamento Azure Synapse ed è necessario l'isolamento di rete per i dati nell'archivio  analitico, è necessario eseguire il mapping dell'account Azure Cosmos DB nell'area di lavoro Synapse usando l'endpoint privato gestito da Analisi.

## <a name="next-steps"></a>Passaggi successivi

* Introduzione [all'esecuzione di query nell'archivio analitico con Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Introduzione [all'esecuzione di query nell'archivio analitico Azure Synapse pool SQL serverless](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
