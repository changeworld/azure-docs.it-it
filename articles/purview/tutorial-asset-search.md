---
title: 'Esercitazione: Esplorare la home page di Azure Purview e cercare un asset'
description: Questa esercitazione descrive come usare le funzionalità della home page di Azure Purview ed eseguire ricerche nel catalogo.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677832"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Esercitazione: Esplorare la home page di Azure Purview (anteprima) e cercare un asset

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

In questa esercitazione si acquisisce familiarità con Azure Purview esplorando le funzionalità della home page e cercando un asset nel catalogo.

Questa è la *seconda parte di una serie di esercitazioni in cinque parti* contenenti le informazioni di base per gestire la governance dell'intera piattaforma dati usando Azure Purview. Questa esercitazione si basa sull'attività completata nella [Parte 1: Analizzare i dati con Azure Purview](tutorial-scan-data.md)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Esplorare la home page di Azure Purview.
> * Cercare un asset.
> * Aggiungere il proprietario di un asset.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione: Analizzare i dati con Azure Purview](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Esplorare la home page di Azure Purview

I passaggi seguenti illustrano la home page di Azure Purview.

1. Passare alla risorsa Azure Purview nel portale di Azure e selezionare **Apri in Purview Studio**. Si viene automaticamente reindirizzati nella home page di Purview Studio.

   Nella parte superiore della home page vengono visualizzati il nome del catalogo e una serie di analisi dello stesso. Sono inclusi il numero di origini, gli asset di dati e i termini di glossario. Nel riepilogo è possibile vedere che sono presenti più di 200 asset in totale e 113 termini di glossario. Questi numeri vengono aggiornati via via che l'organizzazione analizza e aggiunge altri termini in Azure Purview.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Screenshot che mostra la home page di Azure Purview.":::

1. Selezionare **Esplora asset** per visualizzare tutti gli asset.

## <a name="search-for-an-asset"></a>Cercare un asset

Prima di iniziare, ecco un breve aggiornamento della terminologia di base:

* **Asset**: un singolo oggetto conciso del catalogo che contiene la definizione di qualsiasi entità presente nella piattaforma dati. Sono ad esempio entità le tabelle SQL, i report di Power BI e le attività della data factory.
  
* **Schema**: anche detto colonna o attributo, lo schema rappresenta la struttura di un asset o di un set di risorse.

* **Set di risorse**: un singolo oggetto del catalogo che rappresenta molti oggetti fisici presenti nell'archiviazione. Gli oggetti in genere condividono uno schema comune e, nella maggior parte dei casi, una convenzione di denominazione o una struttura di cartelle. Ad esempio, il formato della data è *aaaa/mm/gg*. Per altre informazioni, vedere [Informazioni sui set di risorse](concept-resource-sets.md).

* **Tipo di asset**: un raggruppamento di asset e set di risorse con un nome logico, che in genere corrisponde al nome della piattaforma dati.

Seguire questa procedura per cercare un asset e impostare se stessi come proprietario:

1. Nella casella **Search catalog** (Cerca nel catalogo) della home page immettere il nome del gruppo di risorse che contiene la piattaforma dati, ovvero il gruppo di risorse creato nell'esercitazione precedente. Viene visualizzato un elenco di suggerimenti.

1. Selezionare **Visualizza i risultati della ricerca**. Poiché gli asset iniziano tutti con il nome del gruppo di risorse, vengono visualizzati tutti nei risultati della ricerca. Al di fuori dell'esercitazione si cercherebbero specifici nomi di asset, non gruppi di risorse.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Screenshot che mostra l'elenco di suggerimenti per la ricerca nel catalogo.":::

1. È possibile usare i filtri nel riquadro di spostamento sinistro per filtrare per tipo di asset, classificazione, contatto, etichetta e termine di glossario.

1. Per cercare un set di risorse, iniziare a digitarne il nome. Viene visualizzato un elenco di suggerimenti per la ricerca con le parole chiave corrette. È anche possibile cercare nomi assoluti racchiudendoli tra virgolette.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Ricerca di asset per parola chiave in Azure Purview":::

## <a name="edit-an-asset"></a>Modificare un asset

1. Selezionare uno degli asset nei risultati della ricerca. Quindi selezionare **Modifica**

1. Nella scheda **Panoramica** è possibile aggiungere una descrizione.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Screenshot che mostra il campo Descrizione nella scheda Panoramica.":::

1. Selezionare la scheda **Contatti**. Accanto a **Proprietari**, in **Seleziona utente o gruppo**, iniziare a digitare il proprio indirizzo di posta elettronica aziendale.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Screenshot che mostra i campi popolati.":::

    Viene automaticamente visualizzato un suggerimento per il nome.

1. Accanto a **Esperti**, in **Seleziona utente o gruppo**, immettere un nome, ad esempio quello del proprio manager, quindi selezionare **Salva**.

    I campi di descrizione, nome del proprietario e nome dell'esperto sono ora popolati.

## <a name="next-steps"></a>Passaggi successivi

Prima di passare all'esercitazione successiva di questa serie, esplorare la home page di Azure Purview autonomamente. In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> * Esplorare la home page di Azure Purview.
> * Cercare un asset.
> * Aggiungere il proprietario di un asset.

Passare all'esercitazione successiva per informazioni su come esplorare gli asset in Azure Purview e individuarne la derivazione.

> [!div class="nextstepaction"]
> [Esplorare gli asset e visualizzare la derivazione](tutorial-browse-and-view-lineage.md)
