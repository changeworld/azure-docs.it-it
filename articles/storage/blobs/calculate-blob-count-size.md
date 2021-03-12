---
title: Calcolare il numero e le dimensioni del BLOB usando l'inventario di archiviazione di Azure
description: Informazioni su come calcolare il numero e le dimensioni totali dei BLOB per ogni contenitore.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102638166"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Calcolare il numero di BLOB e le dimensioni totali per ogni contenitore usando l'inventario di archiviazione di Azure

Questo articolo usa la funzionalità di inventario di archiviazione BLOB di Azure e le sinapsi di Azure per calcolare il numero di BLOB e le dimensioni totali dei BLOB per ogni contenitore. Questi valori sono utili per l'ottimizzazione dell'utilizzo di BLOB per ogni contenitore.

I metadati del BLOB non sono inclusi in questo metodo. La funzionalità di inventario dell'archiviazione BLOB di Azure usa l'API REST di [elenco BLOB](/rest/api/storageservices/list-blobs) con parametri predefiniti. L'esempio non supporta quindi gli snapshot, i contenitori ' $' e così via.

> [!IMPORTANT]
> L'inventario BLOB è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali applicabili alle funzionalità di Azure disponibili in versione beta, di anteprima o non ancora rilasciate a livello generale.

## <a name="enable-inventory-reports"></a>Abilita report inventario

Il primo passaggio di questo metodo consiste nell' [abilitare i report di inventario](blob-inventory.md#enable-inventory-reports) nell'account di archiviazione. Potrebbe essere necessario attendere fino a 24 ore dopo l'abilitazione dei report di inventario per il primo report da generare.

Quando si dispone di un report di inventario da analizzare, concedere a se stessi l'accesso in lettura BLOB al contenitore in cui risiede il file CSV del report.

1. Passare al contenitore con il file di rapporto inventario CSV.
1. Selezionare **controllo di accesso (IAM)**, quindi **aggiungere assegnazioni di ruolo**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Selezionare Aggiungi assegnazioni di ruolo":::

1. Nell'elenco a discesa **ruolo** selezionare **lettore dati BLOB di archiviazione** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Aggiungere il ruolo lettore dati BLOB di archiviazione dall'elenco a discesa":::

1. Immettere l'indirizzo di posta elettronica dell'account che si sta usando per eseguire il report nel campo **Seleziona** .

## <a name="create-an-azure-synapse-workspace"></a>Creare un'area di lavoro di Azure Synapse

Successivamente, [creare un'area di lavoro di Azure sinapsi](/azure/synapse-analytics/get-started-create-workspace) in cui verrà eseguita una query SQL per segnalare i risultati dell'inventario.

## <a name="create-the-sql-query"></a>Creare la query SQL

Dopo aver creato l'area di lavoro di Azure sinapsi, seguire questa procedura.

1. Passare a [https://web.azuresynapse.net](https://web.azuresynapse.net).
1. Selezionare la scheda **sviluppo** sul lato sinistro.
1. Selezionare il segno più grande (+) per aggiungere un elemento.
1. Selezionare **script SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Selezionare lo script SQL per creare una nuova query":::

## <a name="run-the-sql-query"></a>Eseguire la query SQL

1. Aggiungere la query SQL seguente nell'area di lavoro di Azure sinapsi per [leggere il file di inventario CSV](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file).

    Per il `bulk` parametro, utilizzare l'URL del file CSV del report di inventario che si desidera analizzare.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Assegnare un nome alla query SQL nel riquadro proprietà a destra.

1. Pubblicare la query SQL premendo CTRL + S o selezionando il pulsante **pubblica tutti** .

1. Selezionare il pulsante **Esegui** per eseguire la query SQL. Il numero di BLOB e le dimensioni totali per ogni contenitore sono riportati nel riquadro **risultati** .

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Output dell'esecuzione dello script per calcolare il numero di BLOB e le dimensioni totali.":::

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'inventario BLOB di archiviazione di Azure per gestire i dati BLOB](blob-inventory.md)
- [Calcolare le dimensioni di fatturazione totali di un contenitore BLOB](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
