---
title: Raccomandazioni di sicurezza nel Centro sicurezza di Azure
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100339"
---
# <a name="review-your-security-recommendations"></a>Esaminare le raccomandazioni sulla sicurezza

Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.

## <a name="monitor-recommendations"></a>Monitorare le raccomandazioni <a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. 

1. Dal menu del Centro sicurezza aprire la pagina **raccomandazioni** per visualizzare le raccomandazioni applicabili all'ambiente in uso. I consigli sono raggruppati in controlli di sicurezza.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Raccomandazioni raggruppate per controllo di sicurezza" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Per trovare raccomandazioni specifiche per il tipo di risorsa, la gravità, l'ambiente o altri criteri importanti, usare i filtri facoltativi sopra l'elenco di raccomandazioni.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtri per ridefinire l'elenco delle raccomandazioni del Centro sicurezza di Azure":::

1. Espandere un controllo e selezionare una raccomandazione specifica per visualizzare la pagina dei dettagli della raccomandazione.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina dei dettagli della raccomandazione." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La pagina include:

    1. Per consigli supportati, la barra degli strumenti superiore mostra uno o tutti i pulsanti seguenti:
        - **Imponi** e **Nega** (vedere [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md))
        - **Visualizzare la definizione dei criteri** per passare direttamente alla voce relativa ai criteri di Azure per i criteri sottostanti
    1. **Indicatore di gravità**
    1. **Intervallo di aggiornamento** (se pertinente)
    1. **Conteggio delle risorse esentate** se esistono esenzioni per questa raccomandazione, viene visualizzato il numero di risorse esentate
    1. **Descrizione** : breve descrizione del problema
    1. **Passaggi correttivi** -Descrizione dei passaggi manuali necessari per correggere il problema di sicurezza sulle risorse interessate. Per consigli con la correzione rapida, è possibile selezionare **Visualizza la logica di monitoraggio e aggiornamento** prima di applicare la correzione consigliata alle risorse. 
    1. **Risorse interessate** : le risorse sono raggruppate in schede:
        - **Risorse integre** : risorse rilevanti che non sono interessate o per le quali il problema è già stato risolto.
        - **Risorse non integre** : risorse ancora interessate dal problema identificato.
        - **Risorse non applicabili** : risorse per le quali la raccomandazione non può fornire una risposta definitiva. La scheda non applicabile include anche i motivi di ogni risorsa. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Risorse non applicabili con motivi.":::
    1. Pulsanti di azione per correggere la raccomandazione o attivare un'app per la logica.

## <a name="preview-recommendations"></a>Suggerimenti per l'anteprima

Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro.

È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::
 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni correlate:

- [Correggere le raccomandazioni](security-center-remediate-recommendations.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md).
- [Automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md): automatizzare le risposte alle raccomandazioni
- [Esentare una risorsa da una raccomandazione](exempt-resource.md)
- [Raccomandazioni sulla sicurezza: una guida di riferimento](recommendations-reference.md)