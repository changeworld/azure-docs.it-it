---
title: Domande frequenti sull'API Azure Cosmos DB per MongoDB
description: Risposte alle domande frequenti sull'API Azure Cosmos DB per MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019014"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Domande frequenti sull'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

L'API Azure Cosmos DB per MongoDB è un livello di compatibilità del protocollo wire che consente alle applicazioni di comunicare in modo semplice e trasparente con il motore di database nativo di Azure Cosmos usando gli SDK e i driver supportati dalla community esistenti per MongoDB. Gli sviluppatori possono ora usare toolchain e competenze di MongoDB esistenti per creare applicazioni che sfruttano i vantaggi offerti da Azure Cosmos DB. Gli sviluppatori traggono vantaggio dalle funzionalità esclusive di Azure Cosmos DB, tra cui la distribuzione globale con replica di scrittura in più aree, l'indicizzazione automatica, la manutenzione dei backup, i contratti di servizio con supporto finanziario e così via.

## <a name="how-do-i-connect-to-my-database"></a>Come ci si connette al database?

Il modo più veloce per connettersi a un database Cosmos con l'API di Azure Cosmos DB per MongoDB è quello di passare al [portale di Azure](https://portal.azure.com). Accedere al proprio account e quindi fare clic su **Avvio rapido** nel menu di spostamento a sinistra. La Guida introduttiva è il modo migliore per ottenere i frammenti di codice per la connessione al database.

Azure Cosmos DB applica standard e requisiti di sicurezza rigidi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite TLS. Assicurarsi quindi di usare TLSv 1.2.

Per altre informazioni, vedere [Connettersi al database Cosmos con l'API di Azure Cosmos DB per MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Codici di errore durante l'uso dell'API di Azure Cosmos DB per MongoDB?

Insieme ai codici di errore comuni di MongoDB, l'API Azure Cosmos DB per MongoDB presenta codici di errore specifici. Si trovano nella [Guida alla risoluzione dei problemi](mongodb-troubleshoot.md).

## <a name="supported-drivers"></a>Driver supportati

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Il driver Simba per MongoDB è supportato per l'uso con l'API di Azure Cosmos DB per MongoDB?

Sì, è possibile usare il driver ODBC Mongo di Simba con l'API di Azure Cosmos DB per MongoDB

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app Web .NET usando l'API Azure Cosmos DB per MongoDB](create-mongodb-dotnet.md)
* [Creare un'app console con Java e l'API MongoDB in Azure Cosmos DB](create-mongodb-java.md)
