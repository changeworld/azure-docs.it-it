---
title: Applicare le classificazioni agli asset (anteprima)
description: Questo documento descrive come applicare le classificazioni agli asset.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96554872"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Applicare le classificazioni agli asset in Azure

Questo articolo illustra come applicare le classificazioni agli asset.

## <a name="introduction"></a>Introduzione

Le classificazioni possono essere di tipo sistema o personalizzato. Per impostazione predefinita, le classificazioni di sistema sono presenti in competenza. È possibile creare classificazioni personalizzate in base a un modello di espressione regolare. Le classificazioni possono essere applicate alle risorse in modo automatico o manuale.

Questo documento illustra come applicare le classificazioni ai dati.

## <a name="prerequisites"></a>Prerequisiti

- Creare classificazioni personalizzate in base alle esigenze.
- Configurare l'analisi sulle origini dati.

## <a name="apply-classifications"></a>Applicare classificazioni
In Azure è possibile applicare classificazioni di sistema o personalizzate a un asset di file, tabelle o colonne. Questo articolo descrive i passaggi per applicare manualmente le classificazioni agli asset.

### <a name="apply-classification-to-a-file-asset"></a>Applicare la classificazione a un asset di file
Azure competenza può analizzare e classificare automaticamente i file di documentazione. Se, ad esempio, si dispone di un file denominato *multiple.docx* e di un numero di ID nazionale nel contenuto, Azure competenza aggiunge la classificazione **numero di identificazione nazionale UE** alla pagina dei dettagli dell'asset di file.

In alcuni scenari potrebbe essere necessario aggiungere manualmente le classificazioni all'asset di file. Se sono presenti più file raggruppati in un set di risorse, aggiungere le classificazioni a livello di set di risorse.

Per aggiungere una classificazione personalizzata o di sistema a un set di risorse di partizione, attenersi alla procedura seguente:

1. Cercare o esplorare la partizione e passare alla pagina dei dettagli dell'asset.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Screenshot che mostra la pagina dei dettagli dell'asset.":::

1. Nella scheda **Panoramica** visualizzare la sezione **classificazioni** per verificare se sono presenti classificazioni esistenti. Selezionare **Modifica**.

1. Dall'elenco a discesa **classificazioni** selezionare le classificazioni specifiche a cui si è interessati. Ad esempio, il **numero di carta di credito**, ovvero una classificazione di sistema e **CustomerAccountID**, che è una classificazione personalizzata.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Screenshot che illustra come selezionare le classificazioni da aggiungere a un asset.":::

1. Selezionare **Salva**

1. Nella scheda **Panoramica** verificare che le classificazioni selezionate siano visualizzate nella sezione **classificazioni** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Screenshot che illustra come verificare che le classificazioni siano state aggiunte a un asset.":::

### <a name="apply-classification-to-a-table-asset"></a>Applicare la classificazione a un asset tabella

Quando Azure competenza analizza le origini dati, non assegna automaticamente le classificazioni agli asset della tabella. Se si vuole che l'asset della tabella disponga di una classificazione, è necessario aggiungerla manualmente.

Per aggiungere una classificazione a un asset tabella:

1. Trovare un asset tabella a cui si è interessati. Ad esempio, **Customer** Table.

1. Verificare che alla tabella non siano assegnate classificazioni. Selezionare **Modifica**.

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Screenshot che illustra come visualizzare e modificare le classificazioni di un asset tabella.":::

1. Dall'elenco a discesa **classificazioni** selezionare una o più classificazioni. Questo esempio usa una classificazione personalizzata denominata **CustomerInfo**, ma è possibile selezionare tutte le classificazioni per questo passaggio.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Screenshot che illustra come selezionare le classificazioni da aggiungere a un asset tabella.":::

1. Selezionare **Save (Salva** ) per salvare le classificazioni.

1. Nella pagina **Overview (panoramica** ) verificare che Azure competenza abbia aggiunto le nuove classificazioni.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Screenshot che illustra come verificare che le classificazioni siano state aggiunte a un asset tabella.":::

### <a name="add-classification-to-a-column-asset"></a>Aggiungere la classificazione a un asset della colonna

Azure competenze analizza e aggiunge automaticamente le classificazioni a tutti gli asset delle colonne. Tuttavia, se si desidera modificare la classificazione, è possibile eseguire questa operazione a livello di colonna.

Per aggiungere una classificazione a una colonna:

1. Trovare e selezionare l'asset della colonna e quindi scegliere **modifica** dalla scheda **Panoramica** .

1. Selezionare la scheda **schema**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Screenshot che illustra come modificare lo schema di una colonna.":::

1. Identificare le colonne a cui si è interessati e selezionare **Aggiungi una classificazione**. In questo esempio viene aggiunta una classificazione di **password comuni** alla colonna **PasswordHash** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Screenshot che illustra come aggiungere una classificazione a una colonna.":::

1. Selezionare **Salva**

1. Selezionare la scheda **schema** e verificare che la classificazione sia stata aggiunta alla colonna.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Screenshot che illustra come confermare che una classificazione è stata aggiunta a uno schema di colonna.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Effetti della ripetizione dell'analisi sulle classificazioni esistenti

Le classificazioni vengono applicate la prima volta, in base al controllo set di esempio sui dati e alla corrispondenza con il modello Regex set. Al momento della ripetizione dell'analisi, se si applicano nuove classificazioni, la colonna otterrà ulteriori classificazioni. Le classificazioni esistenti vengono mantenute nella colonna ed è necessario rimuoverle manualmente.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come creare una classificazione personalizzata, vedere [creare una classificazione personalizzata](create-a-custom-classification-and-classification-rule.md).