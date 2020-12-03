---
title: Usare la studio di competenza
description: Questo articolo concettuale descrive come usare Azure competenza Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1b2d371153d6612f454e1bf51b78c6b6189a08b2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552621"
---
# <a name="use-purview-studio"></a>Usare la studio di competenza

Questo articolo fornisce una panoramica di alcune delle principali funzionalità di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account di competenza attivo è già stato creato in portale di Azure e l'utente dispone delle autorizzazioni per accedere a ambito Studio.

## <a name="launch-purview-account"></a>Avvia account di competenza

* Per avviare l'account di competenza, passare a account di competenza in portale di Azure, selezionare l'account che si vuole avviare e avviare l'account.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot della selezione per avviare il catalogo degli account Azure per le competenze.":::

* Un altro modo per avviare l'account di competenza consiste nell'accedere a `https://web.purview.azure.com` , selezionare **Azure Active Directory** e un nome di account per avviare l'account.

## <a name="home-page"></a>Home page

**Home** è la pagina iniziale per il client Azure per la competenza.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot della Home page.":::

Nell'elenco seguente sono riepilogate le principali funzionalità della **Home page**. Ogni numero nell'elenco corrisponde a un numero evidenziato nella schermata precedente.

1. Nome descrittivo del catalogo. È possibile impostare il nome del catalogo in **Management Center** > **informazioni sull'account*.

2. Il catalogo Analytics mostra il numero di:
    - Utenti, gruppi e applicazioni
    - Origini dati
    - Asset
    - Termini di glossario

3. La casella di ricerca consente di cercare gli asset di dati nel Catalogo dati.

4. I pulsanti di accesso rapido consentono di accedere alle funzioni di uso frequente dell'applicazione. I pulsanti presentati variano a seconda del ruolo assegnato all'account utente.

    - Per l' *amministratore dell'origine dati*, i pulsanti di accesso rapido sono: **registra origini dati** e **Knowledge Center**.
    - Per *Data Curator*, i pulsanti sono **Knowledge Center**, **Esplora risorse**, **Gestisci Glossario** e **Visualizza informazioni dettagliate**.
    - Per il *lettore dati*, i pulsanti in primo piano sono **Knowledge Center**, **Esplora risorse**, **Visualizza glossario** e **Visualizza informazioni dettagliate**.

5. La barra di spostamento a sinistra consente di individuare le pagine principali dell'applicazione. I pulsanti presentati variano a seconda del ruolo assegnato all'account utente.

    - Per l' *amministratore dell'origine dati*, i pulsanti sono  **Home**, **origini** e **centro di gestione**.
    - Per il *curatore dei dati*, i pulsanti sono **Home**, **Glossario**, **Insights** e **centro di gestione**.
    - Per *il lettore dati*, i pulsanti sono **Home**, **Glossario**, **Insights** e centro di **gestione**.
  
6. La scheda **accesso di recente** Mostra un elenco degli asset di dati a cui si accede di recente. Per informazioni sull'accesso alle risorse, vedere [eseguire ricerche nel Data Catalog](how-to-search-catalog.md) e cercare [in base al tipo di asset](how-to-browse-catalog.md#browse-experience).  La scheda **elementi personali** è un elenco di asset di dati di proprietà dell'utente connesso.
7. **Collegamenti utili** contiene collegamenti a stato area, documentazione, prezzi, panoramica e stato di competenza
8. La barra di spostamento superiore contiene informazioni sulle note sulla versione/aggiornamenti, le sezioni relative a account, notifiche, guida e commenti e suggerimenti.

## <a name="knowledge-center"></a>Centro informazioni

Nel Knowledge Center è possibile trovare tutti i video e le esercitazioni relative alla competenza.

## <a name="guided-tours"></a>Presentazioni guidate

Ogni esperienza utente in Azure ambito studio avrà Tours guidati per fornire una panoramica della pagina. Per avviare la presentazione guidata **, fare clic su?** nella barra superiore e selezionare **visite guidate**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot della presentazione guidata.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'entità di sicurezza](tutorial-scan-data.md)