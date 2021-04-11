---
title: Risolvere i problemi Azure Cosmos DB le eccezioni di richiesta non valida
description: Informazioni su come diagnosticare e correggere le eccezioni di una richiesta non valida, ad esempio il contenuto di input o la chiave di partizione, non corrisponde alla chiave di partizione in Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556125"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnosticare e risolvere i problemi relativi alle eccezioni delle richieste non valide in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Il codice di stato HTTP 400 indica che la richiesta contiene dati non validi o che mancano i parametri obbligatori.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Manca la proprietà ID
In questo scenario è comune vedere l'errore:

*Il contenuto di input non è valido perché le proprietà obbligatorie-' ID; '-mancano*

Una risposta con questo errore indica che il documento JSON inviato al servizio non dispone della proprietà ID richiesta.

### <a name="solution"></a>Soluzione
Specificare una `id` proprietà con un valore stringa in base alla [specifica Rest](/rest/api/cosmos-db/documents) come parte del documento, gli SDK non generano automaticamente i valori per questa proprietà.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Tipo di chiave di partizione non valido
In questo scenario è frequente vedere errori come:

*Chiave di partizione... non è valido.*

Una risposta con questo errore indica che il valore della chiave di partizione è di un tipo non valido.

### <a name="solution"></a>Soluzione
Il valore della chiave di partizione deve essere una stringa o un numero, verificare che il valore sia dei tipi previsti.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Valore della chiave di partizione errato
In questo scenario è comune vedere l'errore:

*Il PartitionKey Estratto dal documento non corrisponde a quello specificato nell'intestazione*

Una risposta con questo errore indica che si sta eseguendo un'operazione e si passa un valore della chiave di partizione che non corrisponde al valore del corpo del documento per la proprietà prevista. Se il percorso della chiave di partizione della raccolta è `/myPartitionKey` , il documento dispone di una proprietà denominata `myPartitionKey` con un valore che non corrisponde a quanto specificato come valore della chiave di partizione durante la chiamata al metodo SDK.

### <a name="solution"></a>Soluzione
Inviare il parametro del valore della chiave di partizione che corrisponde al valore della proprietà del documento.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticare e risolvere](troubleshoot-java-sdk-v4-sql.md) i problemi quando si usa il Azure Cosmos DB Java v4 SDK.
* Informazioni sulle linee guida sulle prestazioni per [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
