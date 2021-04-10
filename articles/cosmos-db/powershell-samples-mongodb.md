---
title: Esempi di Azure PowerShell per l'API Azure Cosmos DB per MongoDB
description: Ottenere gli esempi di Azure PowerShell per eseguire attività comuni nell'API Azure Cosmos DB per MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: 2f852b1ec359033c8bc700c8a382705b4479d6de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503352"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Esempi di Azure PowerShell per l'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

La tabella seguente contiene collegamenti a script di Azure PowerShell comunemente usati per Azure Cosmos DB. Usare i collegamenti a destra per passare agli esempi specifici delle API. Gli esempi comuni sono uguali per tutte le API. Per tutti i cmdlet di PowerShell per Azure Cosmos DB sono disponibili pagine di riferimento nelle [informazioni di riferimento su Azure PowerShell](/powershell/module/az.cosmosdb). Il `Az.CosmosDB` modulo fa ora parte del `Az` modulo. [Scaricare e installare](/powershell/azure/install-az-ps) la versione più recente di AZ Module per ottenere i cmdlet di Azure Cosmos DB. È anche possibile ottenere la versione più recente dal [PowerShell Gallery](https://www.powershellgallery.com/packages/Az/5.4.0). È anche possibile creare una copia tramite fork di questi esempi di PowerShell per Cosmos DB dal repository GitHub, nella pagina degli [esempi di PowerShell per Cosmos DB in GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Esempi comuni

|Attività | Descrizione |
|---|---|
|[Aggiornare un account](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare il livello di coerenza predefinito di un account Azure Cosmos DB. |
|[Aggiornare le aree di un account](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aggiornare le aree di un account Cosmos DB. |
|[Cambiare la priorità di failover o attivare un failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cambiare la priorità di failover a livello di area di un account Azure Cosmos o attivare un failover manuale. |
|[Chiavi dell'account o stringhe di connessione](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ottenere le chiavi primarie e secondarie e le stringhe di connessione oppure rigenerare la chiave di un account Azure Cosmos DB. |
|[Creare un account Cosmos con il firewall IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account Azure Cosmos DB con il firewall IP abilitato. |
|||

## <a name="mongo-db-api-samples"></a>Esempi per l'API Mongo DB

|Attività | Descrizione |
|---|---|
|[Creare un account, un database e una raccolta](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un account, un database e una raccolta Azure Cosmos DB. |
|[Creare un account, un database e una raccolta con scalabilità automatica](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Creare un account, un database e una raccolta con scalabilità automatica Azure Cosmos. |
|[Elencare o ottenere database o raccolte](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Elencare o ottenere un database o una raccolta. |
|[Operazioni di velocità effettiva](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Operazioni di velocità effettiva per un database o una raccolta, incluse le operazioni get, update e migrate, tra la velocità effettiva con scalabilità automatica e la velocità effettiva standard. |
|[Bloccare le risorse per impedirne l'eliminazione](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedire l'eliminazione delle risorse tramite blocchi. |
|||
