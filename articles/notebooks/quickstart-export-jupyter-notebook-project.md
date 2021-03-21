---
title: Usa un Jupyter Notebook con offerte Microsoft
description: Informazioni sul modo in cui è possibile usare i notebook di Jupyter con le offerte Microsoft.
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507378"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Usa un Jupyter Notebook con offerte Microsoft

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In questa Guida introduttiva si apprenderà come importare un Jupyter Notebook per l'uso in offerte Microsoft assorte. 

Se si dispone di notebook Jupyter esistenti o si vuole avviare un nuovo progetto, è possibile usarli con molte offerte di Microsoft. Alcune opzioni descritte nelle sezioni seguenti includono: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Creare un ambiente per i notebook

Se si vuole creare un ambiente che corrisponda a quello dell'anteprima Azure Notebooks ritirata, è possibile usare il file script fornito in GitHub.

1. Passare al [repository GitHub](https://github.com/microsoft/AzureNotebooks) di Azure Notebooks o [accedere direttamente alla cartella dell'ambiente](https://aka.ms/aznbrequirementstxt).
1. Da un prompt dei comandi passare alla directory che si vuole usare per i progetti.
1. Scaricare il contenuto della cartella dell'ambiente e seguire le istruzioni del file README per installare le dipendenze del pacchetto Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Usare i notebook in Visual Studio Code

[VS Code](https://code.visualstudio.com/) è un editor di codice gratuito che può essere usato in locale o connesso a una risorsa di calcolo remota. In combinazione con l'estensione Python, offre un ambiente completo per lo sviluppo in Python, inclusa un'esperienza nativa avanzata per l'uso di Jupyter Notebook. 

![Supporto di VS Code per Jupyter Notebook](media/vs-code-jupyter-notebook.png)

Se si dispone di file di progetto esistenti o si desidera creare un nuovo notebook, è possibile utilizzare VS Code. Per informazioni sull'uso di VS Code con i notebook di Jupyter, vedere la pagina relativa all'uso dei [notebook di Jupyter in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) e [Data Science in Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial) esercitazioni.

È anche possibile usare lo [script dell'ambiente Azure Notebooks](#create-an-environment-for-notebooks) con Visual Studio Code per creare un ambiente corrispondente a quello di Azure Notebooks (anteprima).

## <a name="use-notebooks-in-github-codespaces"></a>Usare Notebooks in GitHub Codespaces

GitHub Codespaces offre ambienti ospitati nel cloud, in cui è possibile modificare i notebook usando Visual Studio Code, o nel Web browser. Offre la stessa esperienza Jupyter di VS Code, ma senza la necessità di installare nulla sul dispositivo. Se non si vuole configurare un ambiente locale e si preferisce una soluzione basata sul cloud, la creazione di un codespace è un'ottima opzione. Attività iniziali
1. Opzionale Raccogliere tutti i file di progetto che si vuole usare con gli spazi dei dati di GitHub.
1. [Creare un repository GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) per archiviare i notebook.   
1. [Aggiungere i file](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) al repository.
1. [Richiedere l'accesso all'anteprima di GitHub Codespaces](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Usare i notebook con Azure Machine Learning

Azure Machine Learning offre una piattaforma di Machine Learning end-to-end per consentire agli utenti di compilare e distribuire modelli più velocemente in Azure. Azure ML consente di eseguire Jupyter Notebook in una macchina virtuale o in un ambiente di calcolo cluster condiviso. Se si necessita di una soluzione basata sul cloud per il carico di lavoro ML con verifica esperimenti, gestione del set di dati e altre funzionalità, è consigliabile usare Azure Machine Learning. Per iniziare a usare Azure ML:

1. Opzionale Raccogliere tutti i file di progetto che si vuole usare con Azure ML.
1. [Creare un'area di lavoro](../machine-learning/how-to-manage-workspace.md) nel portale di Azure.

   ![Creare un'area di lavoro](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Aprire [Azure Studio (anteprima)](https://ml.azure.com/).
1. Usando la barra di spostamento a sinistra, selezionare **Notebook**.
1. Fare clic sul pulsante **Carica file** e caricare i file di progetto.

Per altre informazioni su Azure ML e sull'esecuzione di Jupyter Notebook, è possibile vedere la [documentazione](../machine-learning/how-to-run-jupyter-notebooks.md) o provare il modulo [Introduzione a Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) in Microsoft Learn.


## <a name="use-azure-lab-services"></a>Usare Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) consente ai docenti di configurare facilmente e fornire l'accesso su richiesta a macchine virtuali preconfigurate per un'intera classe. Se si sta cercando una soluzione per usare Jupyter Notebook e le risorse di calcolo cloud in un ambiente di classe personalizzato, Lab Services è un'ottima opzione.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

Se si dispone di file di progetto esistenti o si desidera creare un nuovo notebook, è possibile utilizzare Azure Lab Services. Per indicazioni su come configurare un Lab, vedere [Configurare un lab per data science con Python e Jupyter Notebook](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Usare GitHub

GitHub offre un modo gratuito basato sul controllo del codice sorgente per archiviare notebook (e altri file), condividere i notebook con altri utenti e collaborare in modo efficace. Se si sta cercando una soluzione per condividere i progetti e collaborare con altri utenti, GitHub è un'ottima opzione e può essere combinato con [GitHub Codespaces](#use-notebooks-in-github-codespaces) per un'esperienza di sviluppo ottimale. Per iniziare a usare GitHub

1. Opzionale Raccogliere tutti i file di progetto che si vuole usare con GitHub.
1. [Creare un repository GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) per archiviare i notebook. 
1. [Aggiungere i file](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) al repository.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Informazioni su Azure Machine Learning Jupyter Notebook](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Informazioni su GitHub Codespaces](https://github.com/features/codespaces)
- [Informazioni su Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Informazioni su GitHub](https://help.github.com/github/getting-started-with-github/)
