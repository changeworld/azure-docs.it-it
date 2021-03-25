---
title: Come modificare in blocco gli asset per classificazioni di tag, termini di glossario e modificare i contatti
description: Informazioni su come modificare in blocco le risorse in Azure.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 4dc1af590a1965c155a7af7b233b431f982a73d2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024235"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Come modificare in blocco gli asset per annotare le classificazioni, i termini di glossario e modificare i contatti

Questo articolo descrive come contrassegnare più termini di glossario, classificazioni, proprietari ed esperti in un elenco di asset selezionati in un'unica azione.

### <a name="add-assets-to-view-selected-list-using-search"></a>Aggiungi asset per visualizzare l'elenco selezionato usando la ricerca

1. Eseguire una ricerca nell'asset di dati che si desidera aggiungere all'elenco per la modifica in blocco.

2. Nella pagina dei risultati della ricerca, passare il mouse sull'asset che si desidera aggiungere all'elenco di visualizzazione di modifica bulk **selezionato** per visualizzare una casella di controllo.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Screenshot della casella di controllo.":::

3. Selezionare la casella di controllo per aggiungerla all'elenco di visualizzazione di modifica in blocco **selezionato** . Una volta aggiunta, l'icona elementi selezionati viene visualizzata nella parte inferiore della pagina.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Screenshot dell'elenco.":::

4. Ripetere i passaggi precedenti per aggiungere tutti gli asset di dati all'elenco.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Aggiungi asset per visualizzare l'elenco selezionato dalla pagina dettagli Asset

1. Nella pagina dettagli Asset selezionare la casella di controllo nell'angolo superiore destro per aggiungere l'asset all'elenco di visualizzazione di modifica bulk **selezionato** .

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Screenshot dell'asset.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Modificare in blocco gli asset nell'elenco Visualizza selezionati per aggiungere, sostituire o rimuovere i termini di glossario.

1. Una volta eseguita l'identificazione di tutti gli asset di dati che devono essere modificati in blocco, selezionare Visualizza elenco **selezionato** dalla pagina dei risultati della ricerca o dalla pagina dettagli Asset.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Screenshot della visualizzazione.":::

2. Esaminare l'elenco e selezionare **Rimuovi** se si desidera rimuovere qualsiasi condizione.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Screenshot della rimozione.":::

3. Selezionare modifica in blocco per aggiungere, rimuovere o sostituire i termini di glossario per tutti gli asset selezionati.

4. Per aggiungere termini di glossario, selezionare operazione come **Aggiungi**. Selezionare tutti i termini di glossario che si desidera aggiungere al nuovo valore. Al termine, selezionare Applica.
    - L'operazione di aggiunta aggiungerà nuovo valore all'elenco dei termini di glossario già contrassegnati per gli asset di dati.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Screenshot dell'aggiunta.":::

5. Per sostituire i termini di glossario, selezionare operazione come **Sostituisci con**. Selezionare tutti i termini di glossario che si desidera sostituire nel nuovo valore. Al termine, selezionare Applica.
    - L'operazione di sostituzione sostituisce tutti i termini di glossario per gli asset di dati selezionati con i termini selezionati in nuovo valore.
   
6. Per rimuovere i termini di glossario, selezionare operazione come **Rimuovi**. Al termine, selezionare Applica.
    - Rimuovi operazione eliminerà tutti i termini di glossario per gli asset di dati selezionati.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Screenshot dei termini di rimozione.":::

7. Ripetere le versioni precedenti per le classificazioni, i proprietari e gli esperti.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Screenshot delle classificazioni e dei contatti.":::

8. Al termine, chiudere il pannello di modifica bulk selezionando **Chiudi** o **Rimuovi tutto e Chiudi**. Close non rimuoverà gli asset selezionati, mentre Remove All e Close rimuoverà tutti gli asset selezionati.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Screenshot della chiusura.":::

   > [!Important]
   > Il numero consigliato di asset per la modifica bulk è 25. La selezione di più di 25 potrebbe causare problemi di prestazioni.
   > La casella **Visualizza selezionato** sarà visibile solo se è selezionato almeno un asset.


Per altre informazioni, seguire l' [esercitazione: creare e importare termini di glossario](how-to-create-import-export-glossary.md) .
