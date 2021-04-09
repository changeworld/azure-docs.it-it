---
title: Creare un'offerta di test
description: Come creare un'offerta di sviluppo separata per il test dell'offerta di produzione nel programma Commercial Marketplace nel centro per i partner Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 893d38d7dcf2ef0910bc46d3e9bfd168c2a89162
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543364"
---
# <a name="create-a-test-offer"></a>Creare un'offerta di test

Per lo sviluppo in un ambiente separato dall'offerta di produzione, è possibile creare un'offerta di test e sviluppo (DEV) separata e un'offerta di produzione (PROD) separata. Per informazioni sui vantaggi derivanti dall'uso di un'offerta di sviluppo separata, vedere [pianificare un'offerta SaaS](plan-saas-offer.md#test-offer).

La maggior parte delle impostazioni verrà configurata nelle offerte sviluppo e PROD. Ad esempio, la lingua e le risorse di marketing ufficiali, ad esempio le schermate e i loghi, devono essere uguali. Nei casi in cui la configurazione è la stessa, è possibile copiare e incollare i campi dai piani nell'offerta di sviluppo ai piani nell'offerta di PROD.

Le sezioni seguenti descrivono le differenze di configurazione tra le offerte sviluppo e PROD.

## <a name="offer-setup-page"></a>Pagina Configurazione dell'offerta

Si consiglia di usare lo stesso alias nella casella **alias** di entrambe le offerte, ma aggiungere "_Test" all'alias dell'offerta dev. Se, ad esempio, l'alias dell'offerta PROD è "contososolution", l'alias dell'offerta DEV dovrebbe essere "contososolution_test". In questo modo, è possibile identificare facilmente l'offerta di sviluppo dall'offerta di produzione.

Nella sezione **Customer Leads** usare una tabella di Azure o un ambiente di test CRM per l'offerta dev. Usare il sistema di gestione dei lead previsto per l'offerta PROD.

## <a name="properties-page"></a>Pagina Proprietà

Configurare questa pagina in entrambe le offerte per sviluppo e PROD.

## <a name="offer-listing-page"></a>Pagina Presentazione dell'offerta

Configurare questa pagina in entrambe le offerte per sviluppo e PROD.

## <a name="preview-audience"></a>Destinatari dell'anteprima

Nell'offerta DEV, includere i nomi dell'entità utente Azure Active Directory (AAD) o gli indirizzi di posta elettronica account Microsoft (MSA) degli sviluppatori e dei tester, incluso. Il nome dell'entità utente di un utente in AAD può essere diverso dall'indirizzo di posta elettronica dell'utente. Ad esempio, jane.doe@contoso.com non funzionerà, ma janedoe@contoso.com lo utilizzerà. Gli utenti designati avranno accesso all'offerta DEV quando si condivide il collegamento di **Anteprima** durante la fase di sviluppo e test.

Nell'offerta di produzione, includere il Azure AD nome dell'entità utente o l'indirizzo di posta elettronica dell'account Microsoft degli utenti che convalideranno l'offerta prima di selezionare il **pulsante Go Live** per pubblicare l'offerta in tempo reale.

## <a name="technical-configuration-page"></a>Pagina Configurazione tecnica

In questa tabella vengono descritte le differenze tra le impostazioni per le offerte per lo sviluppo e le offerte PROD.

***Tabella 1: differenze di configurazione tecnica***

| Impostazione | Offerta di sviluppo | Offerta PROD |
| ------------ | ------------- | ------------- |
| URL della pagina di destinazione | Immettere l'endpoint di sviluppo/test. | Immettere l'endpoint di produzione. |
| Webhook connessione | Immettere l'endpoint di sviluppo/test. | Immettere l'endpoint di produzione. |
| ID tenant Azure Active Directory | Immettere l'ID tenant per la registrazione dell'app di test (ID directory AAD). | Immettere l'ID tenant per la registrazione dell'app di produzione. |
| ID dell'applicazione Azure Active Directory | Immettere l'ID applicazione per la registrazione dell'app di test (ID client). | Immettere l'ID applicazione per la registrazione dell'app di produzione. |
||||

## <a name="plan-overview-page"></a>Pagina panoramica piano

Quando si creano i piani, è consigliabile usare lo stesso _ID piano_ e il _nome del piano_ nelle offerte di sviluppo e di produzione, tranne che per aggiungere l'ID del piano nell'offerta dev con **_Test**. Se, ad esempio, l'ID del piano nell'offerta PROD è "Enterprise", l'ID del piano nell'offerta DEV dovrebbe essere "enterprise_test". In questo modo, è possibile identificare facilmente l'offerta di sviluppo dall'offerta di produzione. Si creeranno piani nell'offerta di produzione con i modelli di prezzi e i prezzi che si scelgono più idonei per l'offerta.

### <a name="plan-listing"></a>Presentazione del piano

Nella scheda   >  **elenco piano** panoramica piano immettere la stessa descrizione del piano nei piani dev e prod.

### <a name="pricing-and-availability-page"></a>Pagina Prezzi e disponibilità

In questa sezione vengono fornite indicazioni per completare la pagina relativa alla **Panoramica** dei  >  **prezzi e alla disponibilità** del piano.

#### <a name="markets"></a>Mercati

Selezionare gli stessi mercati per le offerte sviluppo e PROD.

#### <a name="pricing"></a>Prezzi

Usare l'offerta DEV per sperimentare i modelli di determinazione prezzi. Dopo aver verificato il modello di determinazione dei prezzi o i modelli più adatti, verranno creati i piani nell'offerta PROD con i modelli di prezzi e i prezzi desiderati.

L'offerta DEV deve avere piani con prezzi zero o bassi nei piani. L'offerta PROD avrà i prezzi che si desidera addebitare ai clienti.

> [!IMPORTANT]
> Gli acquisti effettuati in anteprima verranno elaborati per le offerte di sviluppo e PROD. Se un'offerta ha un prezzo di $100/mo, la tua azienda verrà addebitata $100. In tal caso, è possibile aprire un [ticket di supporto](support.md) e verranno rilasciati i pagamenti per l'importo totale (e non verrà addebitata alcuna tariffa per il servizio di archiviazione).

#### <a name="pricing-model"></a>Modello di prezzi

Usare lo stesso modello di determinazione prezzi nei piani delle offerte sviluppo e PROD. Ad esempio, se il piano nell'offerta PROD è forfettario, con un termine di fatturazione mensile, configurare il piano nell'offerta DEV usando lo stesso modello.

Per ridurre i costi per il test dei modelli di prezzo, incluse le dimensioni di misurazione personalizzate del Marketplace, è consigliabile configurare la sezione relativa ai **prezzi** della scheda **prezzi e disponibilità** nell'offerta dev con prezzi inferiori rispetto all'offerta prod. Di seguito sono riportate alcune linee guida che è possibile seguire quando si impostano i prezzi per i piani nell'offerta DEV.

***Tabella 2: linee guida sui prezzi***

| Prezzo | Commento |
| ------------ | ------------- |
| $ 0,00 | Impostare un costo totale per le transazioni pari a zero in modo da non avere alcun impatto finanziario. Usare questo prezzo quando si effettuano chiamate alle API di misurazione o per testare i piani di acquisto nell'offerta durante lo sviluppo della soluzione. |
| $0,01-$49,99 | Usare questo intervallo di prezzi per testare l'analisi, la creazione di report e il processo di acquisto. |
| $50,00 e versioni successive | Utilizzare questo intervallo di prezzi per testare i pagamenti. Per informazioni sulla pianificazione dei pagamenti, vedere [pianificazioni dei pagamenti e processi](/partner-center/payout-policy-details). |
|||

Per evitare di addebitare una tariffa del servizio di archiviazione per il test, aprire un [ticket di supporto](support.md).

#### <a name="free-trial"></a>Versione di prova gratuita

Non abilitare una versione di valutazione gratuita per l'offerta DEV.

## <a name="co-sell-with-microsoft-page"></a>Co-selling con la pagina Microsoft

Non configurare la **co-selling con** la scheda Microsoft dell'offerta dev.

## <a name="resell-through-csps"></a>Rivendere tramite CSP

Non configurare la scheda **rivendi tramite CSP** dell'offerta dev.

## <a name="next-steps"></a>Passaggi successivi

- [Testare e pubblicare un'offerta SaaS](test-publish-saas-offer.md)
