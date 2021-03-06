---
title: Connettere un'applicazione MongoDB ad Azure Cosmos DB
description: Informazioni su come connettere un'app MongoDB a Azure Cosmos DB ottenendo la stringa di connessione da portale di Azure
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/08/2021
ms.reviewer: sngun
robots: noindex
ms.openlocfilehash: bd244b0bb0aa5c5b2377b5a5675466645da3256c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99982096"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connettere un'applicazione MongoDB ad Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Informazioni su come connettere un'app MongoDB ad Azure Cosmos DB usando una stringa di connessione MongoDB. È quindi possibile usare un database di Azure Cosmos DB come archivio dati per l'app MongoDB. Oltre all'esercitazione riportata di seguito, è possibile esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API Azure Cosmos DB per MongoDB.

Questa esercitazione illustra due modi per recuperare le informazioni della stringa di connessione:

- [Metodo di avvio rapido](#get-the-mongodb-connection-string-by-using-the-quick-start), per l'uso con i driver .NET, Node.js, MongoDB Shell, Java e Python
- [Metodo della stringa di connessione personalizzata](#get-the-mongodb-connection-string-to-customize)da usare con altri driver

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si ha un account Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) ora.
- Un account Cosmos. Per istruzioni, vedere [Creare un'app Web usando l'API Azure Cosmos DB per MongoDB e .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Ottenere la stringa di connessione MongoDB usando il metodo di avvio rapido

1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API.
3. Nel riquadro sinistro del pannello dell'account fare clic su **Avvio rapido**.
4. Scegliere la piattaforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Se il driver o lo strumento non compare nell'elenco, ricordare che altri frammenti di codice di connessione vengono continuamente documentati. Commentare di seguito ciò che si vorrebbe vedere. Per informazioni su come realizzare la propria connessione, leggere [Ottenere informazioni sulla stringa di connessione dell'account](#get-the-mongodb-connection-string-to-customize).
5. Copiare e incollare il frammento di codice nell'app MongoDB.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Pannello di avvio rapido":::

## <a name="get-the-mongodb-connection-string-to-customize"></a> Ottenere la stringa di connessione MongoDB da personalizzare

1. In un browser Internet accedere al [portale di Azure](https://portal.azure.com).
2. Nel pannello **Azure Cosmos DB** selezionare l'API.
3. Nel riquadro sinistro del pannello dell'account, fare clic su **Stringa di connessione**.
4. Viene aperto il pannello **Stringa di connessione**. Contiene tutte le informazioni necessarie per connettersi all'account usando un driver per MongoDB, inclusa una stringa di connessione precostruita.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Pannello Stringa di connessione" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Requisiti della stringa di connessione

> [!Important]
> Azure Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite *TLS*.

Azure Cosmos DB supporta il formato URI standard della stringa di connessione MongoDB, con un paio di requisiti specifici: gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite TLS. Il formato della stringa di connessione sarà quindi:

`mongodb://username:password@host:port/[database]?ssl=true`

I valori di questa stringa sono disponibili nel pannello **Stringa di connessione** mostrato in precedenza:

* Username (obbligatorio): nome dell'account Cosmos.
* Password (obbligatoria): password dell'account Cosmos.
* Host (obbligatorio): nome di dominio completo dell'account Cosmos.
* Porta (obbligatorio): 10255.
* Database (facoltativo): il database utilizzato dalla connessione. Se viene specificato alcun database, il database predefinito è "test".
* ssl=true (obbligatorio)

Si consideri l'account mostrato nel pannello **Stringa di connessione**. Una stringa di connessione valida è la seguente:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.
