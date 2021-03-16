---
title: Come usare i tag di tabella per eseguire il training del sistema di riconoscimento del modulo personalizzato
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare in modo efficace le etichette con tag di tabella con supervisione.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467860"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Usare i tag di tabella per eseguire il training del modello di modulo personalizzato

In questo articolo si apprenderà come eseguire il training del modello di modulo personalizzato con tag di tabella (etichette). Per alcuni scenari è necessaria un'etichetta più complessa rispetto alla semplice allineamento delle coppie chiave-valore. Questi scenari includono l'estrazione di informazioni da moduli con strutture gerarchiche complesse o la presenza di elementi che non vengono rilevati ed estratti automaticamente dal servizio. In questi casi, è possibile usare i tag Table per eseguire il training del modello di modulo personalizzato.

## <a name="when-should-i-use-table-tags"></a>Quando è necessario usare I tag della tabella?

Di seguito sono riportati alcuni esempi di quando si usano i tag di tabella:

- I dati che si desidera estrarre vengono presentati come tabelle nei form e la struttura delle tabelle è significativa. Ogni riga della tabella rappresenta ad esempio un elemento e ogni colonna della riga rappresenta una funzionalità specifica di tale elemento. In questo caso, è possibile utilizzare un tag di tabella in cui una colonna rappresenta le funzionalità e una riga rappresenta le informazioni su ogni funzionalità.
- I dati che si desidera estrarre non vengono presentati in campi di modulo specifici ma semanticamente, i dati possono essere inseriti in una griglia bidimensionale. Ad esempio, il form include un elenco di persone e include un nome, un cognome e un indirizzo di posta elettronica. Si desidera estrarre queste informazioni. In questo caso, è possibile usare un tag table con nome, cognome e indirizzo di posta elettronica come colonne e ogni riga viene popolata con le informazioni relative a una persona dell'elenco.

> [!NOTE]
> Il riconoscimento del modulo trova ed estrae automaticamente tutte le tabelle nei documenti, indipendentemente dal fatto che le tabelle siano contrassegnate o meno. Non è quindi necessario etichettare ogni tabella dal form con un tag Table e i tag della tabella non devono replicare la struttura di una tabella molto trovata nel form. Le tabelle estratte automaticamente dal riconoscimento modulo verranno incluse nella sezione pageResults dell'output JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Creare un tag table con lo strumento di test OCR (FOTT)
<!-- markdownlint-disable MD004 -->
* Determinare se si desidera un tag di tabella **dinamico** o a **dimensione fissa** . Se il numero di righe varia da documento a documento, usare un tag di tabella dinamica. Se il numero di righe è coerente tra i documenti, usare un tag di tabella a dimensione fissa.
* Se il tag Table è dinamico, definire i nomi delle colonne e il tipo di dati e il formato per ogni colonna.
* Se la tabella è a dimensione fissa, definire il nome della colonna, il nome della riga, il tipo di dati e il formato per ogni tag.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Configurare un tag di tabella":::

## <a name="label-your-table-tag-data"></a>Etichettare i dati dei tag di tabella

* Se il progetto include un tag di tabella, è possibile aprire il pannello di etichette e popolare il tag come si farebbe per l'etichetta dei campi chiave-valore.
:::image type="content" source="media/table-labeling.png" alt-text="Etichetta con tag di tabella":::

## <a name="next-steps"></a>Passaggi successivi

Seguire la Guida introduttiva per eseguire il training e usare il modello di riconoscimento del modulo personalizzato:

> [!div class="nextstepaction"]
> [Eseguire il training con le etichette usando lo strumento di assegnazione di etichette di esempio](quickstarts/label-tool.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](overview.md)
>
