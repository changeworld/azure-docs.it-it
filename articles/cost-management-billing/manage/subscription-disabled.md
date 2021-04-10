---
title: Riattivare una sottoscrizione di Azure disabilitata
description: Descrive i casi in cui la sottoscrizione potrebbe essere disabilitata e come riattivarla.
keywords: sottoscrizione di Azure disabilitata
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: 1c40102f8815cf7299a99d290b7f57b386ede401
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055128"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Riattivare una sottoscrizione di Azure disabilitata

La sottoscrizione di Azure può essere disabilitata perché il credito è scaduto, è stato raggiunto il limite di spesa, è scaduto un pagamento, è stato raggiunto il limite della carta di credito oppure la sottoscrizione è stata annullata dall'amministratore account. Identificare il problema che descrive la situazione in corso e seguire la procedura descritta in questo articolo per riattivare la sottoscrizione.

## <a name="your-credit-is-expired"></a>Il credito è scaduto

Quando ci si iscrive a un account Azure gratuito, si ottiene una sottoscrizione di valutazione gratuita, che fornisce un credito Azure di 200 USD nella valuta di fatturazione per 30 giorni e 12 mesi di servizi gratuiti. Al termine dei 30 giorni, Azure disabilita la sottoscrizione. La sottoscrizione viene disabilitata per impedire addebiti errati per un utilizzo non coperto dal credito e dai servizi gratuiti inclusi nella sottoscrizione. Per continuare a usare i servizi di Azure, è necessario [aggiornare la sottoscrizione](upgrade-azure-subscription.md). Dopo l'aggiornamento, la sottoscrizione ha ancora accesso ai servizi gratuiti per 12 mesi. Vengono addebitati solo i costi di utilizzo oltre i limiti di quantità di servizi gratuiti.

## <a name="you-reached-your-spending-limit"></a>È stato raggiunto il limite di spesa

Le sottoscrizioni di Azure con credito derivante dalla versione di prova gratuita e Visual Studio Enterprise hanno limiti di spesa, È possibile utilizzare solo i servizi fino al credito incluso. Quando l'utilizzo raggiunge il limite di spesa, Azure Disabilita la sottoscrizione per il resto del periodo di fatturazione. La sottoscrizione viene disabilitata per impedire addebiti errati per un utilizzo non coperto dal credito incluso nella sottoscrizione. Per rimuovere il limite di spesa, vedere [rimuovere il limite di spesa nel portale di Azure](spending-limit.md#remove).

> [!NOTE]
> Se si usa una sottoscrizione di valutazione gratuita e si rimuove il limite di spesa, la sottoscrizione verrà convertita in sottoscrizione individuale con pagamento in base al consumo alla fine del periodo di valutazione gratuita. Conservare il credito rimanente per 30 giorni dopo la creazione della sottoscrizione. Si dispone inoltre dell'accesso ai servizi gratuiti per 12 mesi.

Per monitorare e gestire l'attività di fatturazione per Azure, vedere [Pianificare la gestione dei costi di Azure](../understand/plan-manage-costs.md).

## <a name="your-bill-is-past-due"></a>Il saldo da pagare è scaduto

Per risolvere un saldo scaduto, vedere uno degli articoli seguenti:

- Per le sottoscrizioni del programma di sottoscrizione Microsoft online, incluso il pagamento in base al consumo, vedere [risolvere il saldo scaduto per la sottoscrizione di Azure dopo aver ricevuto un messaggio di posta elettronica da Azure](resolve-past-due-balance.md).
- Per le sottoscrizioni ai contratti con i clienti Microsoft, vedere [come pagare la fattura per Microsoft Azure](../understand/pay-bill.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>La fattura supera il limite della carta di credito

Per risolvere il problema, [passare a un'altra carta di credito](change-credit-card.md). Oppure, nel caso di un'azienda, è possibile [passare al metodo di pagamento tramite fattura](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>La sottoscrizione è stata annullata per errore

Se si è l'amministratore dell'account ed è stata annullata accidentalmente una sottoscrizione con pagamento in base al consumo, è possibile riattivarla nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a sottoscrizioni e quindi selezionare la sottoscrizione annullata.
1. Selezionare **riattiva**.
1. Confermare la riattivazione selezionando **OK**.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="Screenshot che mostra la conferma della riattivazione" :::

Per altri tipi di sottoscrizione, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per riattivare la sottoscrizione.

## <a name="after-reactivation"></a>Dopo la riattivazione

Dopo la riattivazione della sottoscrizione, si potrebbe riscontrare un ritardo nella creazione o gestione delle risorse. Se il ritardo supera i 30 minuti, contattare il supporto per la [fatturazione di Azure](https://go.microsoft.com/fwlink/?linkid=2083458) per assistenza. La maggior parte delle risorse di Azure riprende automaticamente l'esecuzione senza bisogno di alcun intervento. Tuttavia, è consigliabile controllare le risorse dei servizi di Azure ed eventualmente riavviare quelle la cui esecuzione non sia ripresa automaticamente.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [Pianificare la gestione dei costi di Azure](../understand/plan-manage-costs.md).
