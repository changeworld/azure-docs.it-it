---
title: Come creare, importare ed esportare i termini di glossario
description: Informazioni su come creare, importare ed esportare i termini di glossario in competenza di Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 7466e143f345ea305c7e9ef118d09fb6f685ac16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694488"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Come creare, importare ed esportare i termini di glossario

Questo articolo descrive come creare un termine di glossario aziendale nel Catalogo dati di Azure e come importare ed esportare i termini di glossario usando i file con estensione CSV.

## <a name="create-a-new-term"></a>Crea un nuovo termine

Per creare un nuovo termine del glossario, seguire questa procedura:

1. Selezionare l'icona del glossario nella finestra di spostamento a sinistra in home page per passare alla pagina elenco termini.

2. Nella pagina **termini del glossario** selezionare **+ nuovo termine**. Viene visualizzata una pagina con il modello **predefinito di sistema** selezionato. Scegliere il modello per il quale si vuole creare un termine di glossario e selezionare **continua**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Screenshot della creazione del nuovo termine." border="true":::

3. Assegnare un nome al nuovo termine, che deve essere univoco nel catalogo. Il nome del termine fa distinzione tra maiuscole e minuscole, vale a dire che è possibile avere un termine denominato **Sample** ed **Sample** nel catalogo.

4. Aggiungere una **definizione**.

5. Impostare lo **stato** per il termine. Per impostazione predefinita, i nuovi termini hanno lo stato **bozza** .

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Screenshot delle opzioni di stato." border="true":::

   Questi indicatori di stato sono metadati associati al termine. Attualmente è possibile impostare lo stato seguente per ogni termine:

   - **Bozza**: questo termine non è ancora implementato ufficialmente.
   - **Approvato**: questo termine è ufficiale/standard/approvato.
   - **Scaduto**: questo termine non deve più essere usato.
   - **Avviso**: questo termine richiede attenzione.

6. Aggiungere **risorse** e **acronimo**. Se il termine fa parte della gerarchia, è possibile aggiungere termini padre nell' **elemento padre** nella scheda Panoramica.

7. Aggiungere **sinonimi** e **termini correlati** nella scheda correlata.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Screenshot del nuovo termine > scheda correlata." border="true":::

8. Facoltativamente, selezionare la scheda **contatti** per aggiungere esperti e amministratori al periodo di validità.

9. Selezionare **Crea** per creare il termine.

## <a name="import-terms-into-the-glossary"></a>Importare i termini nel glossario

Il Data Catalog di competenza di Azure fornisce un file template. csv che consente di importare i termini nel glossario.

È possibile importare termini nel catalogo. I termini duplicati nel file verranno sovrascritti.

Si noti che i nomi dei termini fanno distinzione tra maiuscole e minuscole. Ad esempio, `Sample` e `saMple` possono esistere entrambi nello stesso Glossario.

### <a name="to-import-terms-follow-these-steps"></a>Per importare termini, attenersi alla seguente procedura

1. Quando ci si trova nella pagina dei **termini del glossario** , selezionare **Importa termini**.

2. Verrà visualizzata la pagina modello di termine. Trovare la corrispondenza del modello del termine con il tipo di. CSV che si vuole importare.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Screenshot della pagina dei termini del glossario, pulsante Importa termini.":::

3. Scaricare il modello CSV e usarlo per immettere le condizioni che si desidera aggiungere.

   > [!Important]
   > Il sistema supporta solo l'importazione delle colonne disponibili nel modello. Il modello "System Default" avrà tutti gli attributi predefiniti.
   > Tuttavia, i modelli con termine personalizzato avranno gli attributi predefiniti e gli attributi personalizzati aggiuntivi definiti nel modello. Pertanto, il. Il file CSV è diverso dal numero totale di colonne e nomi di colonna, a seconda del termine modello selezionato. È anche possibile esaminare il file per individuare eventuali problemi dopo il caricamento.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Screenshot della pagina dei termini del glossario selezionare file da importare.":::

4. Al termine della compilazione del file con estensione CSV, selezionare il file da importare e quindi fare clic su **OK**.

5. Il sistema caricherà il file e aggiungerà tutti i termini al catalogo.

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Esporta termini dal glossario con attributi personalizzati

Si dovrebbe essere in grado di esportare i termini dal glossario, purché i termini selezionati appartengano allo stesso modello di termine.

1. Quando ci si trova nel glossario, per impostazione predefinita il pulsante **Esporta** è disabilitato. Dopo aver selezionato i termini da esportare, il pulsante **Esporta** viene abilitato se i termini selezionati appartengono allo stesso modello.

2. Selezionare **Esporta** per scaricare i termini selezionati.

 > [!Important]
   > Se i termini in una gerarchia appartengono a modelli di termini diversi, è necessario suddividerli in diversi modi. File CSV per l'importazione. L'aggiornamento di un elemento padre di un termine, inoltre, non è attualmente supportato tramite il processo di importazione.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, seguire l' [esercitazione: creare e importare termini di glossario](tutorial-import-create-glossary-terms.md) .
