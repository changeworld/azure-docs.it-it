---
title: Raccomandazioni per le prenotazioni di Azure
description: Informazioni sulle raccomandazioni per le prenotazioni di Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767926"
---
# <a name="reservation-recommendations"></a>Consigli sulle prenotazioni

Le raccomandazioni per l'acquisto di istanze riservate di Azure vengono fornite tramite l'[API delle raccomandazioni per le prenotazioni](/rest/api/consumption/reservationrecommendations) di Consumo di Azure, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e tramite l'esperienza di acquisto delle prenotazioni nel portale di Azure.

Nei passaggi seguenti viene definita la modalità di calcolo delle raccomandazioni:

1. Il motore di raccomandazione valuta l'utilizzo orario per le risorse nell'ambito specificato negli ultimi 7, 30 e 60 giorni.
2. In base ai dati di utilizzo, il motore simula i costi con e senza prenotazioni.
3. Vengono simulati i costi per le diverse quantità e viene consigliata la quantità che consente di ottenere il massimo risparmio.
4. Se le risorse vengono arrestate regolarmente, con la simulazione non verrà individuato alcun risparmio e non verrà fornita alcuna raccomandazione di acquisto.
5. I calcoli delle raccomandazioni includono eventuali sconti speciali sui tassi di utilizzo su richiesta.

## <a name="recommendations-in-the-azure-portal"></a>Raccomandazioni nel portale di Azure

Le raccomandazioni per l'acquisto di prenotazioni sono illustrate anche nell'esperienza di acquisto del portale di Azure. Le raccomandazioni sono contrassegnate con la dicitura **Quantità raccomandata**. All'acquisto, la quantità raccomandata da Azure garantirà il massimo risparmio possibile. Anche se è possibile acquistare qualsiasi quantità, se si acquista una quantità diversa il risparmio non sarà ottimale.

Di seguito sono riportati alcuni esempi che spiegano il motivo.

Nell'immagine di esempio seguente relativa alla raccomandazione selezionata, Azure raccomanda l'acquisto di una quantità pari a 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Esempio di raccomandazione per l'acquisto di prenotazioni" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Quando si seleziona Visualizza dettagli, vengono visualizzate altre informazioni sulla **raccomandazione.** L'immagine seguente mostra i dettagli relativi alla raccomandazione. La quantità consigliata viene calcolata per il massimo utilizzo possibile e si basa sull'utilizzo cronologico. Se l'utilizzo non è uniforme, è possibile che la raccomandazione non indichi un utilizzo al 100%. Nell'esempio si noti che l'utilizzo ha fluttuato nel tempo. Sono visualizzati il costo della prenotazione, il possibile risparmio e la percentuale di utilizzo.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Esempio che mostra i dettagli per una raccomandazione di acquisto della prenotazione " :::

Il grafico e i valori stimati cambiano quando si aumenta la quantità consigliata. Aumentando la quantità di prenotazioni, i risparmi si ridurranno perché si finirà con un uso ridotto della prenotazione. In altre parole, si pagherà per le prenotazioni che non sono completamente utilizzate.

Se si riduce la quantità di prenotazione, si ridurranno anche i risparmi. Anche se l'utilizzo aumenta, probabilmente si presenteranno periodi in cui le prenotazioni non riusciranno a coprire completamente l'utilizzo. L'utilizzo oltre la quantità delle prenotazioni sarà soddisfatto da risorse più costose con pagamento in base al consumo. Nell'immagine di esempio seguente viene illustrato questo aspetto. La quantità della prenotazione è stata ridotta manualmente a 4. L'utilizzo della prenotazione viene aumentato, ma i risparmi complessivi vengono ridotti perché sono presenti costi con pagamento in base al costo.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Esempio che mostra i dettagli modificati di una raccomandazione per l'acquisto di prenotazioni" :::

Per ottimizzare i risparmi con le prenotazioni, provare ad acquistare prenotazioni che siano più vicine possibile alla quantità della raccomandazione.

## <a name="recommendations-in-azure-advisor"></a>Raccomandazioni in Azure Advisor

Le raccomandazioni per l'acquisto di prenotazioni sono disponibili in Azure Advisor. Tenere presente quanto segue:

- In Advisor sono disponibili solo le raccomandazioni relative a una singola sottoscrizione. Per visualizzare le raccomandazioni per l'intero ambito di fatturazione (account di fatturazione o profilo di fatturazione), eseguire le seguenti attività:
  -  Nel portale di Azure, passare a **Prenotazioni** Aggiungi e quindi selezionare il tipo per cui  >   si vogliono visualizzare le raccomandazioni.
- Le raccomandazioni disponibili in Advisor considerano la tendenza di utilizzo degli ultimi 30 giorni.
- La quantità e i risparmi consigliati sono per una prenotazione di tre anni, se disponibile. Se non viene venduta una prenotazione di tre anni per il servizio, la raccomandazione viene calcolata usando il prezzo della prenotazione di un anno.
- I calcoli delle raccomandazioni includono eventuali sconti speciali sui tassi di utilizzo su richiesta.
- Se si acquista una prenotazione con ambito condiviso, le raccomandazioni per l'acquisto della prenotazione di Advisor possono richiedere fino a cinque giorni.

## <a name="other-expected-api-behavior"></a>Altro comportamento previsto dell'API

- Con un periodo analizzato di sette giorni si potrebbero non ottenere suggerimenti in caso di arresto delle macchine virtuali per più di un giorno.

## <a name="next-steps"></a>Passaggi successivi
- Ottenere [raccomandazioni sulla prenotazione usando le API REST](/rest/api/consumption/reservationrecommendations/list).
- Informazioni su [come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali](../manage/understand-vm-reservation-charges.md).
