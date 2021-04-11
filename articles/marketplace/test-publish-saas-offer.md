---
title: Come testare e pubblicare un'offerta SaaS in Microsoft Commercial Marketplace
description: Usare il centro per i partner per inviare l'offerta SaaS all'anteprima, visualizzare un'anteprima dell'offerta, testare e quindi pubblicarla nel Marketplace commerciale Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/25/2021
ms.openlocfilehash: aeab671c9283d64f9c1ca37cf184b80b1eca8f35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045247"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Come testare e pubblicare un'offerta SaaS nel Marketplace commerciale

Questo articolo illustra come usare il centro per i partner per inviare l'offerta SaaS per la pubblicazione, visualizzare l'anteprima dell'offerta, testarla e pubblicarla nel Marketplace commerciale. È necessario avere già creato un'offerta che si desidera pubblicare.

> [!NOTE]
> È consigliabile creare un'offerta di test e sviluppo (DEV) separata come metodo a basso rischio per eseguire test prima di pubblicare l'offerta di produzione (PROD). Seguire questa procedura per creare e testare l'offerta DEV prima di pubblicare l'offerta (PROD).

## <a name="submit-your-offer-for-publishing"></a>Invia l'offerta per la pubblicazione

1. Accedere al dashboard del Marketplace commerciale nel centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Nella pagina **Panoramica** selezionare l'offerta che si desidera pubblicare.
1. Nell'angolo in alto a destra del portale selezionare **revisione e pubblicazione**.
2. Verificare che nella colonna **stato** per ogni pagina sia stato **completato**. I tre possibili stati sono i seguenti:

   - **Non avviato** : la pagina è incompleta.
   - **Incompleto** : nella pagina mancano le informazioni necessarie o sono presenti errori che devono essere corretti. È necessario tornare alla pagina e aggiornarla.
   - **Completato** : la pagina è stata completata. Sono stati forniti tutti i dati necessari e non sono presenti errori.

1. Se una delle pagine presenta uno stato diverso da **completo**, selezionare il nome della pagina, correggere il problema, salvare la pagina, quindi selezionare **Verifica e pubblica** di nuovo per tornare a questa pagina.
1. Al termine di tutte le pagine, nella casella **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente. Fornire eventuali note supplementari utili per comprendere l'app.
1. Per avviare il processo di pubblicazione per l'offerta, selezionare **pubblica**. Viene visualizzata la pagina **Panoramica dell'offerta** che mostra lo **stato di pubblicazione** dell'offerta.

Lo stato di pubblicazione dell'offerta cambierà durante il processo di pubblicazione. Per informazioni dettagliate su questo processo, vedere la pagina relativa alla [procedura di convalida e pubblicazione](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Anteprima e test dell'offerta

Quando l'offerta è pronta per la disconnessione, ti invieremo un messaggio di posta elettronica per richiedere la verifica e l'approvazione dell'anteprima dell'offerta. È anche possibile aggiornare la pagina **Panoramica dell'offerta** nel browser per verificare se l'offerta ha raggiunto la fase di approvazione dell'editore. In caso contrario, saranno disponibili i collegamenti pulsante **Vai in diretta** e anteprima. Sarà disponibile un collegamento per Microsoft AppSource Preview, l'anteprima di Azure Marketplace o entrambi a seconda delle opzioni selezionate durante la creazione dell'offerta. Se si sceglie di vendere l'offerta tramite Microsoft, tutti gli utenti che sono stati aggiunti ai destinatari dell'anteprima possono testare l'acquisizione e la distribuzione dell'offerta per garantire che soddisfi i requisiti durante questa fase.

Lo screenshot seguente mostra la pagina di **Panoramica dell'offerta** per un'offerta SaaS, con due collegamenti di anteprima nel pulsante **Go Live** . I passaggi di convalida visualizzati in questa pagina variano a seconda delle selezioni effettuate durante la creazione dell'offerta.

![Viene illustrata la pagina di panoramica dell'offerta per un'offerta nel centro per i partner. Vengono visualizzati il pulsante Go Live e i collegamenti di anteprima. Il collegamento Visualizza report di convalida viene visualizzato anche in convalida automatica.](./media/review-publish-offer/publish-status-saas.png)

Per visualizzare in anteprima l'offerta, seguire questa procedura.

1. Nella pagina **Panoramica dell'offerta** selezionare un collegamento anteprima nel pulsante **Vai in diretta** .

1. Per convalidare il flusso di acquisto e di installazione end-to-end, acquistare i piani nell'offerta mentre è in anteprima. Per prima cosa, inviare una notifica a Microsoft con un [ticket di supporto](https://aka.ms/marketplacesupport) per assicurarsi che non venga elaborato alcun addebito.

1. Se l'offerta SaaS supporta la [fatturazione a consumo usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md), esaminare e seguire le procedure consigliate di test descritte in dettaglio nelle API per la [fatturazione a consumo del Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Esaminare e seguire le istruzioni di test nelle [API di evasione Saas versione 2 di Microsoft Commercial Marketplace](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) per assicurarsi che l'offerta sia stata integrata correttamente con le API prima di pubblicare l'offerta in tempo reale.

1. Se il passaggio di convalida dell'offerta ha generato avvisi, nella pagina **Panoramica dell'offerta** viene visualizzato un collegamento **Visualizza report di convalida** . Assicurarsi di esaminare il report e risolvere i problemi prima di selezionare il pulsante **Go Live** . In caso contrario, la certificazione avrà probabilmente esito negativo e il ritardo dell'offerta potrebbe non riuscire.

1. Se è necessario apportare modifiche dopo la visualizzazione in anteprima e il test dell'offerta, è possibile modificare e inviare di nuovo per pubblicare una nuova anteprima. Per ulteriori informazioni, vedere [aggiornare un'offerta esistente nel Marketplace commerciale](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Pubblica l'offerta in tempo reale

Dopo aver completato tutti i test nell'anteprima, selezionare **Go Live** per pubblicare l'offerta in diretta nel Marketplace commerciale. Se l'offerta è già presente nel Marketplace commerciale, gli eventuali aggiornamenti non verranno attivati fino a quando non si seleziona **Go Live**.

> [!IMPORTANT]
> Non selezionare mai **Live** per un' [offerta di sviluppo/test](create-saas-dev-test-offer.md).

Ora che si è scelto di rendere disponibile l'offerta nel Marketplace commerciale, viene eseguita una serie di controlli di convalida finali per assicurarsi che l'offerta Live sia configurata esattamente come la versione di anteprima dell'offerta. Per informazioni dettagliate su questi controlli di convalida, vedere [fase di pubblicazione](review-publish-offer.md#publish-phase).

Al termine di questi controlli di convalida, l'offerta sarà disponibile nel Marketplace.

## <a name="next-steps"></a>Passaggi successivi

- [Accedere ai report analitici per il Marketplace commerciale nel centro per i partner](./partner-center-portal/analytics.md)
