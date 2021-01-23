---
title: Cambiare l'offerta per la sottoscrizione di Azure
description: Informazioni su come modificare la sottoscrizione di Azure e passare a un'offerta diversa.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: banders
ms.openlocfilehash: dd8040effc5972d86e620793e437f5b185e12603
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685464"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Modificare una sottoscrizione di Azure in un'offerta diversa

I clienti con una sottoscrizione con [pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/) al consumo possono passare la sottoscrizione di Azure a un'altra offerta nel portale di Azure. È ad esempio possibile usare questa funzionalità per sfruttare il [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Per eseguire l'aggiornamento dalla versione di valutazione gratuita**, Vedere [Aggiornare la sottoscrizione](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Attività supportate:

È possibile passare da una sottoscrizione con pagamento in base al consumo a:

- [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Per altre modifiche di offerte, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="switch-subscription-offer"></a>Passare a un'offerta di sottoscrizione diversa

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **sottoscrizioni** e quindi selezionare la sottoscrizione con pagamento in base al consumo.
1. Nella parte superiore della pagina selezionare **Switch offer**. L'opzione è disponibile solo se si dispone di una sottoscrizione con pagamento in base al consumo e si è completato il primo periodo di fatturazione.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="ALTImage che mostra i dettagli della sottoscrizione con l'opzione offerta optionTEXT" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. Selezionare l'offerta desiderata dall'elenco di offerte a cui è possibile passare la sottoscrizione. L'elenco varia in base alle appartenenze associate all'account. Se non sono disponibili opzioni, controllare l'[elenco di offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi che siano disponibili le appartenenze corrette. Selezionare quindi **Avanti**.
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="Selezionare un'offerta a cui passare" lightbox="./media/switch-azure-offer/select-offer.png" :::
    A seconda dell'offerta a cui si passa, può essere visualizzata una nota relativa all'impatto del passaggio. Leggere attentamente l'elenco e seguire le istruzioni prima di continuare. Potrebbe inoltre essere necessario verificare il numero di telefono.
1. Dopo aver esaminato eventuali note o aver verificato il numero di telefono, selezionare **Cambia offerta**.
1. La sottoscrizione viene ora trasferita alla nuova offerta.

## <a name="frequently-asked-questions"></a>Domande frequenti
Le sezioni seguenti includono le risposte alle domande più frequenti.

### <a name="what-is-an-azure-offer"></a>Che cos'è un'offerta di Azure?

Per offerta di Azure si intende il *tipo* di sottoscrizione di Azure di cui si dispone. Ad esempio, [una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sono tutte offerte di Azure. Tutte le offerte presentano diversi [termini](https://azure.microsoft.com/support/legal/offer-details/) e alcune offrono vantaggi speciali. L'offerta della sottoscrizione viene visualizzata nella pagina dei dettagli della sottoscrizione.

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="Pagina dei dettagli della sottoscrizione che mostra il tipo di offerta" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-button"></a>Perché il pulsante non è visualizzato?

L'opzione opzione **offerta** potrebbe non essere visibile se:

* Non si dispone di una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Attualmente solo le sottoscrizioni con pagamento in base al consumo possono essere convertite in un'altra offerta.
  * Se si usa la [versione di valutazione gratuita](https://azure.microsoft.com/free/), vedere le informazioni relative a come [eseguire l'aggiornamento alla versione con pagamento in base al consumo](upgrade-azure-subscription.md).
  * Per cambiare offerta da una sottoscrizione diversa, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Il primo periodo di fatturazione non è ancora terminato ed è necessario attenderne il completamento prima di poter cambiare le offerte.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Perché viene visualizzato il messaggio "Non ci sono al momento offerte disponibili nella propria area geografica o nel proprio paese"?

* Potrebbe non essere consentito passare a un'altra offerta. Controllare l'[elenco delle offerte disponibili a cui è possibile passare](#whats-supported) e assicurarsi di aver attivato i vantaggi corretti con Visual Studio o BizSpark.
* Alcune offerte potrebbero non essere disponibili in tutti i paesi o aree geografiche.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Qual è l'effetto del passaggio a un'altra offerta di Azure sul servizio e sulla fatturazione?

Ecco i dettagli di ciò che accade quando si passano le offerte di Azure.

#### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

Non è previsto alcun tempo di inattività dei servizi per gli utenti associati alla sottoscrizione. Tuttavia, è possibile che l'offerta a cui si passa preveda restrizioni. Alcune offerte, ad esempio, non consentono l'uso in produzione. Sarà quindi necessario spostare le risorse di produzione a un'altra sottoscrizione.

#### <a name="quota-increases-are-reset"></a>Gli aumenti di quota vengono reimpostati

Quando si cambia offerta, tutti gli [aumenti di limite o quota al di sopra del limite predefinito](../../azure-portal/supportability/resource-manager-core-quotas-request.md) vengono reimpostati. Non ci sono tempi di inattività del servizio, anche se si hanno risorse che superano il limite predefinito. Ad esempio, se si usassero 200 core nella sottoscrizione, con il cambio di offerta la quota di core potrebbe essere reimpostata sul valore predefinito di 20 core. Le VM che usano i 200 core non sono interessate e continuano a funzionare. Tuttavia, se non si effettua un'altra richiesta di aumento di quota, non sarà possibile eseguire il provisioning di altri core.

#### <a name="billing"></a>Fatturazione

Il giorno del passaggio viene generata una fattura per tutti gli addebiti in sospeso. Gli addebiti per la sottoscrizione vengono quindi applicati in base alle condizioni tariffarie della nuova offerta. La ricorrenza di fatturazione della sottoscrizione viene modificata alla data in cui è stato eseguito il passaggio di offerte. I dati relativi all'utilizzo e alla fatturazione generati prima del passaggio a un'altra offerta non vengono conservati, quindi è consigliabile scaricarne una copia prima del passaggio.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>È possibile eseguire la migrazione da una sottoscrizione con pagamento in base al consumo a Cloud Solution Provider (CSP) o al contratto Enterprise Agreement?

* Per eseguire la migrazione a CSP, vedere [Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP](transfer-subscriptions-subscribers-csp.md).
* Per migrare la sottoscrizione esistente a un contratto Enterprise, chiedere all'amministratore dell'iscrizione di aggiungere l'account al contratto Enterprise. Seguire le istruzioni nel messaggio di posta elettronica di invito per spostare le sottoscrizioni nella registrazione EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>È possibile eseguire la migrazione di dati e servizi a una nuova sottoscrizione?

* Sì, è possibile eseguire la migrazione delle risorse direttamente alla nuova sottoscrizione. Vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Per trasferire la proprietà di una sottoscrizione di Azure e tutti gli elementi in essa contenuti a un altro utente, vedere [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Trasferimento della proprietà di una sottoscrizione di Azure)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](../costs/quick-acm-cost-analysis.md)
