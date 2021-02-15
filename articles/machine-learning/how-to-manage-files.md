---
title: Creare e gestire i file nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e gestire i file nell'area di lavoro in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101588"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Come creare e gestire i file nell'area di lavoro

Informazioni su come creare e gestire i file nell'area di lavoro Azure Machine Learning.  Questi file vengono archiviati nell'archivio dell'area di lavoro predefinito. I file e le cartelle possono essere condivisi con altri utenti con accesso in lettura all'area di lavoro e possono essere usati da qualsiasi istanza di calcolo nell'area di lavoro.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Crea file

Per creare un nuovo file nella cartella predefinita ( `Users > yourname` ):

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook).
1. Selezionare l' **+** immagine.
1. Selezionare l'immagine  **Crea nuovo file** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file"::: (Crea nuovo file)

1. Assegnare un nome al file.
1. Selezionare un tipo di file.
1. Selezionare **Crea**.

I notebook e la maggior parte dei tipi di file di testo vengono visualizzati nella sezione di anteprima.  Per la maggior parte degli altri tipi di file non è presente un'anteprima.

Per creare un nuovo file in una cartella diversa:
1. Selezionare "..." alla fine della cartella
1. Selezionare **Create new file** (Crea nuovo file).

> [!IMPORTANT]
> Il contenuto di notebook e script può potenzialmente leggere i dati delle sessioni e accedere ai dati senza l'organizzazione in Azure.  Caricare solo file da origini attendibili. Per altre informazioni, vedere [procedure consigliate per il codice protetto](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Gestire i file con git

[Usare un terminale dell'istanza di calcolo](how-to-access-terminal.md#git) per clonare e gestire i repository git.

## <a name="clone-samples"></a>Clonare gli esempi

L'area di lavoro contiene una cartella di **notebook di esempio** con notebook progettati per semplificare l'esplorazione dell'SDK e fungere da esempi per i propri progetti di machine learning.   solo questi notebook nella cartella per eseguirli e modificarli.  

Per un esempio, vedere [Esercitazione: Creare il primo esperimento di Machine Learning](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Condividi file

Copiare e incollare l'URL per condividere un file.  Solo gli altri utenti dell'area di lavoro possono accedere a questo URL.  Vedere altre informazioni su come [concedere l'accesso all'area di lavoro](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Eliminare un file

*Non è possibile* eliminare i file dei **notebook di esempio** .  Questi file fanno parte di studio e vengono aggiornati ogni volta che viene pubblicato un nuovo SDK.  

È *possibile* eliminare i file presenti nella sezione **file** in uno dei modi seguenti:

* In Studio selezionare **...** alla fine di una cartella o un file.  Assicurarsi di usare un browser supportato (Microsoft Edge, Chrome o Firefox).
* [Usare un terminale](how-to-access-terminal.md) da qualsiasi istanza di calcolo nell'area di lavoro. Viene eseguito il mapping della cartella **~/cloudfiles** alla risorsa di archiviazione nell'account di archiviazione dell'area di lavoro.
* In Jupyter o JupyterLab con i relativi strumenti.
