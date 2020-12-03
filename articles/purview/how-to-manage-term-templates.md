---
title: Come gestire i modelli di termini per il glossario aziendale
description: Informazioni su come gestire i modelli di termini per il glossario aziendale in un catalogo dati di Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555340"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Come gestire i modelli di termini per il glossario aziendale

Azure competenza consente di creare un glossario dei termini importanti per arricchire i dati. Ogni nuovo termine aggiunto alla propria competenza Data Catalog Glossario si basa su un modello di termine che determina i campi per il termine. Questo articolo descrive come creare un modello di termine e attributi personalizzati che possono essere associati a termini di glossario.

## <a name="manage-term-templates-and-custom-attributes"></a>Gestire modelli di termini e attributi personalizzati

Usando i modelli di termini, è possibile creare attributi personalizzati, raggrupparli e applicare un modello durante la creazione di termini. Una volta creato un termine, il modello associato al termine non può essere modificato.

1. Nella pagina **termini del glossario** selezionare **Gestisci modelli di termine**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Screenshot dei termini del glossario > pulsante Gestisci modelli di termine.":::

2. La pagina mostra sia gli attributi di sistema che quelli personalizzati. Selezionare la scheda **personalizzata** per creare o modificare modelli di termini.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Screenshot dei termini di glossario > pagina Gestisci modelli di termine.":::

3. Selezionare **+ nuovo modello di termine** e immettere un nome e una descrizione per il modello.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Screenshot dei termini di glossario > gestire modelli di termini > nuovi modelli di termine":::

4. Selezionare **+ nuovo attributo** per creare un nuovo attributo personalizzato per il termine modello. Immettere un nome di attributo, Description. Il nome dell'attributo personalizzato deve essere univoco all'interno di un modello di termine, ma può essere usato per riutilizzare tutti i modelli.

   Scegliere il tipo di campo dall'elenco di opzioni **testo**, **scelta singola**, **Multichoice** o  **Data**. È anche possibile specificare una stringa di valore predefinita per i tipi di campo di testo.  L'attributo può anche essere contrassegnato come **obbligatorio**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Screenshot dei termini del glossario > pagina nuovo attributo.":::

5. Una volta creati tutti gli attributi personalizzati, selezionare **Anteprima** per disporre la sequenza di attributi personalizzati. È possibile trascinare e rilasciare attributi personalizzati nella sequenza desiderata.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Screenshot dei termini del glossario > modello di termine anteprima.":::

6. Una volta definiti tutti gli attributi personalizzati, selezionare **Crea** per creare un modello di termine con attributi personalizzati.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Screenshot dei termini del glossario > nuovo modello di termine-pulsante Crea.":::

7. Gli attributi personalizzati esistenti possono essere contrassegnati come scaduti controllando il **Contrassegno come scaduto**. Una volta scaduto, l'attributo non può essere riattivato. L'attributo scaduto verrà disattivato per i termini esistenti. I nuovi termini futuri creati con questo modello di termine non visualizzeranno più l'attributo contrassegnato come scaduto.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Screenshot dei termini di glossario > attributo Edit per contrassegnarlo come scaduto.":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, seguire l' [esercitazione: creare e importare termini di glossario](tutorial-import-create-glossary-terms.md) .
