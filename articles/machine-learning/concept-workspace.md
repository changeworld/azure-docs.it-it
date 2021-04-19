---
title: Che cos'è un'area di lavoro
titleSuffix: Azure Machine Learning
description: L'area di lavoro è la risorsa di primo livello per Azure Machine Learning. Mantiene una cronologia di tutte le esecuzioni di training, con log, metriche, output e uno snapshot degli script.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719062"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Definizione di area di lavoro di Azure Machine Learning

L'area di lavoro è la risorsa di primo livello per Azure Machine Learning, offrendo una posizione centralizzata in cui lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi log, metriche, output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Dopo aver creato un modello, registrarlo nell'area di lavoro. È quindi possibile usare il modello registrato e gli script di assegnazione dei punteggi per la distribuzione in Istanze di Azure Container, servizio Azure Kubernetes o in un field-programmable gate array (FPGA) come endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i componenti seguenti di un'area di lavoro:

+ Un'area di lavoro [può contenere Azure Machine Learning di calcolo](concept-compute-instance.md), risorse cloud configurate con l'ambiente Python necessario per eseguire Azure Machine Learning.

+ [I ruoli utente](how-to-assign-roles.md) consentono di condividere l'area di lavoro con altri utenti, team o progetti.
+ [Le destinazioni](concept-azure-machine-learning-architecture.md#compute-targets) di calcolo vengono usate per eseguire gli esperimenti.
+ Quando si crea l'area di [lavoro, vengono](#resources) create automaticamente anche le risorse associate.
+ [Gli esperimenti](concept-azure-machine-learning-architecture.md#experiments) sono esecuzioni di training usate per compilare i modelli.  
+ [Le pipeline sono](concept-azure-machine-learning-architecture.md#ml-pipelines) flussi di lavoro riutilizzabili per il training e la nuova formazione del modello.
+ [I set di](concept-azure-machine-learning-architecture.md#datasets-and-datastores) dati sono di aiuto nella gestione dei dati utilizzati per il training del modello e la creazione della pipeline.
+ Dopo aver creato un modello da distribuire, creare un modello registrato.
+ Usare il modello registrato e uno script di assegnazione dei punteggi per creare un [endpoint di distribuzione](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Strumenti per l'interazione dell'area di lavoro

È possibile interagire con l'area di lavoro nei modi seguenti:

> [!IMPORTANT]
> Gli strumenti contrassegnati (anteprima) di seguito sono attualmente in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Sul Web:
    + [studio di Azure Machine Learning ](https://ml.azure.com) 
    + [Finestra di progettazione di Azure Machine Learning](concept-designer.md) 
+ In qualsiasi ambiente Python con Azure Machine Learning [SDK per Python](/python/api/overview/azure/ml/intro).
+ In qualsiasi ambiente R con Azure Machine Learning [SDK per R (anteprima).](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ Nella riga di comando usando l'estensione dell'interfaccia Azure Machine Learning [comando](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS Code estensione](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Machine Learning con un'area di lavoro

Le attività di Machine Learning leggono e/o scrivono elementi nell'area di lavoro.

+ Eseguire un esperimento per eseguire il training di un modello: scrive i risultati dell'esecuzione dell'esperimento nell'area di lavoro.
+ Usare Machine Learning automatizzato per eseguire il training di un modello: scrive i risultati del training nell'area di lavoro.
+ Registrare un modello nell'area di lavoro.
+ Distribuire un modello: usa il modello registrato per creare una distribuzione.
+ Creare ed eseguire flussi di lavoro riutilizzabili.
+ Visualizzare gli artefatti di Machine Learning, ad esempio esperimenti, pipeline, modelli, distribuzioni.
+ Tenere traccia e monitorare i modelli.

## <a name="workspace-management"></a>Gestione dell'area di lavoro

È anche possibile eseguire le attività di gestione dell'area di lavoro seguenti:

| Attività di gestione dell'area di lavoro   | Portale              | Studio | Python SDK/R SDK       | CLI        | Visual Studio Code
|---------------------------|---------|---------|------------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Gestisci l'accesso all'area di lavoro    | **&check;**   || |  **&check;**    ||
| Creare e gestire le risorse di calcolo    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Creare una macchina virtuale notebook |   | **&check;** | |     ||

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Creare un'area di lavoro

Esistono diversi modi per creare un'area di lavoro:  

* Usare il [portale di Azure](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) per un'interfaccia con punti e clic per illustrare ogni passaggio.
* Usare Azure Machine Learning [SDK per Python per](how-to-manage-workspace.md?tabs=python#create-a-workspace) creare un'area di lavoro in tempo reale da script Python o jupyter notebook
* Usare un modello [Azure Resource Manager o l'interfaccia](how-to-create-workspace-template.md) [Azure Machine Learning comando](reference-azure-machine-learning-cli.md) quando è necessario automatizzare o personalizzare la creazione con gli standard di sicurezza aziendali.
* Se si lavora in Visual Studio Code, usare [l'estensione VS Code .](how-to-manage-resources-vscode.md#create-a-workspace)

> [!NOTE]
> Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.

## <a name="associated-resources"></a><a name="resources"></a> Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Archiviazione di Azure account:](https://azure.microsoft.com/services/storage/)viene usato come archivio dati predefinito per l'area di lavoro.  Anche i notebook di Jupyter usati con le Azure Machine Learning di calcolo vengono archiviati qui. 
  
  > [!IMPORTANT]
  > Per impostazione predefinita, l'account di archiviazione è un account per utilizzo generico v1. È possibile [eseguire l'aggiornamento alla versione per utilizzo generico v2](../storage/common/storage-account-upgrade.md) dopo la creazione dell'area di lavoro. Non abilitare lo spazio dei nomi gerarchico nell'account di archiviazione dopo l'aggiornamento alla versione 2 per utilizzo generico.

  Per usare un account Archiviazione di Azure esistente, non può essere di tipo BlobStorage o un account Premium (Premium_LRS e Premium_GRS). Non può inoltre avere uno spazio dei nomi gerarchico (usato con Azure Data Lake Storage Gen2). Né l'archiviazione Premium né gli spazi dei nomi gerarchici sono supportati con _l'account_ di archiviazione predefinito dell'area di lavoro. È possibile usare l'archiviazione Premium o lo spazio dei nomi gerarchico _con account di archiviazione non_ predefiniti.
  
+ [Registro Azure Container](https://azure.microsoft.com/services/container-registry/): registra i contenitori Docker da usare durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, il Servizio Di controllo di accesso **viene caricato lazy fino** a quando non vengono create immagini di distribuzione.

+ [applicazione Azure Insights:](https://azure.microsoft.com/services/application-insights/)archivia le informazioni di monitoraggio sui modelli.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): archivia i segreti usati dalle destinazioni di calcolo e altre informazioni riservate necessarie per l'area di lavoro.

> [!NOTE]
> È invece possibile usare istanze di risorse di Azure esistenti quando si crea l'area di lavoro con [Python SDK,](how-to-manage-workspace.md?tabs=python#create-a-workspace) [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)o l'interfaccia della riga di comando Azure Machine Learning usando un modello di [Resource Kit.](how-to-create-workspace-template.md)

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Cosa è successo all'edizione Enterprise

A partire da settembre 2020, tutte le funzionalità disponibili nelle aree di lavoro dell'edizione Enterprise sono ora disponibili anche nelle aree di lavoro dell'edizione Basic. Non è più possibile creare nuove aree di lavoro aziendali.  Qualsiasi SDK, interfaccia della riga di comando o Azure Resource Manager che usano il parametro continuerà a funzionare, ma verrà eseguito il provisioning di un'area di lavoro `sku` Basic.

A partire dal 21 dicembre, tutte edizione Enterprise di lavoro verranno impostate automaticamente su Basic Edition, che ha le stesse funzionalità. Durante questo processo non si verificherà alcun tempo di inattività. Il 1° gennaio 2021 edizione Enterprise ritiro formale. 

In entrambe le edizioni i clienti sono responsabili dei costi delle risorse di Azure utilizzate e non dovranno pagare costi aggiuntivi per Azure Machine Learning. Per altri [dettagli, vedere la](https://azure.microsoft.com/pricing/details/machine-learning/) pagina Azure Machine Learning prezzi.

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ad Azure Machine Learning, vedere:

+ [Azure Machine Learning panoramica](overview-what-is-azure-ml.md)
+ [Creare e gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Introduzione alle Azure Machine Learning nell'ambiente di sviluppo](tutorial-1st-experiment-sdk-setup-local.md)
+ [Esercitazione: Introduzione alla creazione del primo esperimento di Machine Learning in un'istanza di calcolo](tutorial-1st-experiment-sdk-setup.md)
+ [Esercitazione: Creare il primo modello di classificazione con apprendimento automatico](tutorial-first-experiment-automated-ml.md) 
+ [Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
