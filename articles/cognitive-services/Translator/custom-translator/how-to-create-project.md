---
title: Come creare un convertitore personalizzato per il progetto
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e gestire un progetto nel traduttore personalizzato di servizi cognitivi di Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895799"
---
# <a name="create-a-project"></a>Creare un progetto

Un progetto è un contenitore per modelli, documenti e test. Ogni progetto include automaticamente tutti i documenti caricati nell'area di lavoro con la coppia di lingue corretta.

La creazione del progetto è il primo passaggio per la creazione di un modello.

## <a name="create-a-project"></a>Creare un progetto:

1.  Nel portale di [Custom Translator](https://portal.customtranslator.azure.ai) fare clic su Create project (Crea progetto).

    ![Crea progetto](media/how-to/how-to-create-project.png)

2.  Immettere i dettagli seguenti relativi al progetto nella finestra di dialogo:

    a.  Project Name (Nome del progetto) - obbligatorio: assegnare al progetto un nome univoco e significativo. Non è necessario citare le lingue all'interno del titolo.

    b.  Description (Descrizione): una breve descrizione sul progetto. Questa descrizione non influisce sul comportamento di Custom Translator o sul sistema personalizzato risultante, ma consente di distinguere i diversi progetti.

    c.  Language pair (Coppia di lingue) - obbligatorio: selezionare la lingua di origine e di destinazione per la traduzione.

    d.  Category (Categoria) - obbligatorio: selezionare la categoria più appropriata per il progetto. La categoria descrive la terminologia e lo stile dei documenti che si intende tradurre.

    e.  Category description (Descrizione categoria): usare questo campo per descrivere meglio il campo o il settore specifico in cui si lavora. Ad esempio, se la categoria è medicina, si potrebbe aggiungere un documento particolare, ad esempio con tema chirurgia o pediatria. La descrizione non influisce sul comportamento di Custom Translator o sul sistema personalizzato risultante.

    f.  Project label (Etichetta progetto): l'[etichetta del progetto](workspace-and-project.md#project-labels) consente di distinguere tra progetti con la stessa coppia di lingue e categoria. Come procedura consigliata, usare un'etichetta *solo* se si prevede di creare più progetti per la stessa coppia di lingue e la stessa categoria e si vuole accedere a questi progetti con un ID di categoria diverso. Non usare questo campo se si creano sistemi per una sola categoria. Un'etichetta di progetto non è obbligatoria e non è utile per distinguere tra coppie di lingue. È possibile usare la stessa etichetta per più progetti.

    ![Finestra di dialogo per la creazione del progetto](media/how-to/how-to-create-project-dialog.png)

3.  Fare clic su Crea

## <a name="view-project-details"></a>Visualizzare i dettagli del progetto

La pagina di destinazione di Custom Translator mostra i primi 10 progetti nell'area di lavoro. Visualizza il nome del progetto, la coppia di lingue, la categoria, lo stato e il punteggio BLEU.

Dopo aver selezionato un progetto, si noteranno le informazioni seguenti nella pagina del progetto:

- Category ID (ID categoria): l'ID della categoria viene creato concatenando l'ID dell'area di lavoro, l'etichetta del progetto e il codice di categoria. Usare l'ID di categoria con l'API Traduzione testuale per ottenere traduzioni personalizzate. Per copiare, fare clic sull'icona di copia.

- Pulsante per l'esecuzione del training: usare questo pulsante per avviare il [training di un modello](how-to-train-model.md).

- Pulsante per l'aggiunta di documenti: usare questo pulsante per [caricare documenti](how-to-upload-document.md).

- Pulsante per filtrare i documenti: usare questo pulsante per filtrare e cercare documenti specifici.

    ![Visualizzare i dettagli del progetto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Passaggi successivi

- Scoprire [come cercare, eliminare e modificare progetti](how-to-search-edit-delete-projects.md).
- Scoprire [come caricare documenti](how-to-upload-document.md) per compilare modelli di traduzione.
