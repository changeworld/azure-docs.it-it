---
title: Attività Power Query in Azure Data Factory
description: Informazioni su come usare l'attività Power Query per le funzionalità di data wrangling in una pipeline Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385456"
---
# <a name="power-query-activity-in-data-factory"></a>Attività di Power query in data factory

L'attività Power Query consente di compilare ed eseguire Power Query mash-up per eseguire data wrangling su larga scala in una pipeline Data Factory. È possibile creare un nuovo mash-up Power Query dall'opzione di menu nuove risorse oppure aggiungendo un'attività di risparmio energia alla pipeline.

![Screenshot che mostra Power Query nel riquadro risorse Factory.](media/data-flow/power-query-wrangling.png)

In precedenza, data wrangling in Azure Data Factory veniva creato dall'opzione di menu flusso di dati. Questa operazione è stata modificata per la creazione da una nuova attività di Power Query. È possibile lavorare direttamente all'interno dell'editor di mash-up Power Query per eseguire l'esplorazione interattiva dei dati e quindi salvare il lavoro. Al termine, è possibile eseguire l'attività di Power Query e aggiungerla a una pipeline. Azure Data Factory lo scalerà automaticamente e rendere operativo i data wrangling usando l'ambiente Spark del flusso di dati di Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Conversione nello script del flusso di dati

Per ottenere la scalabilità con l'attività Power Query, Azure Data Factory converte lo ```M``` script in uno script del flusso di dati in modo che sia possibile eseguire le Power query su larga scala usando l'ambiente Spark del flusso di dati Azure Data Factory. Consente di creare il flusso di dati che si verifica usando la preparazione dei dati senza codice. Per l'elenco delle funzioni disponibili, vedere [funzioni di trasformazione](wrangling-functions.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui concetti di data wrangling con [Power query in Azure Data Factory](wrangling-tutorial.md)
