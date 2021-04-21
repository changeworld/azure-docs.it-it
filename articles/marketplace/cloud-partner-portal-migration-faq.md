---
title: Domande frequenti sulla transizione dal portale Cloud Partner a Partner Center - Marketplace commerciale Microsoft
description: Risposte alle domande frequenti sulla transizione delle offerte da portale Cloud Partner a Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0a45e7c0479cf490ad8688230897ae89ce4d1020
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819125"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Domande frequenti sulla transizione dal portale Cloud Partner a Partner Center

La portale Cloud Partner è passata a Partner Center. Partner Center offre un'esperienza semplificata e integrata per la pubblicazione di nuove offerte [in Microsoft AppSource](https://appsource.microsoft.com/) o [Azure Marketplace](https://azuremarketplace.microsoft.com/) e per la gestione delle offerte esistenti di cui è stata eseguita la migrazione dal portale Cloud Partner. Tutte le funzioni del portale Cloud Partner sono disponibili in Partner Center. Questo articolo risolve le domande frequenti su questo problema.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Che cosa significa la transizione Partner Center per me?

È possibile continuare a lavorare in Partner Center:

| Area | Modifiche |
| --- | --- |
| Account | Non è necessario creare un nuovo account Partner Center account. È possibile usare le credenziali portale Cloud Partner esistenti per accedere Partner Center in cui si gestiranno ora l'account, gli utenti, le autorizzazioni e la fatturazione. Il contratto di pubblicazione e le informazioni sul profilo aziendale vengono migrati al nuovo account Partner Center, insieme alle informazioni sul profilo di pagamento, agli account utente e alle autorizzazioni e alle offerte attive. Per altre informazioni, [vedere Gestire l'account del marketplace](manage-account.md)commerciale in Partner Center . |
| Esperienza di pubblicazione e gestione delle offerte | I dati dell'offerta sono stati spostati dal portale Cloud Partner a Partner Center. È ora possibile accedere alle offerte in Partner Center, che offre un'esperienza utente migliorata e un'interfaccia intuitiva. Informazioni su come [aggiornare un'offerta esistente nel marketplace commerciale.](partner-center-portal/update-existing-offer.md) |
| Disponibilità delle offerte nel marketplace commerciale | Nessuna modifica. Se l'offerta è disponibile nel marketplace commerciale, continuerà a essere live. |
| Nuovi acquisti e distribuzioni | Nessuna modifica. I clienti possono continuare ad acquistare e distribuire le offerte senza interruzioni. |
| Proventi | Qualsiasi acquisto e distribuzione continuerà a essere pagato normalmente. Altre informazioni su [Come ottenere i pagamenti nel marketplace commerciale.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| Integrazioni di API con [API del portale Cloud Partner esistenti](cloud-partner-portal-api-overview.md) | Le PORTALE CLOUD PARTNER esistenti sono ancora supportate e le integrazioni esistenti continuano a funzionare. Per altre [informazioni, vedere Portale Cloud Partner le API REST saranno supportate?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analisi | È possibile continuare a monitorare le vendite, valutare le prestazioni e ottimizzare le offerte nel marketplace commerciale visualizzando le analisi in Partner Center. Esistono differenze tra la modalità di visualizzazione dei report di analisi in CPP e Partner Center. Ad esempio, **Informazioni dettagliate per il venditore** in CPP ha una scheda Orders & Usage (Utilizzo di **Orders &)** che visualizza i dati per le offerte basate sull'utilizzo e le offerte non basate sull'utilizzo, mentre Partner Center nella pagina **Orders** (Ordini) è presente una scheda separata per le offerte SaaS. Per altre informazioni, vedere [Accedere ai report analitici per il marketplace commerciale in Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>È necessario creare un nuovo account per gestire le offerte in Partner Center?

No, l'account verrà mantenuto. Ciò significa che se si è un partner esistente, è possibile usare le credenziali dell'account portale Cloud Partner esistente per accedere a Partner Center. Non creare un nuovo account o le offerte create in portale Cloud Partner e spostate in Partner Center non verranno associate.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quali pagine del Centro per i partner corrispondono alle pagine usate nel portale Cloud Partner?

Di seguito sono Partner Center per le pagine comunemente usate nel portale Cloud Partner. Se i collegamenti portale Cloud Partner sono stati salvati come segnalibri, è necessario aggiornarli.

| Pagina del portale per Cloud Partner | portale Cloud Partner collegamento alla pagina | Partner Center collegamento alla pagina |
| --- | --- | --- |
| Pagina Tutte le offerte | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Pagina Tutti gli autori | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Profilo di pubblicazione | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Pagina Users (Utenti) | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Pagina Cronologia | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Il Centro per i partner non supporta ancora la funzionalità Cronologia. |
| Dashboard Informazioni cognitive dettagliate | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Report sui proventi | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Differenze del report sui proventi

Queste sono le differenze nel report dei pagamenti tra il portale Cloud Partner ritirato e l'Partner Center:

| Portale per Cloud Partner | Centro per i partner |
| --- | --- |
| **Link**: https://cloudpartner.azure.com/ | **Link**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation**: i report sui proventi sono disponibili in Insights Proventi | **Navigation**: i report sui proventi sono disponibili nel Centro per i partner - icona Proventi |
| **Ambito**:<ul><li>È visibile la transazione per ogni voce per le riscossioni in corso, incassate e pagate.</li><li>Report: tutte le voci vengono visualizzate dopo la creazione dell'ordine d'acquisto, incluse le riscossioni e le fatturazioni in corso, lo stato della riscossione e le voci non ancora idonee per il pagamento.</li></ul> | **Ambito**:<ul><li>Visualizza le voci dopo che sono state dichiarate redditi idonei.</li><li>I clienti eseguono prima il pagamento a favore di Microsoft e quindi i fornitori di software indipendenti possono iniziare a vedere il report sui proventi.</li><li>Nel report sui proventi non vengono visualizzate le riscossioni e le fatturazioni in corso.</li></ul> |
| **La transazione non è pronta per ricevere i proventi**: fatturazione in corso | **La transazione non è pronta per ricevere i proventi**: pagamento successivo stimato: i proventi sono nello stato non elaborato. |
| **Stato dei proventi**: n/d | **Stato dei proventi**:<ul><li>Non elaborato: il reddito è idoneo al pagamento.</li><li>Imminente: il reddito verrà inviato all'editore nei proventi mensili successivi.</li><li>Inviato: il pagamento è stato inviato alla banca.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Quali sono le offerte pubblicate nel portale Cloud Partner?

Le offerte sono state spostate in Partner Center e saranno accessibili dopo l'accesso a Partner Center, ad eccezione delle offerte di Dynamics Nav Managed Service e Cortana Intelligence. Se l'offerta è stata distribuita nel marketplace commerciale, continuerà a essere in tempo reale e i clienti continueranno ad acquistarla e distribuirla senza interruzioni. Per altri dettagli, vedere la domanda successiva Quali offerte sono state **spostate in Partner Center?**.

## <a name="what-offers-were-moved-to-partner-center"></a>Quali offerte sono state spostate Partner Center?

Tutti i tipi di offerta supportati in precedenza nel portale Cloud Partner sono supportati in Partner Center, ad eccezione delle offerte del servizio gestito Dynamics Nav e di Cortana Intelligence.

Per i tipi di offerta supportati in Partner Center, tutte le offerte sono state spostate indipendentemente dal relativo stato. Anche le offerte draft, de-listed e preview-only sono spostate.

| Tipo di offerta | Spostato in Partner Center? | Passaggi successivi |
| --- | --- | --- |
| SaaS | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Pianificare un'offerta SaaS per il marketplace commerciale.](plan-saas-offer.md) |
| Macchina virtuale | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, [vedere Pianificare un'offerta di macchina virtuale.](marketplace-virtual-machines.md) |
| Applicazione Azure | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, [vedere Creare un'offerta di applicazione Azure.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, [vedere Creare un'offerta Dynamics 365 Business Central.](partner-center-portal/create-new-business-central-offer.md) |
| Dynamics 365 for Customer Engagement & PowerApps | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Creare un'offerta Dynamics 365 for Customer Engagement & PowerApps.](dynamics-365-customer-engage-offer-setup.md) |
| Dynamics 365 for Operations | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Creare un'offerta Dynamics 365 for Operations.](partner-center-portal/create-new-operations-offer.md) |
| App Power BI | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Creare un Power BI app per AppSource.](partner-center-portal/create-power-bi-app-offer.md) |
| Modulo IoT Edge | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Creare, configurare e pubblicare un'offerta IoT Edge modulo in Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Contenitore | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, [vedere Creare un'offerta di contenitore di Azure.](./create-azure-container-offer.md) |
| Servizio di consulenza | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre informazioni, [vedere Creare un'offerta di servizio di consulenza](./create-consulting-service-offer.md). |
| Servizio gestito | Sì | Accedere a Partner Center creare nuove offerte e gestire le offerte create in portale Cloud Partner. Per altre [informazioni, vedere Creare un'offerta di servizio gestito](./plan-managed-service-offer.md). |
| Servizio gestito Dynamics Nav | No | Microsoft ha evoluto il servizio gestito dynamics NAV in [Dynamics 365 Business Central,](/dynamics365/business-central/)quindi le offerte dinamiche del servizio gestito dynamics NAV sono elencate da AppSource. Queste offerte non sono più individuabili dai clienti e non sono state spostate in Partner Center. Per rendere disponibili le offerte in AppSource, adattarle alle offerte di Dynamics 365 Business Central e inviarle in [Partner Center](https://partner.microsoft.com/). Per altre [informazioni, vedere Creare un'offerta Dynamics 365 Business Central.](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | No | Microsoft ha evoluto la road map del prodotto per Cortana Intelligence, quindi le offerte live di Cortana Intelligence sono stati de-elencate da AppSource. Queste offerte non sono più individuabili dai clienti e non sono state spostate in Partner Center. Per rendere disponibili le offerte nel marketplace commerciale, adattare le offerte alle offerte SaaS (Software as a Service) e inviarle in [Partner Center](https://partner.microsoft.com/). Per altre informazioni, vedere Elenco di controllo per la creazione [di offerte SaaS in Partner Center](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Non è possibile trovare le offerte portale Cloud Partner in Partner Center

Ciò che viene visualizzato in Partner Center dipende dai programmi a cui si è registrati, dagli account a cui si appartiene e dai ruoli utente e dalle autorizzazioni assegnati. Sono disponibili Partner Center diversi programmi ed è possibile che l'utente sia registrato in più programmi. È anche possibile avere accesso a più account con le stesse credenziali utente.

Le offerte create in portale Cloud Partner sono disponibili in Partner Center nel programma **Marketplace** commerciale e nell'account usato per creare le offerte. Per assicurarsi di visualizzare il programma giusto e l'account giusto, seguire questa procedura. Per altri suggerimenti sulla risoluzione dei problemi, vedere [Gestire l'account Partner Center servizio.](/partner-center/partner-center-account-setup)

### <a name="access-the-right-program-in-partner-center"></a>Accedere al programma più Partner Center

1. Accedere al [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) con le stesse credenziali usate per accedere al portale Cloud Partner. Nel riquadro di spostamento a sinistra vengono visualizzate le opzioni associate ai programmi registrati.

    Esempio: si supponga di avere accesso a tre programmi: il programma MPN, il programma Segnalazioni e il programma Marketplace commerciale. Quando si accede Partner Center, questi tre programmi verranno visualizzati nel riquadro di spostamento.

2. Selezionare **Panoramica del marketplace**  >  **commerciale** per accedere alle offerte.

    Se il programma Marketplace commerciale non viene visualizzato nel riquadro di spostamento a sinistra, è possibile che l'account non sia corretto. Seguire i passaggi nella sezione successiva per accedere all'account giusto.

    [![Screenshot che mostra il menu Partner Center Panoramica](media/cpp-pc-faq/overview-menu.png "Mostra il menu Partner Center panoramica")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Accedere all'account giusto in Partner Center

Se si fa parte di più account, nel Centro per i partner verrà visualizzato un pulsante di selezione account contrassegnato da due frecce nel menu di spostamento a sinistra. Selezionare il pulsante di selezione account per visualizzare un elenco di tutti gli account a cui si appartiene. Selezionare un account nell'elenco per passare a esso e visualizzare tutti i programmi e le informazioni relative a tale account. Se non viene visualizzato un pulsante di selezione account nel menu di spostamento, si è membri di un singolo account.

[![Screenshot che mostra il Partner Center selezione account.](media/cpp-pc-faq/picker-button.png "Mostra il pulsante Partner Center selezione account")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Ricerca per categorie creare nuove offerte?

Accedere al programma Marketplace commerciale in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per creare nuove offerte. Nella pagina Panoramica selezionare **+ Nuova offerta**.

[![Screenshot che mostra il Partner Center panoramica.](media/cpp-pc-faq/new-offer.png "Mostra il menu Partner Center panoramica")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Non è possibile accedere ed è necessario aprire un ticket di supporto

Se non è possibile accedere al proprio account, è possibile aprire un [ticket di supporto](https://partner.microsoft.com/support/v2/?stage=1) qui.

## <a name="where-are-instructions-for-using-partner-center"></a>Dove sono le istruzioni per l'Partner Center?

Passare alla documentazione [del marketplace commerciale,](index.yml)quindi espandere **Portale del Marketplace commerciale in Partner Center**. Per visualizzare gli articoli della Guida per la creazione di Partner Center, espandere **Crea una nuova offerta.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Quali sono le differenze di gestione della pubblicazione e dell'offerta?

Ecco alcune differenze tra l'portale Cloud Partner e Partner Center.

### <a name="modular-publishing-capabilities"></a>Funzionalità di pubblicazione modulari

Il Centro per i partner offre un'opzione di pubblicazione modulare che consente di selezionare le modifiche da pubblicare anziché pubblicare sempre tutti gli aggiornamenti in una sola volta. Ad esempio, la schermata seguente mostra che le uniche modifiche selezionate per la pubblicazione sono le modifiche apportate a **Proprietà** e  **Inserzione offerta**. Le modifiche apportate nella pagina Anteprima non verranno pubblicate.

[![Screenshot che mostra la Partner Center di revisione e pubblicazione.](media/cpp-pc-faq/review-page.png "Mostra la Partner Center di revisione e pubblicazione")](media/cpp-pc-faq/review-page.png#lightbox)

Gli aggiornamenti che non vengono pubblicati vengono salvati come bozze. Continuare a usare l'anteprima dell'offerta per verificare l'offerta prima di renderla pubblica.

### <a name="enhanced-preview-options"></a>Opzioni di anteprima ottimizzate

Il Centro per i partner include una [funzionalità di confronto](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) con opzioni di filtro ottimizzate. Questo consente di confrontare le versioni di anteprima e live dell'offerta.

[![Screenshot che mostra la Partner Center di confronto.](media/cpp-pc-faq/compare.png "Mostra la funzionalità Partner Center di confronto")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Personalizzazione e modifiche di navigazione

Si noteranno alcune modifiche di personalizzazione. Ad esempio, *gli SKU* sono marchiati *come Piani* in Partner Center:

[![Screenshot che mostra la Partner Center Plans.](media/cpp-pc-faq/plans.png "Mostra la pagina Partner Center dei piani")](media/cpp-pc-faq/plans.png#lightbox)

Inoltre, le informazioni fornite in precedenza nelle pagine **Marketplace** o **Storefront Details** (Consulting Service, Power BI  app) nel portale Cloud Partner vengono ora raccolte nella pagina di inserzione dell'offerta in Partner Center:

[! [Screenshot che mostra la pagina Partner Center presentazione dell'offerta.] (media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Le informazioni fornite in precedenza per gli SKU in una singola pagina del portale Cloud Partner possono ora essere raccolte in diverse pagine Partner Center:

- Pagina Di configurazione del piano
- Pagina Elenco piani
- Pagina Disponibilità del piano
- Pianificare la configurazione tecnica, come illustrato di seguito:

[![Mostra la pagina Partner Center configurazione tecnica.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

L'ID offerta è ora visualizzato sulla barra di spostamento a sinistra dell'offerta:

![Mostra la località Partner Center'ID offerta](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Interrompere la vendita di un'offerta

È possibile chiedere di [interrompere la vendita di un'offerta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) sul marketplace direttamente dal portale del Centro per i partner. L'opzione è disponibile nella pagina **Panoramica offerta** per l'offerta.

[![Screenshot che mostra la Partner Center per interrompere la vendita di un'offerta.](media/cpp-pc-faq/stop-sell.png "Mostra la pagina Partner Center per interrompere la vendita di un'offerta")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Le API REST portale Cloud Partner sono ancora supportate?

Le API portale Cloud Partner sono integrate con Partner Center e continueranno a funzionare. La transizione al Partner Center introduce piccole modifiche. Esaminare la tabella seguente per assicurarsi che il codice continui a funzionare in Partner Center.

| API | Descrizione delle modifiche | Impatto |
| --- | --- | --- |
| Pubblicazione POST, GoLive, annullamento | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso ma continuerà a funzionare allo stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si invia una delle richieste POST corrispondenti per un'offerta, l'intestazione Location avrà uno dei due formati seguenti, a seconda dello stato di migrazione dell'offerta: <ul><li>Offerte non migrate: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operazione GET | Per le offerte che in precedenza supportavano un campo "posta elettronica di notifica" nella risposta, questo campo verrà deprecato e non verrà più restituito per le offerte migrate. | Per le offerte migrate, non verranno più inviate notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel Centro per i partner per inviare messaggi di posta elettronica. In particolare, le notifiche sullo stato dell'operazione verranno inviate all'indirizzo di posta elettronica impostato nella sezione Informazioni di contatto del venditore delle impostazioni dell'account in Partner Center.<br><br>Verificare che l'indirizzo di posta elettronica impostato nella sezione Seller contact info (Informazioni di contatto del venditore) in [Account settings (Impostazioni account)](https://partner.microsoft.com/dashboard/account/management) in Partner Center sia corretto per ricevere le notifiche. |
|||