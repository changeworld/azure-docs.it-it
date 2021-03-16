---
title: 'ML Studio (classico): eseguire la migrazione a un esperimento di ricompilazione Azure Machine Learning'
description: Ricompilare gli esperimenti di studio (classico) in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565169"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Ricompilare un esperimento di studio (classico) in Azure Machine Learning

Questo articolo illustra come ricompilare un esperimento di studio (classico) in Azure Machine Learning. Per altre informazioni sulla migrazione da studio (classico), vedere [l'articolo Panoramica della migrazione](migrate-overview.md).

Gli **esperimenti** di studio (classico) sono simili alle **pipeline** in Azure Machine Learning. Tuttavia, in Azure Machine Learning pipeline sono basate sullo stesso back-end che alimenta l'SDK. Ciò significa che sono disponibili due opzioni per lo sviluppo di Machine Learning: la finestra di progettazione con trascinamento della selezione o gli SDK Code-First.

Per altre informazioni sulla creazione di pipeline con l'SDK, vedere informazioni sulle [pipeline Azure Machine Learning](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'area di lavoro di Azure Machine Learning. [Creare un'area di lavoro di Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Esperimento di studio (classico) per la migrazione.
- [Caricare il set di dati](migrate-register-dataset.md) in Azure Machine Learning.

## <a name="rebuild-the-pipeline"></a>Ricompilare la pipeline

Dopo aver eseguito [la migrazione del set di dati in Azure Machine Learning](migrate-register-dataset.md), è possibile ricreare l'esperimento.

In Azure Machine Learning, il grafico visivo è denominato **bozza della pipeline**. In questa sezione si ricrea l'esperimento classico come bozza della pipeline.

1. Vai a Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. Nel riquadro di spostamento a sinistra selezionare **finestra** > **di progettazione moduli predefiniti di facile utilizzo per** la ![ creazione di una nuova bozza di pipeline.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Ricompilare manualmente l'esperimento con i moduli della finestra di progettazione.
    
    Per trovare i moduli sostitutivi, vedere la [tabella di mapping del modulo](migrate-overview.md#studio-classic-and-designer-module-mapping) . Molti dei moduli più diffusi di studio (classico) hanno versioni identiche nella finestra di progettazione.

    > [!Important]
    > Se l'esperimento usa il modulo Execute R script, è necessario eseguire passaggi aggiuntivi per eseguire la migrazione dell'esperimento. Per altre informazioni, vedere [migrate R script Modules](migrate-execute-r-script.md).

1. Modificare i parametri.
    
    Selezionare ogni modulo e modificare i parametri nel pannello Impostazioni modulo a destra. Usare i parametri per ricreare la funzionalità dell'esperimento di studio (classico). Per ulteriori informazioni su ogni modulo, vedere il [riferimento al modulo](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Inviare un'esecuzione e verificare i risultati

Dopo aver ricreato l'esperimento di studio (classico), è il momento di inviare un' **esecuzione della pipeline**.

Un'esecuzione di pipeline viene eseguita su una **destinazione di calcolo** collegata all'area di lavoro. È possibile impostare una destinazione di calcolo predefinita per l'intera pipeline oppure è possibile specificare le destinazioni di calcolo in base al modulo.

Una volta inviata un'esecuzione da una bozza della pipeline, questa viene trasformata in un' **esecuzione della pipeline**. Ogni esecuzione della pipeline viene registrata e registrata Azure Machine Learning.

Per impostare una destinazione di calcolo predefinita per l'intera pipeline:
1. Selezionare l'icona a forma di ingranaggio icona a forma di **ingranaggio** ![ ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) accanto al nome della pipeline.
1. Selezionare **Seleziona destinazione di calcolo**.
1. Selezionare un calcolo esistente o creare un nuovo calcolo seguendo le istruzioni visualizzate.

Ora che è stata impostata la destinazione di calcolo, è possibile inviare un'esecuzione della pipeline:

1. Nella parte superiore dell'area di disegno selezionare **Invia**.
1. Selezionare **Crea nuovo** per creare un nuovo esperimento.
    
    Gli esperimenti organizzano esecuzioni di pipeline simili. Se una pipeline viene eseguita più volte, è possibile selezionare lo stesso esperimento per le esecuzioni successive. Questa operazione è utile per la registrazione e il rilevamento.
1. Immettere un nome per l'esperimento. e quindi selezionare **Invia**.

    La prima esecuzione può richiedere fino a 20 minuti. Poiché le impostazioni di calcolo predefinite hanno una dimensione minima del nodo pari a 0, la finestra di progettazione deve allocare risorse dopo l'inattività. Le esecuzioni successive importano meno tempo, perché i nodi sono già allocati. Per velocizzare il tempo di esecuzione, è possibile creare una risorsa di calcolo con una dimensione minima del nodo maggiore o uguale a 1.

Al termine dell'esecuzione, è possibile controllare i risultati di ogni modulo:

1. Fare clic con il pulsante destro del mouse sul modulo di cui si desidera visualizzare l'output.
1. Selezionare Visualizza **,** **Visualizza output** o **Visualizza log**.

    - **Visualizza**: Visualizza in anteprima il set di dati dei risultati.
    - **Visualizza output**: consente di aprire un collegamento al percorso di archiviazione di output. Usare questo per esplorare o scaricare l'output. 
    - **Visualizza log**: Visualizza i registri driver e di sistema. Usare il **70_driver_log** per visualizzare le informazioni correlate allo script inviato dall'utente, ad esempio errori ed eccezioni.

> [!IMPORTANT]
> I moduli di progettazione usano pacchetti Python open source, rispetto ai pacchetti C# in studio (versione classica). Di conseguenza, l'output del modulo può variare leggermente tra la finestra di progettazione e studio (classica). 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come ricompilare un esperimento di studio (classico) in Azure Machine Learning. Il passaggio successivo consiste [nel ricompilare i servizi Web in Azure Machine Learning](migrate-rebuild-web-service.md).


Vedere gli altri articoli della serie di migrazione Studio (classica):

1. [Panoramica sulla migrazione](migrate-overview.md).
1. [Eseguire la migrazione del set di dati](migrate-register-dataset.md).
1. **Ricompilare una pipeline di training di studio (classica)**.
1. [Ricompilare un servizio Web di studio (classico)](migrate-rebuild-web-service.md).
1. [Integrare un servizio web Azure Machine Learning con le app client](migrate-rebuild-integrate-with-client-app.md).
1. [Migrare Execute R script](migrate-execute-r-script.md).
