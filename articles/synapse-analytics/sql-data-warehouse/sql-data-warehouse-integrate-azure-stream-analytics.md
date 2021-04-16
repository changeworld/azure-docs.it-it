---
title: Usare Analisi di flusso di Azure nel pool SQL dedicato
description: Suggerimenti per l'Analisi di flusso di Azure con pool SQL dedicato in Azure Synapse per lo sviluppo di soluzioni in tempo reale.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566580"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Usare Analisi di flusso di Azure con pool SQL dedicato in Azure Synapse Analytics

Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l’esercitazione [Introduzione all’uso di Analisi di flusso di Azure](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

In questo articolo si apprenderà come usare il pool SQL dedicato come sink di output per l'inserimento di dati ad alta velocità effettiva con Analisi di flusso di Azure processi.

## <a name="prerequisites"></a>Prerequisiti

* Analisi di flusso di Azure processo: per creare un processo Analisi di flusso di Azure, seguire i passaggi nell'esercitazione Introduzione all'uso Analisi di flusso di Azure [per:](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  

    1. Creare un input dell'hub eventi
    2. Configurare e avviare l'applicazione di generazione di eventi
    3. Eseguire il provisioning di un processo di Analisi dei flussi
    4. Specificare la query e l'input del processo
* Pool SQL dedicato: per creare un nuovo pool SQL dedicato, seguire la procedura descritta in Avvio [rapido: Creare un pool SQL dedicato.](../quickstart-create-sql-pool-portal.md)

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Specificare l'output di streaming in modo che punti al pool SQL dedicato

### <a name="step-1"></a>Passaggio 1

Dal portale di Azure passare al processo di Analisi di flusso e fare clic su **Output nel** menu **Topologia processo.**

### <a name="step-2"></a>Passaggio 2

Fare clic sul **pulsante** Aggiungi e scegliere **Azure Synapse Analytics** dal menu a discesa.

![Scegliere Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Passaggio 3

Immettere i valori seguenti:

* *Alias di output:* immettere un nome descrittivo per l'output del processo.
* *Sottoscrizione*:
  * Se il pool SQL dedicato si trova nella stessa sottoscrizione del processo di Analisi di flusso, fare clic su Seleziona Azure Synapse Analytics ***dalle sottoscrizioni.***
  * Se il pool SQL dedicato si trova in una sottoscrizione diversa, fare clic su Fornisci impostazioni Azure Synapse Analytics manualmente.
* *Database:* selezionare il database di destinazione dall'elenco a discesa.
* *Nome utente*: digitare il nome utente di un account con autorizzazioni di scrittura nel database.
* *Password*: fornire la password per l'account utente specificato.
* *Tabella*: specificare il nome della tabella di destinazione nel database.
* Fare clic sul **pulsante** Salva

![Modulo Azure Synapse Analytics completato](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Passaggio 4

Prima di poter eseguire un test, è necessario creare la tabella nel pool SQL dedicato.  Eseguire lo script di creazione della tabella seguente usando SQL Server Management Studio (SSMS) o lo strumento di query scelto.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Passaggio 5

Nella pagina portale di Azure processo di Analisi di flusso fare clic sul nome del processo.  Fare clic sul pulsante ***Test** _ nel riquadro _ *_Dettagli output_**.

![Pulsante Test nei dettagli outpout Quando la connessione al database ha esito positivo, verrà ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) visualizzata una notifica nel portale.

### <a name="step-6"></a>Passaggio 6

Fare clic sul menu ***Query** _ in _*_Topologia processo e_*_ modificare la query per inserire i dati nell'output del flusso creato.  Fare clic sul _*_pulsante Test query_*_ selezionata per testare la query.  Fare clic sul pulsante _ *_Salva query_** quando il test della query ha esito positivo.

![Salvare la query](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Passaggio 7

Avviare il Analisi di flusso di Azure processo.  Fare clic sul pulsante ***Start** _ nel menu _ *_Panoramica_**.

![Avviare il processo di Analisi di flusso](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Fare clic ***sul pulsante*** Avvia nel riquadro avvia processo.

![Fare clic su Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dell'integrazione, vedere [Integrare altri servizi.](sql-data-warehouse-overview-integrate.md)
Per altri suggerimenti sullo sviluppo, vedere Decisioni di progettazione e [tecniche di codifica per il pool SQL dedicato.](sql-data-warehouse-overview-develop.md)
