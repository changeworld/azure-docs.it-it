---
title: Abilitare i notebook nell'account Azure Cosmos DB (anteprima)
description: I notebook predefiniti di Azure Cosmos DB consentono di analizzare e visualizzare i dati all'interno del portale. Questo articolo descrive come abilitare questa funzionalità per gli account Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692777"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Abilitare i notebook per gli account Azure Cosmos DB (anteprima)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> I notebook predefiniti per Azure Cosmos DB sono attualmente disponibili in [29 aree](#supported-regions). Per usare i notebook, [creare un nuovo account Cosmos](#create-a-new-cosmos-account) o [abilitare i notebook in un account esistente](#enable-notebooks-in-an-existing-cosmos-account) in una di queste aree. 

I notebook Jupyter predefiniti in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure. Questo articolo descrive come abilitare questa funzionalità per l'account Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Crea un nuovo account Cosmos
A partire dal 10 febbraio 2021, per i nuovi account Azure Cosmos creati in una delle [aree supportate](#supported-regions) verranno automaticamente abilitati i notebook. Non è necessaria alcuna configurazione aggiuntiva per abilitare i notebook. Usare le istruzioni seguenti per creare un nuovo account:
1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
1. Immettere le impostazioni di base per l'account.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Pagina del nuovo account per Azure Cosmos DB":::

1. Selezionare **Rivedi e crea**. È possibile ignorare l'opzione **rete** e **tag** . 
1. Esaminare le impostazioni dell'account e quindi selezionare **Crea**. La creazione dell'account richiede alcuni minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Riquadro Notifiche del portale di Azure":::

1. Selezionare **Vai alla risorsa** per passare alla pagina dell'account Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Pagina dell'account Azure Cosmos DB":::

1. Passare al riquadro **Esplora dati** . Verrà visualizzata l'area di lavoro dei notebook.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nuova area di lavoro Azure Cosmos DB notebook":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Abilitare i notebook in un account Cosmos esistente

È anche possibile abilitare notebook per gli account esistenti. Questo passaggio deve essere eseguito solo una volta per ogni account.

1. Passare al riquadro **Esplora dati** nell'account Cosmos.
1. Selezionare **Abilita notebook**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Creare una nuova area di lavoro notebook in Esplora dati":::

1. Verrà richiesto di creare una nuova area di lavoro dei notebook. Selezionare **installazione completa.**
1. L'account è ora abilitato per l'uso dei notebook.

## <a name="create-and-run-your-first-notebook"></a>Creare ed eseguire il primo notebook

Per verificare che sia possibile usare i notebook, selezionare uno dei notebook in notebook di esempio. Verrà salvata una copia del notebook nell'area di lavoro e la si aprirà.

In questo esempio si userà **GettingStarted. ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Visualizzare il notebook GettingStarted. ipynb":::

Per eseguire il notebook:
1. Selezionare la prima cella di codice che contiene il codice Python.
1. Selezionare **Esegui** per eseguire la cella. È anche possibile usare **MAIUSC + INVIO** per eseguire la cella.
1. Aggiornare il riquadro delle risorse per visualizzare il database e il contenitore che è stato creato.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Esegui notebook introduttivo":::

È anche possibile selezionare **nuovo notebook** per creare un nuovo notebook o caricare un file Notebook (con estensione ipynb) esistente selezionando **Carica file** dal menu **notebook personali** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Creare o caricare un nuovo notebook":::

## <a name="supported-regions"></a>Aree supportate
I notebook predefiniti per Azure Cosmos DB sono attualmente disponibili in 29 aree di Azure. Ai nuovi account Azure Cosmos creati in queste aree verranno automaticamente abilitati i notebook. I notebook sono gratuiti con l'account. 

- Australia centrale
- Australia centrale 2
- Australia orientale
- Australia sud-orientale
- Brasile meridionale
- Canada centrale
- Canada orientale
- India centrale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Francia centrale
- Francia meridionale
- Germania settentrionale
- Germania centro-occidentale
- Giappone occidentale
- Corea meridionale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Svizzera settentrionale
- Emirati Arabi Uniti centrali
- Regno Unito meridionale
- Regno Unito occidentale
- Stati Uniti centro-occidentali
- Europa occidentale
- India occidentale
- Stati Uniti occidentali 2

## <a name="next-steps"></a>Passaggi successivi

* Scopri i vantaggi di [Azure Cosmos DB notebook Jupyter](cosmosdb-jupyter-notebooks.md)
* [Esplora la raccolta di esempi di notebook](https://cosmos.azure.com/gallery.html)
* [Pubblicare notebook nella raccolta Azure Cosmos DB notebook](publish-notebook-gallery.md)
* [Usare funzionalità e comandi del notebook Python](use-python-notebook-features-and-commands.md)
* [Usare funzionalità e comandi dei notebook C#](use-csharp-notebook-features-and-commands.md)
* [Importare notebook da un repository GitHub](import-github-notebooks.md)
