---
title: Introduzione alla verifica del flusso di dati in Azure Data Factory
description: Esercitazione su come preparare i dati in Azure Data Factory utilizzando il flusso di dati in corso
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f32739b06920f6b20dc87b8e1fbd2884c323a859
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634129"
---
# <a name="prepare-data-with-data-wrangling"></a>Preparare i dati con data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I dati in data factory consentono di creare mash-up interattivi Power Query in modo nativo in ADF e quindi di eseguirli su larga scala all'interno di una pipeline di ADF.

> [!NOTE]
> Power Query all'attività in ADF è attualmente avilable in versione di anteprima pubblica

## <a name="create-a-power-query-activity"></a>Creare un'attività di Power Query

Esistono due modi per creare un Power Query in Azure Data Factory. Un modo consiste nel fare clic sull'icona con il segno più e selezionare **flusso di dati** nel riquadro risorse Factory.

> [!NOTE]
> In precedenza, la funzionalità data wrangling si trovava nel flusso di lavoro flusso di dati. A questo punto, si creerà il data wrangling mash-up da ```New > Power query```

![Screenshot che mostra Power Query nel riquadro risorse Factory.](media/data-flow/power-query-wrangling.png)

L'altro metodo si trova nel riquadro attività dell'area di disegno della pipeline. Aprire la **Power query** Accordion e trascinare l'attività **Power query** sull'area di disegno.

![Screenshot che evidenzia l'opzione del flusso di dati in corso.](media/data-flow/power-query-activity.png)

## <a name="author-a-wrangling-data-flow"></a>Creazione di un flusso di dati in corso

Aggiungere un **set di dati di origine** per il Power query mash-up. È possibile scegliere un set di dati esistente o crearne uno nuovo. È anche possibile selezionare un set di dati sink. È possibile scegliere uno o più set di dati di origine, ma al momento è consentito un solo sink. La scelta di un set di dati sink è facoltativa, ma è necessario almeno un set di dati di origine.

![Dispute](media/wrangling-data-flow/tutorial4.png)

Fare clic su **Crea** per aprire la Power query editor mashup online.

![Screenshot che mostra il pulsante Crea che consente di aprire il Power Query Editor mashup online.](media/wrangling-data-flow/tutorial5.png)

Consente di creare il flusso di dati che si verifica usando la preparazione dei dati senza codice. Per l'elenco delle funzioni disponibili, vedere [funzioni di trasformazione](wrangling-functions.md). ADF converte lo ```M``` script in uno script del flusso di dati in modo che sia possibile eseguire il Power query su larga scala usando l'ambiente Spark del flusso di dati di ADF.

![Screenshot che illustra il processo per la creazione del flusso di dati in corso.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Esecuzione e monitoraggio di un flusso di dati in corso

Per eseguire un'esecuzione del debug di una pipeline di un flusso di dati che si verifica, fare clic su **debug** nel Canvas della pipeline. Una volta pubblicato il flusso di dati, **trigger esegue ora** un'esecuzione su richiesta dell'ultima pipeline pubblicata. La pianificazione dei flussi di dati può essere pianificata con tutti i trigger di Azure Data Factory esistenti.

![Screenshot che illustra come aggiungere un flusso di dati in confronto.](media/wrangling-data-flow/tutorial3.png)

Passare alla scheda **monitoraggio** per visualizzare l'output di un'esecuzione dell'attività flusso di dati avviata.

![Screenshot che mostra l'output di un'attività del flusso di dati avviata.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati di mapping](tutorial-data-flow.md).
