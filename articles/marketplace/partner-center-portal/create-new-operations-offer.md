---
title: Creare un'offerta di Dynamics 365 per le operazioni in Microsoft AppSource
description: Come creare un'offerta di Dynamics 365 per le operazioni in Microsoft AppSource. Elencare o vendere l'offerta in AppSource o tramite il programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: dc3566634948df3ce8ecb747148eb10f48bfc582
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106888"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Creare un'offerta Dynamics 365 for Operations

Questo articolo descrive come creare una nuova offerta di Dynamics 365 per le operazioni. [Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) di Microsoft è un servizio ERP (Enterprise Resource Planning) che supporta finanza avanzata, operazioni, produzione e gestione della catena di approvvigionamento. Tutte le offerte per Dynamics 365 for Operations devono essere sottoposte al processo di certificazione.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](../create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

>[!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche all'offerta verranno aggiornate solo nel centro per i partner e nel negozio online dopo aver inviato di nuovo l'offerta per la pubblicazione.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento di sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 for Operations**.

    ![Menu di spostamento di sinistra.](./media/new-offer-dynamics-365-operations.png)

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- L'ID offerta combinato con l'ID editore deve avere una lunghezza compresa tra 40 caratteri.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma senza spazi. Se, ad esempio, l'ID dell'editore è `testpublisherid` e si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Questo ID non può essere modificato dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questo nome non può essere modificato dopo aver selezionato **Crea**.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="alias"></a>Alias

Immettere un nome descrittivo che verrà usato per fare riferimento a questa offerta esclusivamente all'interno del centro per i partner. Questo nome (pre-popolato con il contenuto immesso al momento della creazione dell'offerta) non verrà usato nel Marketplace ed è diverso dal nome dell'offerta indicato ai clienti. Se si vuole aggiornare il nome dell'offerta in un secondo momento, passare alla pagina dell' [elenco delle offerte](#offer-listing) .

### <a name="setup-details"></a>Dettagli di configurazione

Per **come si desidera che i clienti possano interagire con questa offerta di inserzione?**, selezionare l'opzione che si desidera utilizzare per questa offerta.

- **Ottienilo ora (gratuito): puoi** elencare gratuitamente la tua offerta ai clienti.
- **Versione di valutazione gratuita** : elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

    > [!NOTE]
    > I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

- **Contattami** : Raccogli le informazioni di contatto del cliente connettendo il sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo un incremento delle conversioni e la generazione di clienti potenziali altamente qualificati. Per altre informazioni, vedere la pagina relativa all' [test drive](../what-is-test-drive.md).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

### <a name="customer-leads"></a>Clienti potenziali

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Panoramica della gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="categories"></a>Categorie

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca del Marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una delle due categorie, incluse una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](../gtm-offer-listing-best-practices.md)consigliate.

### <a name="industries"></a>Settori

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Specificare i termini e le condizioni legali qui. È anche possibile fornire l'indirizzo in cui sono disponibili i termini e le condizioni. Ai clienti verrà chiesto di accettare questi termini prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details such as offer name, description, and images for each language/market. Select the language/market name to provide this info.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Fornire i dettagli dell'elenco di offerte solo in un linguaggio. Non è necessario usare l'inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

Di seguito è riportato un esempio di come vengono visualizzate le informazioni sull'offerta in Microsoft AppSource (i prezzi elencati sono solo a scopo esemplificativo e non sono destinati a riflettere i costi effettivi):
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Descrizioni di chiamata

1. Logo
2. Prodotti
3. Categorie
4. Settori
5. Indirizzo di supporto (collegamento)
6. Condizioni per l'utilizzo
7. Informativa sulla privacy
8. Nome offerta
9. Descrizione
10. Screenshot/video

### <a name="marketplace-details"></a>Dettagli del marketplace

Il **nome** immesso qui verrà visualizzato ai clienti come titolo dell'elenco di offerte. Questo campo viene precompilato con il testo immesso per **Alias offerta** quando è stata creata l'offerta, ma è possibile modificarne il valore. Questo nome può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright). Il nome non può essere costituito da più di 50 caratteri e non può includere emoji.

Fornire una breve descrizione dell'offerta, fino a 100 caratteri, per il **Riepilogo dei risultati della ricerca**. Questa descrizione può essere usata nei risultati della ricerca del marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Facoltativamente, è possibile immettere fino a tre **parole chiave di ricerca** per aiutare i clienti a trovare l'offerta nel Marketplace. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione.

Se si desidera consentire ai clienti di conoscere i **prodotti con cui l'app utilizza**, immettere fino a tre nomi di prodotto.

### <a name="helpprivacy-urls"></a>URL della Guida/URL privacy

Immettere il **collegamento alla guida per l'app** (URL) in cui i clienti possono ottenere ulteriori informazioni sull'offerta. L'URL della guida non può corrispondere all'URL di supporto.

Immettere il **collegamento all'informativa sulla** privacy dell'organizzazione (URL). Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="contact-information"></a>Informazioni contatto

Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto supporto tecnico** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e potranno essere fornite ai partner CSP.

Nella sezione **Contatto di supporto** specificare un valore per **URL supporto** in cui i partner CSP possono trovare supporto per l'offerta. L'URL del supporto tecnico non può corrispondere all'URL della guida.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno un documento di marketing correlato (e fino a un massimo di tre), ad esempio white paper, brochure, elenchi di controllo o presentazioni, in formato PDF.

### <a name="marketplace-media"></a>File multimediali del marketplace

Fornire logo e immagini che verranno usati quando si mostra l'offerta ai clienti. Tutte le immagini devono essere in formato PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

#### <a name="logos"></a>Loghi

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner utilizzerà questo per creare altre dimensioni obbligatorie. Facoltativamente, è possibile sostituire questo oggetto con un'immagine diversa in un secondo momento.

Questi logo vengono usati in posizioni diverse nell'elenco:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. È necessario almeno uno screenshot ed è possibile aggiungerne fino a cinque. Tutte le schermate devono avere 1280 x 720 pixel e in formato PNG.

#### <a name="videos"></a>Video

Se si vuole, è possibile aggiungere fino a quattro video che descrivono l'offerta. I video devono essere ospitati in un sito esterno. Per ciascuna di esse, immettere il nome del video, il relativo indirizzo e un'immagine di anteprima del video (1280 x 720 pixel).

Per risorse aggiuntive relative alle presentazioni nel marketplace, vedere [Procedure consigliate per la presentazione di offerte nel marketplace](../gtm-offer-listing-best-practices.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="availability"></a>Disponibilità

Questa pagina consente di definire dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Per specificare i mercati in cui l'offerta deve essere disponibile, selezionare **modifica mercati** per visualizzare la finestra popup **selezione mercato** .

Selezionare almeno un mercato. Scegliere **Seleziona tutto** per rendere disponibile l'offerta in ogni possibile mercato oppure selezionare solo i mercati specifici desiderati. Al termine, fare clic su **Salva**.

Le selezioni in questo articolo si applicano solo alle nuove acquisizioni; Se un utente dispone già di un'app in un determinato mercato e successivamente si rimuove tale mercato, le persone che dispongono già dell'offerta in quel mercato possono continuare a utilizzarlo, ma nessun nuovo cliente in quel mercato potrà ottenere l'offerta.

> [!IMPORTANT]
> Si è tenuti a soddisfare i requisiti legali locali, anche se non sono elencati qui o nel Centro per i partner. Anche se si selezionano tutti i mercati, le leggi locali, le restrizioni o altri fattori possono impedire che alcune offerte siano elencate in alcuni paesi e regioni.

### <a name="preview-audience"></a>Destinatari dell'anteprima

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile a un **gruppo di destinatari dell'anteprima** limitato. Immettere qui un valore in **Nascondi chiave** (qualsiasi stringa che usa solo lettere minuscole e/o numeri). I membri del gruppo di destinatari in anteprima possono usare questa chiave come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando si è pronti a rendere disponibile l'offerta e a rimuovere la restrizione, sarà necessario rimuovere la chiave specificata in **Nascondi chiave** e ripubblicare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

### <a name="solution-identifier"></a>Identificatore soluzione

Specificare l'identificatore della soluzione (GUID).

Per individuare l'identificatore della soluzione:

1. In Dynamics Lifecycle Services di Microsoft selezionare **Gestione soluzioni**.
2. Selezionare la soluzione e quindi cercare l'**identificatore della soluzione** in **Panoramica pacchetto**. Se l'identificatore è vuoto, scegliere **Modifica**, ripubblicare il pacchetto e quindi riprovare.

### <a name="release-version"></a>Versione di rilascio

Selezionare la versione di Dynamics 365 for Finance and Operations con cui funziona questa soluzione.

Prima di continuare, selezionare **Salva bozza**.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>Contenuto supplementare

Questa pagina consente di fornire informazioni aggiuntive che consentano di convalidare l'offerta. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="validation-assets"></a>Risorse per la convalida

Caricare in questa sezione un [report di Analisi personalizzazione](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report). Questo report viene generato analizzando i modelli di personalizzazione e di estensione, in base a un set predefinito di regole per le procedure consigliate.

Questo file deve essere in formato xls o xlsx. Se si dispone di più report, è possibile caricare un file con estensione zip contenente tutti i report.

### <a name="does-solution-include-localizations"></a>La soluzione prevede localizzazioni?

Se la soluzione abilita l'uso di criteri e standard locali (ad esempio, se sono conformi alle diverse regole relative alle retribuzioni richieste da paesi/aree diverse), selezionare **Sì**. In caso contrario, selezionare **No**.

### <a name="does-solution-enable-translations"></a>La soluzione consente le traduzioni?

Se il testo della soluzione può essere tradotto in altre lingue, rispondere **Sì**. In caso contrario, selezionare **No**.

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, selezionare **revisione e pubblicazione** nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
    - **Non avviato** : la sezione non è stata toccata e deve essere completata.
    - **Incomplete** : la sezione contiene errori che devono essere corretti o richiede ulteriori informazioni. Tornare alla sezione e aggiornarla.
    - **Completa** : la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completato prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app. Per ulteriori informazioni sulle istruzioni di test e sul completamento di una prima pubblicazione, vedere la pagina relativa alla [convalida funzionale AppSource Dynamics 365 Finance and Operations](../dynamics-365-finance-operations-functional-validation.md).
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Invieremo un messaggio di posta elettronica quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. Tornare al centro per i partner e selezionare **Go-Live** per pubblicare l'offerta sul pubblico.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)