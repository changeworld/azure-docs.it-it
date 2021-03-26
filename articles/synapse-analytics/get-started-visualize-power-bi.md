---
title: "Esercitazione: Introduzione ad Azure Synapse Analytics - Visualizzare i dati dell'area di lavoro con Power BI"
description: In questa esercitazione viene illustrato come creare un'area di lavoro di Power BI, collegare l'area di lavoro Azure Synapse e creare un set di dati Power BI che usa i dati dell'area di lavoro Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 03/25/2021
ms.openlocfilehash: 829daaa9c739657528a9085201c61c88635931d0
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608443"
---
# <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI

In questa esercitazione viene illustrato come creare un'area di lavoro di Power BI, collegare l'area di lavoro Azure Synapse e creare un set di dati Power BI che usa i dati dell'area di lavoro Azure Synapse. 

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, [installare Power bi desktop](https://aka.ms/pbidesktopstore).

## <a name="overview"></a>Panoramica

A partire dai dati di NYC Taxi, sono stati creati set di dati aggregati in due tabelle:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

È possibile collegare un'area di lavoro di Power BI all'area di lavoro di Azure Synapse, in modo da visualizzare facilmente i dati nell'area di lavoro di Power BI. È possibile modificare i report di Power BI direttamente nell'area di lavoro di Azure Synapse. 

### <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

1. Accedere a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Fare clic su **aree di lavoro**, quindi selezionare **Crea un'area di lavoro**. Creare una nuova area di lavoro Power BI denominata **NYCTaxiWorkspace1** o simile, perché questo nome deve essere univoco.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Collegare l'area di lavoro di Azure Synapse alla nuova area di lavoro di Power BI

1. In Synapse Studio passare a **Gestisci** > **Servizi collegati**.
1. Selezionare **nuovo**  >  **Connetti a Power bi**.
1. Impostare **nome** su **NYCTaxiWorkspace1**.
1. Impostare **nome area di lavoro** sull'area di lavoro Power BI creata in precedenza, simile a **NYCTaxiWorkspace1**.
1. Selezionare **Crea**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Creare un set di dati di Power BI che usa i dati dell'area di lavoro di Azure Synapse

1. In Synapse Studio passare a **Develop** > **Power BI** (Sviluppo > Power BI).
1. Passare a **NYCTaxiWorkspace1** > **Set di dati di Power BI** e selezionare **New Power BI dataset** (Nuovo set di dati di Power BI). Fare clic su **Start** (Avvia).
1. Selezionare l'origine dati **SQLPOOL1** , quindi fare clic su **continua**.
1. Fare clic su **download** per scaricare il file con estensione PbiDS per il file **NYCTaxiWorkspace1SQLPOOL1. PbiDS** . Fare clic su **Continue**.
1. Aprire il file **PBIDS** scaricato. Power BI Desktop si apre e si connette automaticamente a **SQLDB1** nell'area di lavoro sinapsi di Azure.
1. Se viene visualizzata una finestra di dialogo denominata **SQL Server database**:
    1. Selezionare l'**account Microsoft**.
    1. Selezionare **Accedi** e accedere al proprio account.
    1. Selezionare **Connetti**.
1. All'apertura della finestra di dialogo **Navigator**, selezionare la tabella **PassengerCountStats** e quindi **Carica**.
1. Nella finestra di dialogo **Impostazioni di connessione** selezionare **DirectQuery** > **OK**.
1. Fare clic sul pulsante **Report** a sinistra.
1. In **visualizzazioni** fare clic sull'icona del grafico a linee per aggiungere un **grafico a linee** al report.
    1. In **campi** trascinare la colonna **PassengerCount** in asse **visualizzazioni**  >  .
    1. Trascinare le colonne **SumTripDistance** e **AvgTripDistance** in **Visualizzazioni** > **Valori**.
1. Nella scheda **Home** selezionare **Pubblica**.
1. Fare clic su **Salva** per salvare le modifiche.
1. Scegliere il nome file **PassengerAnalysis.pbix**, quindi selezionare **Salva**.
1. Nella finestra **pubblica in Power bi** , in **selezionare una destinazione**, scegliere il **NYCTaxiWorkspace1**, quindi fare clic su **Seleziona**.
1. Attendere il completamento della pubblicazione. 

### <a name="configure-authentication-for-your-dataset"></a>Configurare l'autenticazione per il set di dati

1. Aprire [powerbi.microsoft.com](https://powerbi.microsoft.com/) e fare clic su **Accedi**.
1. Sul lato sinistro, in **Aree di lavoro** selezionare l'area di lavoro **NYCTaxiWorkspace1**.
1. All'interno dell'area di lavoro individuare un set di dati denominato **Passenger Analysis** e un report denominato **Passenger Analysis**.
1. Passare il puntatore del mouse sul set di dati **PassengerAnalysis**, selezionare il pulsante con i puntini di sospensione (...) e quindi selezionare **Impostazioni**.
1. In **credenziali origine dati** fare clic su **modifica**, impostare il **metodo di autenticazione** su **OAuth2**, quindi selezionare **Accedi**.

### <a name="edit-a-report-in-synapse-studio"></a>Modificare un report in Synapse Studio

1. Tornare a Synapse Studio e selezionare **Close and refresh** (Chiudi e aggiorna).
1. Passare all'hub **Develop** (Sviluppo).
1. A destra del pulsante **Power bi** livello, puntini di sospensione (...) e fare clic su **Aggiorna** per aggiornare il nodo **report Power bi** .
1. In **Power BI** sono ora visibili gli elementi seguenti:
    * In **NYCTaxiWorkspace1**  >  **Power bi SET** di dati, un nuovo set di dati denominato **PassengerAnalysis**.
    * In **NYCTaxiWorkspace1** > **Report di Power BI**, un nuovo report denominato **PassengerAnalysis**.
1. Selezionare il report **PassengerAnalysis**. Il report verrà aperto e sarà possibile modificarlo direttamente all'interno di Synapse Studio.



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitoraggio](get-started-monitor.md)
                                 

