---
title: Salva i costi di calcolo con la capacità riservata
description: Informazioni su come acquistare Azure Data Factory capacità riservata del flusso di dati per risparmiare sui costi di calcolo.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 26a4692603d8e8a80a52ea77bdd56617131cea5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593901"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Risparmiare sui costi per le risorse con capacità riservata: flussi di dati Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Risparmia denaro con Azure Data Factory costi del flusso di dati eseguendo il commit di una prenotazione per le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata, si impegna per l'utilizzo del flusso di dati ADF per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la capacità riservata, è necessario specificare l'area di Azure, il tipo di calcolo, il numero di core e il termine.

Non è necessario assegnare la prenotazione a una factory o a un runtime di integrazione specifico. Le fabbriche esistenti o le nuove Factory distribuite ottengono automaticamente il vantaggio. Con l'acquisto di una prenotazione, viene eseguito il commit dell'utilizzo per i costi di calcolo del flusso di dati per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo che corrispondono agli attributi di prenotazione non vengono più addebitati in base alle tariffe con pagamento in base al consumo. 

È possibile acquistare [capacità riservata](https://portal.azure.com) scegliendo prenotazioni [in anticipo o con pagamenti mensili](../cost-management-billing/reservations/prepare-buy-reservation.md). Per acquistare capacità riservata:

- È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise. Capacità riservata.

Per altre informazioni su come i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati per gli acquisti di prenotazione, vedere [informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> L'acquisto di capacità riservata non consente di pre-allocare o riservare risorse di infrastruttura specifiche (macchine virtuali o cluster) per l'utilizzo.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Determinare le dimensioni Azure IR corrette necessarie prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di risorse di calcolo usate dai flussi di dati esistenti o presto distribuiti usando lo stesso livello di calcolo.

Si supponga, ad esempio, di eseguire una pipeline ogni ora usando l'ottimizzazione per la memoria con 32 core. Si supponga inoltre di pianificare la distribuzione entro il prossimo mese di una pipeline aggiuntiva che usa i core 64 per utilizzo generico. Si supponga poi di sapere che queste risorse saranno necessarie per almeno 1 anno. In questo caso, immettere il numero di core necessari per ogni tipo di calcolo per un'ora. Nel portale di Azure cercare prenotazioni. Scegliere Data Factory > flussi di dati, quindi immettere 32 per l'ottimizzazione per la memoria e 64 per uso generico.

## <a name="buy-reserved-capacity"></a>Acquistare la capacità riservata

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare **Aggiungi** e quindi nel riquadro **prenotazioni acquisti** selezionare flussi di **dati ADF** per acquistare una nuova prenotazione per i flussi di dati di ADF.
4. Immettere gli attributi e i campi obbligatori selezionati per ottenere lo sconto per la capacità riservata. Il numero effettivo di flussi di dati che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.
5. Esaminare il costo della prenotazione di capacità nella sezione **costi** .
6. Selezionare **Acquisto**.
7. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Comprendere lo sconto sulle prenotazioni di Azure](data-flow-understand-reservation-charges.md)
