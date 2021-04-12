---
title: Usare Azure competenza Studio
description: Questo articolo descrive come usare Azure competenza Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283870"
---
# <a name="use-purview-studio"></a>Usare Purview Studio

Questo articolo fornisce una panoramica di alcune delle principali funzionalità di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account di competenza attivo è già stato creato in portale di Azure e l'utente dispone delle autorizzazioni per accedere a ambito Studio.

## <a name="launch-purview-account"></a>Avvia account di competenza

* Per avviare l'account di competenza, passare a account di competenza in portale di Azure, selezionare l'account che si vuole avviare e avviare l'account.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot dell'opzione da selezionare per avviare il catalogo dell'account Azure Purview.":::

* Un altro modo per avviare l'account di competenza consiste nell'accedere a `https://web.purview.azure.com` , selezionare **Azure Active Directory** e un nome di account per avviare l'account.

## <a name="home-page"></a>Home page

**Home** è la pagina iniziale per il client Azure per la competenza.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot della Home page.":::

Nell'elenco seguente sono riepilogate le principali funzionalità della **Home page**. Ogni numero nell'elenco corrisponde a un numero evidenziato nella schermata precedente.

1. Nome descrittivo del catalogo. È possibile impostare il nome del catalogo nelle  >  **informazioni sull'account** del centro di gestione.

2. Il catalogo Analytics mostra il numero di:

   * Utenti, gruppi e applicazioni
   * Origini dati
   * Asset
   * Termini di glossario

3. La casella di ricerca consente di cercare gli asset di dati nel Catalogo dati.

4. I pulsanti di accesso rapido consentono di accedere alle funzioni di uso frequente dell'applicazione. I pulsanti presentati variano a seconda del ruolo assegnato all'account utente.

   * Per *Data Curator*, i pulsanti sono **Knowledge Center**, **Esplora risorse**, **Gestisci Glossario** e **Visualizza informazioni dettagliate**.
   * Per il *lettore dati*, i pulsanti in primo piano sono **Knowledge Center**, **Esplora risorse**, **Visualizza glossario** e **Visualizza informazioni dettagliate**.
   * Per   +  il *curatore dei dati* amministratore dell'origine dati, i pulsanti in primo piano sono **Knowledge Center**, **registrano le origini dati**, **esplorano gli asset** e **gestiscono il Glossario**.
   * Per   +  il *lettore dati* di amministrazione dell'origine dati, i pulsanti in primo piano sono **Knowledge Center**, **registrano le origini dati**, **esplorano gli asset** e **visualizzano il Glossario**.

5. La barra di spostamento a sinistra consente di individuare le pagine principali dell'applicazione. I pulsanti presentati variano a seconda del ruolo assegnato all'account utente.

   * Per il *curatore dei dati*, i pulsanti sono **Home**, **Glossario**, **Insights** e **centro di gestione**.
   * Per *il lettore dati*, i pulsanti sono **Home**, **Glossario**, **Insights** e centro di **gestione**.
   * Per *amministratore origine dati* o responsabile *dati/lettore*, i pulsanti sono **Home**, **origini**, **Glossario**, **informazioni dettagliate** e **centro di gestione**.
  
6. La scheda **accesso di recente** Mostra un elenco degli asset di dati a cui si accede di recente. Per informazioni sull'accesso alle risorse, vedere [eseguire ricerche nel Data Catalog](how-to-search-catalog.md) e cercare [in base al tipo di asset](how-to-browse-catalog.md#browse-experience).  La scheda **elementi personali** è un elenco di asset di dati di proprietà dell'utente connesso.
7. **Collegamenti utili** contiene collegamenti a stato area, documentazione, prezzi, panoramica e stato di competenza
8. La barra di spostamento superiore contiene informazioni sulle note sulla versione/aggiornamenti, le sezioni relative a account, notifiche, guida e commenti e suggerimenti.

## <a name="knowledge-center"></a>Centro informazioni

Nel Knowledge Center è possibile trovare tutti i video e le esercitazioni relative alla competenza.

## <a name="guided-tours"></a>Presentazioni guidate

Ogni esperienza utente in Azure ambito studio avrà Tours guidati per fornire una panoramica della pagina. Per avviare la presentazione guidata **, fare clic su?** nella barra superiore e selezionare **visite guidate**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot della presentazione guidata.":::

> [!Important]
> Il ruolo di *amministratore dell'origine dati* di per sé non ha accesso a ambito Studio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'entità di sicurezza](tutorial-scan-data.md)
