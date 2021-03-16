---
title: Importare in blocco i dati nell'account dell'API SQL di Azure Cosmos DB con .NET SDK
description: Informazioni su come importare o inserire dati in Azure Cosmos DB creando un'applicazione console .NET che ottimizza le unità elaborate con provisioning (UR/sec) necessarie per importare dati
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563624"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Importare in blocco i dati nell'account dell'API SQL di Azure Cosmos DB con .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In questa esercitazione viene illustrato come creare un'applicazione console .NET per ottimizzare la velocità effettiva con provisioning (UR/sec) necessaria per importare dati in Azure Cosmos DB. In questo articolo i dati verranno letti da un'origine dati di esempio e importati in un contenitore di Azure Cosmos.
Questa esercitazione usa la [versione 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) di Azure Cosmos DB .NET SDK, che può essere destinata a .NET Framework o a .NET Core.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creazione di un account di Azure Cosmos
> * Configurazione del progetto
> * Connessione a un account Azure Cosmos con il supporto dell'esecuzione in blocco abilitato
> * Eseguire un'importazione dati tramite operazioni di creazione simultanee

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare di avere le risorse seguenti:

* Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Per verificare la versione disponibile nell'ambiente corrente, è possibile eseguire `dotnet --version`.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passaggio 1: Creare un account Azure Cosmos DB

[Creare un account dell'API SQL di Azure Cosmos DB](create-cosmosdb-resources-portal.md) dal portale di Azure oppure è possibile creare l'account usando l'[emulatore Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Passaggio 2: configurare il progetto .NET

Aprire il prompt dei comandi di Windows o una finestra del terminale dal computer locale. Tutti i comandi delle sezioni successive dovranno essere eseguiti dal prompt dei comandi o dal terminale. Eseguire il comando dotnet new riportato di seguito per creare una nuova app denominata *bulk-import-demo*. Il parametro `--langVersion` imposta la proprietà *LangVersion* nel file di progetto creato.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

L'output previsto al termine della creazione dovrebbe essere simile al seguente:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Passaggio 3: aggiungere il pacchetto di Azure Cosmos DB

Sempre nella directory dell'applicazione, installare la libreria client di Azure Cosmos DB per .NET Core usando il comando dotnet add package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Passaggio 4: ottenere le credenziali dell'account Azure Cosmos

L'applicazione di esempio deve eseguire l'autenticazione all'account Azure Cosmos. Per eseguire l'autenticazione, è necessario passare le credenziali dell'account Azure Cosmos all'applicazione. Ottenere le credenziali dell'account Azure Cosmos seguendo questa procedura:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
1.  Accedere all'account Azure Cosmos.
1.  Aprire il riquadro **Chiavi** e copiare l'**URI** e la **CHIAVE PRIMARIA** dell'account.

Se si usa l'emulatore Azure Cosmos DB, ottenere le [credenziali dell'emulatore da questo articolo](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Passaggio 5: inizializzare l'oggetto CosmosClient con il supporto dell'esecuzione bulk

Aprire, in un editor del codice, il file `Program.cs` generato. Si creerà una nuova istanza di CosmosClient con l'esecuzione in blocco abilitata e la si userà per eseguire operazioni su Azure Cosmos DB. 

Si inizierà sovrascrivendo il metodo `Main` predefinito e definendo le variabili globali. Queste variabili globali includeranno l'endpoint e le chiavi di autorizzazione, il nome del database, il contenitore da creare e il numero di elementi che verranno inseriti in blocco. Assicurarsi di sostituire i valori endpointURL e AuthorizationKey in base all'ambiente in uso. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

All'interno del metodo `Main` aggiungere il codice seguente per inizializzare l'oggetto CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Dopo l'abilitazione dell'esecuzione in blocco, CosmosClient raggruppa internamente le operazioni simultanee in singole chiamate del servizio. In questo modo, ottimizza l'utilizzo della velocità effettiva distribuendo le chiamate del servizio tra le partizioni e assegnando infine i singoli risultati ai chiamanti originari.

È quindi possibile creare un contenitore per archiviare tutti gli elementi.  Definire `/pk` come chiave di partizione, 50000 UR/sec come velocità effettiva con provisioning e un criterio di indicizzazione personalizzato che escluda tutti i campi per ottimizzare la velocità effettiva in scrittura. Aggiungere il codice seguente dopo l'istruzione di inizializzazione di CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Passaggio 6: popolare un elenco di attività simultanee

Per sfruttare i vantaggi del supporto dell'esecuzione in blocco, creare un elenco di attività asincrone in base all'origine dei dati e alle operazioni che si intende eseguire e usare `Task.WhenAll` per eseguirle contemporaneamente.
Iniziare usando dati "Bogus" per generare un elenco di elementi dal modello di dati. In un'applicazione reale gli elementi provengono dall'origine dati desiderata.

Prima di tutto aggiungere il pacchetto Bogus alla soluzione usando il comando dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Definire la definizione degli elementi che si intende salvare. È necessario definire la classe `Item` all'interno del file `Program.cs`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Creare quindi una funzione helper all'interno della classe `Program`. Questa funzione helper otterrà il numero di elementi definiti per l'inserimento e genererà dati casuali:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Usare la funzione helper per inizializzare un elenco di documenti da usare:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Usare quindi l'elenco di documenti per creare attività simultanee e popolare l'elenco attività per inserire gli elementi nel contenitore. Per eseguire questa operazione, aggiungere il codice seguente alla classe `Program`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Tutte queste operazioni punto simultanee verranno eseguite insieme, ovvero in blocco, come descritto nella sezione introduttiva.

## <a name="step-7-run-the-sample"></a>Passaggio 7: eseguire l'esempio

Per eseguire l'esempio, è sufficiente usare il comando `dotnet`:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Ottenere l'esempio completo

Se non si dispone del tempo necessario per completare i passaggi di questa esercitazione o se si vogliono solo scaricare gli esempi di codice, è possibile ottenerli da [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Dopo aver clonato il progetto, assicurarsi di aggiornare le credenziali desiderate all'interno di [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25).

È possibile eseguire l'esempio passando alla directory del repository e usando `dotnet`:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati eseguiti i passaggi seguenti:

> [!div class="checklist"]
> * Creazione di un account di Azure Cosmos
> * Configurazione del progetto
> * Connessione a un account Azure Cosmos con il supporto dell'esecuzione in blocco abilitato
> * Eseguire un'importazione dati tramite operazioni di creazione simultanee

È ora possibile passare all'esercitazione successiva:

> [!div class="nextstepaction"]
>[Eseguire query in Azure Cosmos DB con l'API SQL](tutorial-query-sql-api.md)