---
title: Aggiornamento di .NET per Apache Spark alla versione v 1.0 in HDI
description: Informazioni sull'aggiornamento di .NET per la versione di Apache Spark a 1,0 in HDI e sul modo in cui influiscono sul codice e sui cluster esistenti.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: 4b95e4f164eb2c1d0a881cf9ab14696c3cbed4a7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122444"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Aggiornamento di .NET per Apache Spark alla versione v 1.0 in HDInsight

Questo documento illustra la prima versione principale di [.NET per Apache Spark](https://github.com/dotnet/spark)e come può influito sulle pipeline di produzione correnti nei cluster HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>Informazioni su .NET per Apache Spark versione 1.0.0

Questa è la prima [versione ufficiale principale](https://github.com/dotnet/spark/releases/tag/v1.0.0) di .net per Apache Spark e fornisce la completezza dell'API dataframe per Spark 2.4. x, oltre a Spark 3.0. x insieme ad altre funzionalità. Per un elenco completo di tutte le funzionalità, miglioramenti e correzioni di bug, vedere le [Note sulla versione ufficiale v 1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Un altro aspetto importante da notare è che questa versione **non** è compatibile con le versioni precedenti di `Microsoft.Spark` e `Microsoft.Spark.Worker` . Consultare la [Guida alla migrazione](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) se si prevede di aggiornare .net per Apache Spark applicazione in modo che sia compatibile con la versione 1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Uso di .NET per Apache Spark v 1.0 in HDInsight

Sebbene i cluster HDI correnti non saranno interessati (ad esempio, avranno ancora la stessa versione di prima), i cluster HDI appena creati porteranno a questa versione v 1.0.0 più recente di .NET per Apache Spark. Ciò significa che se:

- **Si dispone di un cluster HDI precedente**: se si vuole aggiornare l'applicazione Spark .NET a v 1.0.0 (scelta consigliata), sarà necessario aggiornare la `Microsoft.Spark.Worker` versione nel cluster HDI. Per altre informazioni, vedere la [sezione Changing versions of .NET for Apache Spark on HDI cluster](#changing-net-for-apache-spark-version-on-hdinsight).
Se non si vuole aggiornare la versione corrente di .NET per Apache Spark nell'applicazione, non è necessario eseguire ulteriori passaggi.  

- **Si dispone di un nuovo cluster HDI**: se si vuole aggiornare l'applicazione Spark .NET alla versione 1.0.0 (scelta consigliata), non sono necessari passaggi per modificare il ruolo di lavoro in HDI. Tuttavia, sarà necessario fare riferimento alla [Guida alla migrazione](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) per comprendere i passaggi necessari per aggiornare il codice e le pipeline.
Se non si vuole modificare la versione corrente di .NET per Apache Spark nell'applicazione, è necessario modificare la versione nel cluster HDI dalla versione 1.0 (impostazione predefinita nei nuovi cluster) a qualsiasi versione in uso. Per altre informazioni, vedere la [sezione Changing versions of .NET for Apache Spark on HDI cluster](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Modifica di .NET per la versione Apache Spark in HDInsight

### <a name="deploy-microsoftsparkworker"></a>Distribuire Microsoft.Spark.Worker

`Microsoft.Spark.Worker` è un componente back-end che risiede nei singoli nodi del ruolo di lavoro del cluster Spark. Quando si vuole eseguire una funzione definita dall'utente (UDF) C#, Spark deve comprendere come avviare CLR .NET per eseguire questa funzione definita dall'utente. `Microsoft.Spark.Worker` fornisce una raccolta di classi per Spark che consentono di abilitare questa funzionalità. Selezionare la versione del ruolo di lavoro a seconda della versione di .NET per Apache Spark che si desidera distribuire nel cluster HDI.

1. Scaricare la versione di Microsoft. Spark. Worker Linux della versione specifica. Se ad esempio si vuole `.NET for Apache Spark v1.0.0` , scaricare [Microsoft. Spark. Worker. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Scaricare lo script [Install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) per installare i file binari del ruolo di lavoro scaricati nel passaggio 1 in tutti i nodi del ruolo di lavoro del cluster HDI.  

3. Caricare i file menzionati in precedenza nell'account di archiviazione di Azure a cui il cluster può accedere. Per altri dettagli, vedere [l'articolo relativo alla distribuzione di .NET per Apache Spark HDI](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) .

4. Eseguire lo `install-worker.sh` script in tutti i nodi del ruolo di lavoro del cluster, usando le azioni script. Per ulteriori informazioni, vedere [l'articolo relativo alla distribuzione di .NET per Apache Spark HDI](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) .

### <a name="update-your-application-to-use-specific-version"></a>Aggiornare l'applicazione per usare una versione specifica

È possibile aggiornare .NET per Apache Spark applicazione per usare una versione specifica scegliendo la versione richiesta del [pacchetto NuGet Microsoft. Spark](https://www.nuget.org/packages/Microsoft.Spark/) nel progetto. Vedere le note sulla versione della versione specifica e la [Guida alla migrazione](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) come indicato in precedenza, se si sceglie di aggiornare l'applicazione a v 1.0.0.

## <a name="faqs"></a>Domande frequenti

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Il cluster HDI esistente con versione < 1.0.0 inizia a non riuscire con la nuova versione?

I cluster HDI esistenti continueranno a avere la stessa versione precedente per .NET per Apache Spark e l'applicazione esistente (con la versione precedente di Spark .NET) non sarà interessata.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire .NET per Apache Spark applicazione in HDInsight](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment)
