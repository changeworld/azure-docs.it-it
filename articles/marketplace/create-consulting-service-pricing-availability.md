---
title: Come configurare i prezzi e la disponibilità di servizi di consulenza nel centro per i partner Microsoft
description: Informazioni su come configurare il servizio di consulenza Dettagli prezzi e disponibilità del mercato in Microsoft Commercial Marketplace tramite il centro per i partner.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780230"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Come configurare i prezzi e la disponibilità di servizi di consulenza

Questo articolo illustra come definire la disponibilità del mercato e i dettagli relativi ai prezzi per l'offerta del servizio di consulenza in Microsoft Commercial Marketplace.

> [!NOTE]
> Le offerte del servizio di consulenza sono disponibili solo per l'elenco. Tutte le transazioni devono essere gestite tra l'utente e i clienti all'esterno del Marketplace commerciale.

## <a name="markets"></a>Mercati

Nella sezione **Markets** selezionare i paesi o le aree in cui il servizio di consulenza sarà disponibile.

1. In **mercati** selezionare il collegamento **modifica mercati** .
2. Nella finestra di dialogo visualizzata selezionare le località di mercato in cui si desidera rendere disponibile l'offerta. È necessario selezionare un minimo di uno e un massimo di 141 mercati.
3. Selezionare **Save (Salva** ) per chiudere la finestra di dialogo.

## <a name="preview-audience"></a>Destinatari dell'anteprima

Quando si pubblica o si aggiorna l'offerta del servizio di consulenza, il centro per i partner crea una versione di anteprima dell'elenco. Questa anteprima è visibile solo agli utenti che dispongono di una **chiave Hide**.

Nel campo **Nascondi chiave** immettere una stringa alfanumerica da usare per accedere alla versione di anteprima dell'offerta.

## <a name="pricing-informational-only"></a>Prezzi (solo informativo)

Nella sezione relativa ai **prezzi** specificare se si tratta di un'offerta gratuita o a pagamento.

Per le offerte a pagamento, specificare se il prezzo è fisso o stimato. Se il prezzo è stimato, la descrizione dell'offerta deve descrivere i fattori che influiscono sul prezzo.

Se si sceglie un paese o una regione nella sezione **Markets** , specificare il prezzo in una valuta supportata per tale mercato e selezionare **Save Draft**. Per l'elenco delle valute supportate, vedere [disponibilità geografica e supporto della valuta per il Marketplace commerciale](./marketplace-geo-availability-currencies.md) .

Se si scelgono più paesi o aree geografiche nella sezione **mercati** , fornire il prezzo in Stati Uniti dollari (USD) e selezionare **Salva bozza**. Il centro per i partner convertirà il prezzo nella valuta locale di tutti i mercati selezionati usando i tassi di cambio disponibili al momento del salvataggio della bozza.

Per convalidare la conversione o impostare prezzi personalizzati in un singolo mercato, è necessario esportare, modificare e quindi importare il foglio di calcolo dei prezzi:

1. In **prezzi** selezionare il collegamento **Esporta dati sui prezzi** . Verrà scaricato un file nel dispositivo.
1. Aprire il file exportedPrice.xlsx in Microsoft Excel.
1. Nel foglio di calcolo è possibile modificare i prezzi e le valute per ogni mercato. Per l'elenco delle valute supportate, vedere [disponibilità geografica e supporto della valuta per il Marketplace commerciale](./marketplace-geo-availability-currencies.md) . Al termine, salvare il file.
1. In centro per i partner, in **prezzi**, selezionare il collegamento **Importa dati prezzi** . Se si importa il file, le informazioni sui prezzi precedenti vengono sovrascritte.

> [!IMPORTANT]
> I prezzi definiti nel centro per i partner sono statici e non seguono variazioni nei tassi di cambio. Per modificare il prezzo in uno o più mercati dopo la pubblicazione, aggiornare e inviare nuovamente l'offerta nel centro per i partner.

Prima di continuare, selezionare **Salva bozza**.

## <a name="next-steps"></a>Passaggi successivi

* [Rivedi e pubblica](review-publish-offer.md)