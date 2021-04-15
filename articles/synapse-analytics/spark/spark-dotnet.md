---
title: Usare .NET per Apache Spark
description: Informazioni sull'uso di .NET e Apache Spark per l'elaborazione batch, lo streaming in tempo reale, l'apprendimento automatico e la scrittura di query ad hoc in notebook di Azure Synapse Analytics.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378551"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Usare .NET per Apache Spark con Azure Synapse Analytics

[.NET per Apache Spark](https://dot.net/spark) offre supporto .NET gratuito, [open source](https://github.com/dotnet/spark)e multipiattaforma per Spark. 

Fornisce associazioni .NET per Spark, che consentono di accedere alle API Spark tramite C# e F#. Con .NET per Apache Spark, è anche possibile scrivere ed eseguire funzioni definite dall'utente per Spark scritte in .NET. Con le API .NET per Spark è possibile accedere a tutti gli aspetti dei dataframe Spark che consentono di analizzare i dati, tra cui Spark SQL, Delta Lake e Structured Streaming.

Per analizzare i dati con .NET per Apache Spark, è possibile usare definizioni di processo batch Spark o notebook interattivi di Azure Synapse Analytics. Questo articolo illustra come usare .NET per Apache Spark con Azure Synapse in entrambi i modi.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Inviare processi batch con la definizione di processo Spark

Vedere l'esercitazione per informazioni su come usare Azure Synapse Analytics per [creare definizioni di processi Apache Spark per i pool di Synapse Spark](apache-spark-job-definitions.md). Se l'app non è stata in pacchetto per l'invio Azure Synapse, completare la procedura seguente.

1. Eseguire i comandi seguenti per pubblicare l'app. Assicurarsi di sostituire *mySparkApp* con il percorso dell'app.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Comprimere il contenuto della cartella publish, ad `publish.zip` esempio, creato come risultato del passaggio 1. Tutti gli assembly devono essere nel primo livello del file ZIP e non deve essere presente alcun livello cartella intermedio. Ciò significa che quando si decomprime `publish.zip` , tutti gli assembly vengono estratti nella directory di lavoro corrente.

    **In Windows:**

    Usare un programma di estrazione, ad esempio [7-Zip](https://www.7-zip.org/) o [WinZip,](https://www.winzip.com/)per estrarre il file nella directory bin con tutti i file binari pubblicati.

    **In Linux:**

    Aprire una shell Bash e cd nella directory bin con tutti i file binari pubblicati ed eseguire il comando seguente.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET per Apache Spark in notebook di Azure Synapse Analytics 

I notebook costituiscono un valida opzione per la realizzazione di prototipi di pipeline e scenari di .NET per Apache Spark. È possibile iniziare a usare, comprendere, filtrare e visualizzare i dati in modo rapido ed efficiente. 

Data engineer, data scientist, business analyst e machine learning engineer possono collaborare lavorando tutti a un documento condiviso e interattivo. È possibile accedere ai risultati immediati dall'esplorazione dei dati e visualizzare i dati nello stesso notebook.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Come usare .NET per i notebook Apache Spark

Quando si crea un nuovo notebook, è necessario scegliere un kernel del linguaggio con cui esprimere la logica di business. Il supporto del kernel è disponibile per diversi linguaggi, tra cui C#.

Per usare .NET per Apache Spark nel notebook di Azure Synapse Analytics, selezionare **.NET Spark (C#)** come kernel e collegare il notebook a un pool di Apache Spark serverless esistente.

Il notebook spark .NET si basa sulle esperienze interattive [.NET](https://github.com/dotnet/interactive) e offre esperienze C# interattive con la possibilità di usare .NET per Spark predefinito con la variabile di sessione Spark già `spark` predefinita.

### <a name="install-nuget-packages-in-notebooks"></a>Installare pacchetti NuGet nei notebook

È possibile installare i pacchetti NuGet di propria scelta nel notebook usando il comando magic prima `#r nuget` del nome del pacchetto NuGet. Il diagramma seguente mostra un esempio:

![Screenshot che mostra l'uso #r per installare un pacchetto NuGet per notebook Spark .NET](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Per altre informazioni su come usare i pacchetti NuGet nei notebook, vedere [la documentazione interattiva di .NET.](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)

### <a name="net-for-apache-spark-c-kernel-features"></a>Funzionalità del kernel C# di .NET per Apache Spark

Le funzionalità seguenti sono disponibili quando si usa .NET per Apache Spark nel notebook di Azure Synapse Analytics:

* HTML dichiarativo: genera l'output dalle celle usando la sintassi HTML, ad esempio intestazioni, elenchi puntati e persino visualizzazione di immagini.
* Istruzioni C# semplici, ad esempio assegnazioni, stampa alla console, generazione di eccezioni e così via.
* Blocchi di codice C# su più righe, ad esempio istruzioni if, cicli foreach, definizioni di classi e così via.
* Accesso alla libreria standard C#, ad esempio System, LINQ, tipi enumerabili e così via.
* Supporto per le funzionalità del linguaggio C# 8.0.
* `spark` come variabile predefinita per fornire l'accesso alla sessione Apache Spark predefinita.
* Supporto per la definizione di [funzioni definite dall'utente .NET che possono essere eseguite all'interno di Apache Spark](/dotnet/spark/how-to-guides/udf-guide). È [consigliabile scrivere e](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) chiamare funzioni definita dall'utente in .NET per Apache Spark ambienti interattivi per imparare a usare funzioni definita dall'utente in .NET per Apache Spark interattive.
* Supporto per la visualizzazione dell'output dei processi Spark con grafici (ad esempio a linee, a barre o istogrammi) e layout (ad esempio singoli, sovrapposti e così via) diversi usando la libreria `XPlot.Plotly`.
* Possibilità di includere i pacchetti NuGet nel notebook C#.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di .NET per Apache Spark](/dotnet/spark/)
* [.NET per Apache Spark guide interattive](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
