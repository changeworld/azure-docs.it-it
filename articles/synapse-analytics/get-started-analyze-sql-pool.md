---
title: "Esercitazione: Introduzione all'analisi dei dati con pool SQL dedicati"
description: In questa esercitazione si useranno i dati di esempio NYC Taxi per esplorare le funzionalità di analisi del pool SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 267dc7c7d89bbecfbed127f4a46adb7cd9044bc4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309374"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analizzare i dati con pool SQL dedicati

In questa esercitazione si useranno i dati dei taxi di New York per esplorare le funzionalità di un pool SQL dedicato.

## <a name="create-a-dedicated-sql-pool"></a>Creare un pool SQL dedicato

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool SQL**.
1. Selezionare **Nuovo**
1. Per **Nome del pool SQL**, selezionare **SQLPOOL1**
1. Per **Livello di prestazioni**, scegliere **DW100C**
1. Selezionare **Rivedi e crea** > **Crea**. Il pool SQL dedicato sarà pronto in pochi minuti. 

Il pool SQL dedicato è associato a un database SQL chiamato anche **SQLPOOL1**.
1. Passare all'   >  **area di lavoro** dati.
1. Verrà visualizzato un database denominato **SQLPOOL1**. Se non viene visualizzato, fare clic su **Aggiorna**.

Un pool SQL dedicato utilizza risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

> [!NOTE] 
> Quando si crea un nuovo pool SQL dedicato (in precedenza SQL Data Warehouse) nell'area di lavoro, verrà aperta la pagina per effettuarne il provisioning. Il provisioning verrà effettuato nel server SQL logico.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Caricare i dati dei taxi di New York in SQLPOOL1

1. In sinapsi Studio passare all'hub **sviluppo** , fare clic sul **+** pulsante per aggiungere una nuova risorsa, quindi creare un nuovo script SQL.
1. Selezionare il pool "SQLPOOL1" (pool creato nel [passaggio 1](./get-started-create-workspace.md) di questa esercitazione) nell'elenco a discesa "Connetti a" sopra lo script.
1. Immettere il codice seguente:
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. Per eseguire lo script, fare clic sul pulsante Esegui.
1. Questo script verrà completato in meno di 60 secondi. Carica 2 milioni righe di dati di NYC Taxi in una tabella denominata **dbo. Viaggio**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Esplorare i dati dei taxi di New York nel pool SQL dedicato

1. In Synapse Studio passare all'hub **Data** (Dati).
1. Passare a **SQLPOOL1** > **Tabelle**. 
3. Fare clic con il pulsante destro del mouse su **dbo. Tabella NYCTaxiTripSmall** e selezionare **nuovo script SQL**  >  **selezionare le prime 100 righe**.
4. Attendere mentre viene creato ed eseguito un nuovo script SQL.
5. Si noti che nella parte superiore dello script SQL la sezione **Connetti a** è impostata automaticamente sul pool SQL denominato **SQLPOOL1**.
6. Sostituire il testo dello script SQL con il codice ed eseguirlo.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Questa query mostra come le distanze totali e la distanza media delle corse sono correlate al numero di passeggeri.
1. Nella finestra dei risultati dello script SQL modificare la **Vista** impostandola su **Grafico** per visualizzare i risultati come un grafico a linee.
    
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analizzare i dati in un account di archiviazione di Azure](get-started-analyze-storage.md)
