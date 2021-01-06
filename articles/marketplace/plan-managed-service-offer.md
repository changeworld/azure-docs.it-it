---
title: Pianificare un'offerta del servizio gestito per Microsoft Commercial Marketplace
description: Come pianificare una nuova offerta di servizio gestito per Azure Marketplace usando il programma Commercial Marketplace nel centro per i partner Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: a504969d23fce8000119aadf9e45d599da0894f0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918180"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Come pianificare un'offerta di servizio gestito per Microsoft Commercial Marketplace

In questo articolo vengono presentati i requisiti per la pubblicazione di un'offerta di servizio gestito in Microsoft Commercial Marketplace tramite il centro per i partner.

I servizi gestiti sono offerte di Azure Marketplace che consentono la gestione Cross-tenant e multi-tenant con il faro di Azure. Per altre informazioni, vedere [che cos'è Azure Lighthouse?](../lighthouse/overview.md) Quando un cliente acquista un'offerta di servizio gestito, è in grado di delegare una o più sottoscrizioni o gruppi di risorse

## <a name="eligibility-requirements"></a>Requisiti di idoneità

Per pubblicare un servizio gestito, è necessario avere ottenuto una competenza Microsoft Gold o Silver nella piattaforma cloud. Questa competenza dimostra le proprie competenze ai clienti. Per altre informazioni, vedere [Microsoft Partner Network le competenze](https://partner.microsoft.com/membership/competencies).

Le offerte devono soddisfare tutti i [criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies) applicabili da pubblicare in Azure Marketplace.

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta diversi tipi di sistemi CRM, oltre all'opzione per l'uso di una tabella di Azure o la configurazione di un endpoint HTTPS usando Power Automate.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Contratti legali

Nella pagina delle proprietà del centro per i partner verrà richiesto di specificare i **termini e le condizioni** per l'utilizzo dell'offerta. È possibile immettere i termini direttamente nel centro per i partner o specificare l'URL in cui è possibile trovarli. I clienti dovranno accettare i termini e le condizioni prima di acquistare l'offerta.

## <a name="offer-listing-details"></a>Dettagli elenco offerte

Quando si crea un'offerta di servizio gestita nel centro per i partner, si immetteranno testo, immagini, documenti e altri dettagli dell'offerta. Questo è ciò che i clienti visualizzeranno quando scoprono l'offerta in Azure Marketplace. Vedere l'esempio seguente:

:::image type="content" source="media/example-managed-service.png" alt-text="Illustra il modo in cui viene visualizzata un'offerta del servizio gestito in Azure Marketplace.":::

**Descrizioni di chiamata**

1. Logo
1. Nome
1. Breve descrizione
1. Categorie
1. Contratti legali e informativa sulla privacy
1. Descrizione
1. Screenshot/video
1. Collegamenti utili

Di seguito è riportato un esempio di come viene visualizzato l'elenco delle offerte nella portale di Azure:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Viene illustrato il modo in cui questa offerta viene visualizzata nel portale di Azure.":::

**Descrizioni di chiamata**

1. Nome
2. Descrizione
3. Collegamenti utili
4. Screenshot/video

> [!NOTE]
> Se l'offerta è in una lingua diversa dall'inglese, l'elenco di offerte può trovarsi in tale lingua, ma la descrizione deve iniziare o terminare con la frase inglese "questo servizio è disponibile nella &lt; lingua del contenuto dell'offerta>". È anche possibile fornire documenti di supporto in una lingua diversa da quella usata nell'elenco dei dettagli dell'offerta.

Per semplificare la creazione dell'offerta, è possibile preparare alcuni di questi elementi in anticipo. Se non diversamente specificato, sono necessari gli elementi seguenti.

**Nome**: verrà visualizzato come titolo dell'inserzione dell'offerta nel Marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere Emoji (a meno che non si tratti del marchio e dei simboli di copyright) e deve essere limitato a 50 caratteri.

**Riepilogo risultati ricerca**: descrivere lo scopo o l'obiettivo dell'offerta in caratteri di 100 o meno. Questo riepilogo viene usato nei risultati della ricerca nell'elenco del Marketplace commerciale. Non deve essere identico al titolo. È consigliabile includere le parole chiave SEO principali.

**Breve descrizione**: fornire una breve descrizione dell'offerta (fino a 256 caratteri). Verrà visualizzato nell'elenco dell'offerta portale di Azure.

**Descrizione**: descrivere l'offerta in caratteri di 3.000 o meno. Questa descrizione verrà visualizzata nell'elenco del Marketplace commerciale. Si consiglia di includere una proposta di valore, il vantaggio principale, le associazioni di categoria o di settore ed eventuali divulgazioni necessarie.

Ecco alcuni suggerimenti per scrivere la descrizione:

* Descrivere chiaramente il valore dell'offerta nelle prime frasi includendo:
    * Tipo di utente che ottiene vantaggi dall'offerta.
    * Quali sono le esigenze dei clienti o generano gli indirizzi delle offerte.
* Ricordare che le prime frasi possono essere visualizzate nei risultati di ricerca.
* Usare il vocabolario specifico del settore.

È possibile usare i tag HTML per formattare la descrizione. Per informazioni sulla formattazione HTML, vedere [tag HTML supportati nelle descrizioni delle offerte del Marketplace commerciale](./supported-html-tags.md).

**Collegamento all'informativa sulla privacy**: specificare un URL per l'informativa sulla privacy, ospitata nel sito. L'utente è responsabile di garantire che l'offerta sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida.

**Collegamenti utili** (facoltativo): caricare documenti online supplementari sull'offerta.

**Informazioni di contatto**: fornire il nome, l'indirizzo di posta elettronica e il numero di telefono di due persone nell'azienda (è possibile essere uno di essi): un contatto di supporto e un contatto tecnico. Queste informazioni verranno usate per comunicare con l'utente sulla propria offerta. Queste informazioni non vengono visualizzate ai clienti, ma possono essere fornite ai partner Cloud Solution Provider (CSP)

**URL di supporto** (facoltativo): se si hanno siti Web di supporto per i clienti globali di Azure e/o i clienti di Azure per enti pubblici, fornire tali URL.

**Supporti del Marketplace-Logos**: fornire un file PNG per il logo di grandi dimensioni dell'offerta. Il centro per i partner lo userà per creare logo di medie e piccole dimensioni. Facoltativamente, è possibile sostituire questi loghi con un'immagine diversa in un secondo momento.

* Il logo di grandi dimensioni (da 216 x 216 a 350 x 350 px) viene visualizzato nell'inserzione dell'offerta in Azure Marketplace.
* Il logo medio (90 x 90 px) viene visualizzato quando viene creata una nuova risorsa.
* Il logo piccolo (48 x 48 px) viene usato nei risultati della ricerca di Azure Marketplace.

Seguire queste linee guida per i logo:

* Assicurarsi che l'immagine non sia allungata.
* La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
* I colori portale di Azure sono bianco e nero. Non usarli come sfondo del logo. Si consiglia di usare semplici colori primari che rendono il logo più importante.
* Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
* L'aspetto del logo deve essere flat. Evitare gradienti. Non inserire testo, nemmeno il nome del marchio o della società, sul logo.

**Supporti Marketplace-schermate** (facoltativo): aggiungere fino a cinque immagini che dimostrano il funzionamento dell'offerta. Tutte le immagini devono avere una dimensione di 1280 x 720 pixel e in. Formato PNG.

**Supporti del Marketplace: video** (facoltativo): è possibile caricare fino a cinque video che illustrano l'offerta. I video devono essere ospitati in YouTube o Vimeo e avere un'anteprima (file PNG 1280 x 720).

## <a name="preview-audience"></a>Destinatari dell'anteprima

Un pubblico di anteprima può accedere all'offerta prima che venga pubblicata in Azure Marketplace per testarla. Nella pagina **Anteprima audience** del centro per i partner è possibile definire un numero limitato di destinatari di anteprima.

> [!NOTE]
> Un pubblico di anteprima è diverso da un piano privato. Un piano privato è quello reso disponibile solo per un determinato gruppo di destinatari. In questo modo è possibile negoziare un piano personalizzato con clienti specifici.

È possibile inviare gli inviti agli indirizzi di posta elettronica dell'account Microsoft (MSA) o Azure Active Directory (Azure AD). Aggiungere fino a 10 indirizzi di posta elettronica manualmente o importare fino a 20 con un file con estensione CSV. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

## <a name="plans-and-pricing"></a>Piani e prezzi

Le offerte del servizio gestito richiedono almeno un piano. Un piano definisce l'ambito della soluzione, i limiti e i prezzi associati, se applicabile. È possibile creare più piani per l'offerta per offrire ai clienti diverse opzioni tecniche e di prezzo. Per indicazioni generali sui piani, inclusi i piani privati, vedere [piani e prezzi per le offerte del Marketplace commerciale](plans-pricing.md).

I servizi gestiti supportano solo un modello di determinazione prezzi: **Bring your own License (BYOL)**. Ciò significa che i clienti verranno addebitati direttamente e Microsoft non addebiterà alcun costo.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'offerta di servizi gestiti](./create-managed-service-offer.md)
* [Procedure consigliate per le inserzioni di offerte](./gtm-offer-listing-best-practices.md)
