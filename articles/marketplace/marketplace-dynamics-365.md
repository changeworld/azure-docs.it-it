---
title: Pianificare le offerte dynamics 365 per Microsoft AppSource
description: Pianificare le offerte dynamics 365 per Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819428"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Pianificare un'offerta di Microsoft Dynamics 365

Questo articolo illustra le diverse opzioni e funzionalità di un'offerta Dynamics 365 Microsoft AppSource nel marketplace commerciale. AppSource include offerte che si basano su Microsoft 365, Dynamics 365, PowerApps e Power BI. AppSource consente offerte a pagamento (*Get It Now*), list ( Contact *Me*) e trial ( Try *It Now*).

Prima di iniziare, creare un account marketplace commerciale in [Partner Center](./partner-center-portal/create-account.md) e assicurarsi che sia registrato nel programma del marketplace commerciale. Esaminare anche il processo [di pubblicazione e le linee guida](/office/dev/store/submit-to-appsource-via-partner-center).

## <a name="licensing-options"></a>Opzioni di licenza

Quando si prepara la pubblicazione di una nuova offerta, è necessario decidere quale opzione di licenza scegliere. In questo modo verranno determinate le informazioni aggiuntive che sarà necessario fornire in un secondo momento durante la creazione dell'offerta Partner Center.

Queste sono le opzioni di licenza disponibili per le offerte dynamics 365:

| Opzione gestione licenze | Processo di transazione |
| --- | --- |
| Scarica ora (gratuito) | Elencare gratuitamente l'offerta ai clienti. |
| Free trial (listing) (Versione di valutazione gratuita (presentazione)) | Offrire ai clienti una versione di valutazione gratuita di uno, tre o sei mesi. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft. |
| Contact me (Contattami) | Consente di raccogliere informazioni di contatto del cliente tramite la connessione al sistema di gestione delle relazioni con i clienti (CRM, Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del CRM, vedere la sezione **Clienti potenziali** della pagina Configurazione dell'offerta del **tipo di** offerta. |
|

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Clienti potenziali

Quando si pubblica un'offerta nel marketplace commerciale con Partner Center, è necessario connetterla al sistema Crm (Customer Relationship Management). per ricevere le informazioni di contatto del cliente non appena questo esprime interesse o usa il prodotto. La connessione a un CRM è necessaria se si vuole abilitare un test drive; In caso contrario, la connessione a un CRM è facoltativa. Partner Center supporta tabelle di Azure, Dynamics 365 Customer Engagement, endpoint HTTPS, Marketo e Salesforce.

## <a name="legal"></a>Legale

Creare i **termini e le condizioni**. I clienti dovranno accettarlo prima di poter provare l'offerta. Microsoft ha un contratto standard, ma non si applica alle offerte di Dynamics 365.

## <a name="offer-listing-details"></a>Dettagli dell'inserzione dell'offerta

Ecco un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (tutti i prezzi elencati sono solo a scopo di esempio e non riflettono i costi effettivi):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Illustra come viene visualizzata questa offerta in Microsoft AppSource.":::

> [!NOTE]
> Il contenuto dell'inserzione dell'offerta non deve essere in inglese se la descrizione dell'offerta inizia con la frase "Questa applicazione è disponibile solo in [lingua non inglese]".

Per facilitare la creazione dell'offerta, preparare questi elementi in anticipo. Tutti gli elementi sono obbligatori, ad eccezione dei casi in cui è stato fatto.

- **Nome:** il nome verrà visualizzato come titolo dell'inserzione dell'offerta nel marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere emoji (a meno che non siano simboli di marchio e copyright) ed è limitato a 50 caratteri.
- **Riepilogo dei risultati della** ricerca: scopo o funzione dell'offerta come singola frase senza interruzioni di riga in un massimo di 100 caratteri. Viene usato nei risultati della ricerca delle inserzioni nel marketplace commerciale.
- **Descrizione:** questa descrizione viene visualizzata nella panoramica delle inserzioni nel marketplace commerciale. È consigliabile includere una proposta di valore, vantaggi chiave, base utenti prevista, qualsiasi associazione di categoria o settore, opportunità di acquisto in-app, eventuali divulgazioni necessarie e un collegamento per altre informazioni. Questa casella di testo include controlli editor di testo RTF per rendere la descrizione più coinvolgente. Facoltativamente, usare i tag HTML per la formattazione.
- **Parole chiave di** ricerca (facoltativo): fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta. Non includere il nome e la **descrizione** dell'offerta;  il testo viene incluso automaticamente nella ricerca.
- **Prodotto con cui funziona l'app** (facoltativo): nomi di un massimo di tre prodotti con cui funziona l'offerta.
- **Collegamenti alla Guida e all'informativa** sulla privacy: URL per la Guida dell'offerta e informativa sulla privacy dell'azienda. L'utente è responsabile di garantire che l'app sia conforme alle leggi e alle normative sulla privacy.
- **Informazioni contatto**
  - **Contatto del supporto:** nome, telefono e indirizzo di posta elettronica che i partner Microsoft useranno quando i clienti aprono i ticket. Includere l'URL per il sito Web di supporto.
  - **Contatto tecnico:** nome, telefono e indirizzo di posta elettronica che Microsoft può usare direttamente in caso di problemi con l'offerta. Queste informazioni di contatto non sono elencate nel marketplace commerciale.
- **Documenti di supporto:** fino a tre documenti per i clienti, ad esempio white paper, brochure, elenchi di controllo o presentazioni di PowerPoint, in formato PDF.
- **Supporti**
    - **Logo:** file PNG per il logo **Large.** Partner Center questa opzione verrà utilizzata per creare altre dimensioni del logo necessarie. Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.
    - **Screenshot:** almeno uno e fino a cinque screenshot che mostrano il funzionamento dell'offerta. Le immagini devono avere dimensioni di 1280 x 720 pixel in formato PNG e includere una didascalia.
    - **Video** (facoltativo): fino a quattro video che illustrano l'offerta. Includere un nome, un URL per YouTube o Vimeo e un'anteprima PNG da 1280 x 720 pixel.

> [!Note]
> L'offerta deve soddisfare i criteri [di certificazione del marketplace commerciale generali](/legal/marketplace/certification-policies#100-general) da pubblicare nel marketplace commerciale.

## <a name="additional-sales-opportunities"></a>Opportunità di vendita aggiuntive

È possibile scegliere di acconsentire esplicitamente ai canali di marketing e vendita supportati da Microsoft. Quando si crea l'offerta Partner Center, si vedranno due schede verso la fine del processo di **co-selling con Microsoft**. Questa opzione consente ai team di vendita Microsoft di prendere in considerazione la soluzione idonea per il co-selling IP durante la valutazione delle esigenze dei clienti. Per informazioni dettagliate su come preparare [l'offerta per la valutazione, Partner Center'opzione di co-selling](commercial-marketplace-co-sell.md) in Partner Center.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver considerato gli elementi di pianificazione descritti in precedenza, selezionare una delle opzioni seguenti (disponibili anche nel sommario a sinistra) per iniziare a creare l'offerta.

| Guida alla pubblicazione    | Note  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Quando si compila per l'edizione Enterprise, esaminare prima di tutto questi processi [di pubblicazione aggiuntivi e le linee guida](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source). |
| [Dynamics 365 Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Esaminare prima di tutto questi processi [di pubblicazione aggiuntivi e le linee guida](/dynamics365/customer-engagement/developer/publish-app-appsource). |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Esaminare prima di tutto questi processi [di pubblicazione aggiuntivi e le linee guida](/power-bi/developer/office-store). |
|
