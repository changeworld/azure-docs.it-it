---
title: Creare un'offerta Dynamics 365 Business Central in Microsoft AppSource
description: Come creare un'offerta Dynamics 365 for Business Central in Microsoft AppSource. Elencare o vendere l'offerta in AppSource o tramite il programma Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501434"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Creare un'offerta Dynamics 365 for Business Central

Questo articolo descrive come creare una nuova offerta Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) è un servizio ERP (Enterprise Resource Planning) che gestisce un'ampia gamma di processi aziendali, tra cui finanza, operazioni, supply chain, CRM, gestione dei progetti ed e-commerce. I pacchetti Premium supportano anche il modello di distribuzione classica e la produzione. Tutte le offerte per Dynamics 365 Business Central devono essere sottoposte al processo di certificazione.

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](../create-account.md) se non è ancora stato fatto. Assicurarsi che l'account sia registrato nel programma del marketplace commerciale.

>[!NOTE]
> Dopo la pubblicazione di un'offerta, le modifiche all'offerta verranno aggiornate solo in Partner Center e nello Store online dopo aver inviato nuovamente l'offerta per la pubblicazione.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel menu di spostamento a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Dynamics 365 Business Central**.

    ![Menu di spostamento a sinistra.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Questo valore è un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web per l'offerta del marketplace e nei modelli di Azure Resource Manager, se applicabile.
- L'ID offerta combinato con l'ID editore deve avere una lunghezza inferiore a 40 caratteri.
- Usare solo lettere minuscole e numeri. Può includere trattini e caratteri di sottolineatura, ma non spazi. Ad esempio, se l'ID editore è `testpublisherid` e si immette **test-offer-1,** l'indirizzo Web dell'offerta sarà `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Questo ID non può essere modificato dopo aver selezionato **Crea.**

Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato nel marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
- Questo nome non può essere modificato dopo aver selezionato **Crea.**

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="alias"></a>Alias

Immettere un nome descrittivo che verrà utilizzato per fare riferimento a questa offerta esclusivamente all'interno Partner Center. Questo nome (precompilato con ciò che è stato immesso al momento della creazione dell'offerta) non verrà usato nel marketplace ed è diverso dal nome dell'offerta visualizzato ai clienti. Se si vuole aggiornare il nome dell'offerta in un secondo momento, passare alla pagina [Presentazione dell'offerta.](#offer-listing)

### <a name="setup-details"></a>Dettagli di configurazione

Selezionare il **tipo di pacchetto** che si applica all'offerta:

- **Un'app per componenti** aggiuntivi estende l'esperienza e le funzionalità esistenti di Dynamics 365 Business Central. Per informazioni dettagliate, vedere [App componente aggiuntivo](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Un'app Connect** può essere usata nello scenario in cui deve essere stabilita una connessione punto a punto tra Dynamics 365 Business Central e una soluzione o un servizio di terze parti. Per informazioni dettagliate, vedere [App di connessione](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

Per **Come si vuole che i potenziali clienti** interagiscano con questa offerta di inserzione? , selezionare l'opzione che si vuole usare per questa offerta.

- **Scarica subito (gratuito):** elencare gratuitamente l'offerta ai clienti.
- **Versione di valutazione gratuita (inserzione):** elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita. Le versioni di valutazione gratuite delle offerte vengono create, gestite e configurate dal servizio e non sono associate a sottoscrizioni gestite da Microsoft.

    > [!NOTE]
    > I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione possono essere usati solo per ottenere informazioni degli utenti tramite Azure Active Directory (Azure AD) per automatizzare la creazione di account nell'app. Gli account Microsoft non sono supportati per l'autenticazione con questo token.

- **Contattami:** raccogliere le informazioni di contatto del cliente connettendo il sistema Customer Relationship Management (CRM). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Questi dettagli sul cliente, tra cui il nome dell'offerta, l'ID e l'origine del marketplace in cui ha trovato l'offerta, verranno inviate al sistema CRM configurato. Per altre informazioni sulla configurazione del sistema CRM, vedere [Clienti potenziali](#customer-leads).

### <a name="test-drive"></a>Test drive

Il test drive è un'ottima soluzione per presentare un'offerta a potenziali clienti, dando loro la possibilità di provarla prima dell'acquisto e ottenendo un incremento delle conversioni e la generazione di clienti potenziali altamente qualificati. Per altre informazioni, iniziare con [Informazioni test drive](../what-is-test-drive.md).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

### <a name="customer-leads"></a>Clienti potenziali

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Per altre informazioni, vedere [Panoramica della gestione dei lead](./commercial-marketplace-get-customer-leads.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Questa pagina consente di definire le categorie e i settori usati per raggruppare l'offerta nel marketplace, la versione dell'app e i contratti legali che supportano l'offerta.

### <a name="categories"></a>Categorie

Selezionare categorie e sottocategorie per inserire l'offerta nelle aree di ricerca del marketplace appropriate. Assicurarsi di descrivere il modo in cui l'offerta supporta queste categorie nella descrizione dell'offerta. Selezionare:

- Almeno una e fino a due categorie, tra cui una categoria primaria e una secondaria (facoltativo).
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se nessuna sottocategoria è applicabile all'offerta, selezionare **Non applicabile.**

Vedere l'elenco completo delle categorie e delle sottocategorie in Procedure consigliate per [l'inserzione delle offerte.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Settori

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Immettere il numero di versione dell'offerta. I clienti visualizzeranno questa versione nella pagina dei dettagli dell'offerta.

### <a name="terms-and-conditions"></a>Termini e condizioni

Specificare i termini e le condizioni legali qui. È anche possibile fornire l'indirizzo in cui sono disponibili i termini e le condizioni. Ai clienti verrà chiesto di accettare questi termini prima di poter provare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Questa pagina consente di definire i dettagli dell'offerta, ad esempio il nome dell'offerta, la descrizione, i collegamenti e i contatti.

> [!NOTE]
> Specificare i dettagli dell'inserzione dell'offerta in una sola lingua. Non è necessario usare l'inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento* utile per offrire contenuto in una lingua diversa da quella usata nel contenuto dell'inserzione dell'offerta.

Ecco un esempio di come vengono visualizzate le informazioni sull'offerta Microsoft AppSource (tutti i prezzi elencati sono solo esempi e non sono destinati a riflettere i costi effettivi):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Illustra come viene visualizzata l'offerta Microsoft AppSource.":::

### <a name="call-out-descriptions"></a>Descrizioni dei call-out

1. Logo
2. Prodotti
3. Categorie
4. Indirizzo di supporto (collegamento)
5. Condizioni per l'utilizzo
6. Informativa sulla privacy
7. Nome offerta
8. Riepilogo
9. Descrizione
10. Screenshot/video

### <a name="marketplace-details"></a>Dettagli del marketplace

Il **nome** immesso qui verrà visualizzato ai clienti come titolo dell'inserzione dell'offerta. Questo campo viene precompilato con il testo immesso per **Alias offerta** quando è stata creata l'offerta, ma è possibile modificarne il valore. Questo nome può essere un marchio registrato (ed è possibile includere i simboli di marchio o copyright). Il nome non può essere costituito da più di 50 caratteri e non può includere emoji.

Fornire una breve descrizione dell'offerta, fino a 100 caratteri, per il riepilogo **dei risultati della ricerca**. Questa descrizione può essere usata nei risultati della ricerca del marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Facoltativamente, è possibile immettere fino a tre parole **chiave di ricerca** per aiutare i clienti a trovare l'offerta nel marketplace. Per ottenere risultati ottimali, usare queste parole chiave anche nella descrizione.

Se si vuole inserire i clienti in informazioni sui prodotti con cui funziona **l'app,** immettere fino a tre nomi di prodotto.

### <a name="helpprivacy-urls"></a>URL della Guida/URL privacy

Immettere il **collegamento alla Guida per l'app** (URL) in cui i clienti possono ottenere altre informazioni sull'offerta. L'URL della Guida non può essere uguale all'URL del supporto tecnico.

Immettere il **collegamento (URL) dell'informativa** sulla privacy dell'organizzazione. Si è tenuti a verificare che l'app sia conforme alle leggi e alle normative sulla privacy e a fornire un'informativa sulla privacy valida.

### <a name="contact-information"></a>Informazioni contatto

Specificare il nome, l'indirizzo di posta elettronica e il numero di telefono per **Contatto supporto tecnico** e **Contatto tecnico**. Queste informazioni non vengono visualizzate ai clienti, ma saranno disponibili per Microsoft e possono essere fornite ai partner CSP.

Nella sezione **Contatto di supporto** specificare un valore per **URL supporto** in cui i partner CSP possono trovare supporto per l'offerta. L'URL del supporto non può essere uguale all'URL della Guida.

### <a name="supporting-documents"></a>Documenti di supporto

Fornire qui almeno un documento di marketing correlato (e fino a un massimo di tre), ad esempio white paper, brochure, elenchi di controllo o presentazioni, in formato PDF.

### <a name="marketplace-media"></a>File multimediali del marketplace

Fornire logo e immagini che verranno usati quando si visualizza l'offerta ai clienti. Tutte le immagini devono essere in formato PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio `https://upload.xboxlive.com` usato dal Centro per i partner.

#### <a name="logos"></a>Loghi

Specificare un file PNG per il logo **Di** grandi dimensioni. Partner Center userà questo file iniziale per creare altre dimensioni necessarie. Facoltativamente, è possibile sostituire l'immagine ridimensionata con la propria immagine in un secondo momento.

Questi logo vengono usati in posizioni diverse nell'inserzione:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere screenshot che mostrano il funzionamento dell'offerta. Sono necessari almeno tre screenshot ed è possibile aggiungerne fino a cinque. Tutti gli screenshot devono avere dimensioni di 1280 x 720 pixel e in formato PNG.

#### <a name="videos"></a>Video

Se si vuole, è possibile aggiungere fino a quattro video che descrivono l'offerta. I video devono essere ospitati in un sito esterno. Per ognuno, immettere il nome del video, il relativo indirizzo e un'immagine di anteprima del video (1280 x 720 pixel).

Per altre risorse per la presentazione nel marketplace, vedere [Procedure consigliate per le inserzioni di offerte del marketplace.](../gtm-offer-listing-best-practices.md)

Prima di continuare, selezionare **Salva bozza**.

## <a name="availability"></a>Disponibilità

Questa pagina consente di definire dove e come rendere disponibile l'offerta.

### <a name="markets"></a>Mercati

Per specificare i mercati in cui l'offerta deve essere disponibile, selezionare **Modifica mercati** per visualizzare la finestra **popup Selezione** mercato.

Selezionare almeno un mercato. Scegliere **Seleziona tutto** per rendere disponibile l'offerta in ogni mercato possibile oppure selezionare solo i mercati specifici desiderati. Al termine, fare clic su **Salva**.

Le selezioni qui si applicano solo alle nuove acquisizioni. Se un utente ha già l'app in un determinato mercato e successivamente lo si rimuove, le persone che hanno già l'offerta in tale mercato possono continuare a usarla, ma nessun nuovo cliente in tale mercato sarà in grado di ottenere l'offerta.

> [!IMPORTANT]
> Si è tenuti a soddisfare i requisiti legali locali, anche se non sono elencati qui o nel Centro per i partner. Anche se si selezionano tutti i mercati, le leggi locali, le restrizioni o altri fattori possono impedire l'elenco di determinate offerte in alcuni paesi e aree geografiche.

### <a name="preview-audience"></a>Destinatari dell'anteprima

Prima di pubblicare l'offerta live all'interno della più ampia offerta del marketplace, è necessario renderla disponibile a un **gruppo di destinatari dell'anteprima** limitato. Immettere qui un valore in **Nascondi chiave** (qualsiasi stringa che usa solo lettere minuscole e/o numeri). I membri del gruppo di destinatari in anteprima possono usare questa chiave come token per visualizzare un'anteprima dell'offerta nel marketplace.

Quindi, quando si è pronti a rendere disponibile l'offerta e a rimuovere la restrizione, sarà necessario rimuovere la chiave specificata in **Nascondi chiave** e ripubblicare l'offerta.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Questa pagina definisce i dettagli tecnici usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla.

Le estensioni inviate per l'offerta devono soddisfare i requisiti specificati nell'elenco di controllo [di convalida tecnico](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission).

### <a name="file-upload"></a>Caricamento di file

Se in precedenza è stata selezionata l'opzione Aggiungi in **,** in cui si carica il file del pacchetto dell'offerta, insieme ai file di pacchetto per qualsiasi estensione in cui sono presenti dipendenze.

#### <a name="extension-package-file"></a>File del pacchetto di estensione

Caricare il file del pacchetto dell'estensione (con estensione app) per l'offerta.

#### <a name="library-extension-package-file"></a>File del pacchetto dell'estensione della libreria

Obbligatorio se l'offerta deve essere installata insieme a un'altra estensione che non verrà pubblicata nel marketplace. In questo caso, caricare qui il file con estensione app.

>[!NOTE]
>Il file del pacchetto di dipendenze non viene più usato. Caricare invece un file del pacchetto di estensione della libreria.

Prima di continuare, selezionare **Salva bozza**.

## <a name="supplemental-content"></a>Contenuto supplementare

Questa pagina consente di fornire informazioni aggiuntive per convalidare l'offerta. Queste informazioni non vengono visualizzate ai clienti o pubblicate nel marketplace.

### <a name="target-release"></a>Versione di destinazione

Indicare quale versione di Microsoft Dynamics Business Central è destinata alla soluzione: **Current,** **Next Major** o **Next Minor.** Queste informazioni consentono di testare la soluzione in modo appropriato.

### <a name="supported-editions"></a>Edizioni supportate

Se l'offerta richiede l'edizione Premium di Microsoft Dynamics 365 Business Central, selezionare solo **Premium**. In caso contrario, selezionare sia **Essentials** sia **Premium**.

### <a name="key-usage-scenario"></a>Scenario di utilizzo principale

Caricare un file PDF in cui sono elencati gli scenari di utilizzo chiave dell'offerta. Tutti gli scenari elencati qui possono essere verificati dal team di convalida prima di approvare l'offerta per il marketplace.

### <a name="test-accounts"></a>Account di test

Se è necessario un account di test per consentire al team di certificazione di controllare correttamente l'offerta, caricare un file PDF, DOC o DOCX con le informazioni specificate in **Account di test**.

### <a name="app-tests-automation"></a>Automazione di test delle app

Se l'offerta è un'app che funge da componente aggiuntivo, è necessario caricare un file in **Automazione di test delle app** (con estensione app). Questo file non è applicabile alle app di connessione.

Prima di continuare, selezionare **Salva bozza**.

## <a name="publish"></a>Pubblica

### <a name="submit-offer-to-preview"></a>Inviare l'offerta per l'anteprima

Dopo aver completato tutte le sezioni necessarie dell'offerta, selezionare **Rivedi** e pubblica nell'angolo superiore destro del portale.

Se è la prima volta che si pubblica questa offerta, è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta.
  - **Non avviato:** la sezione non è stata toccata e deve essere completata.
  - **Incompleto:** la sezione contiene errori che devono essere corretti o che richiedono altre informazioni. Tornare alla sezione e aggiornarla.
  - **Completa:** la sezione è stata completata, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in stato completato prima di poter inviare l'offerta.
- Nella sezione **Note per la certificazione** specificare le istruzioni per il test per il team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per conoscere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica quando una versione di anteprima dell'offerta sarà disponibile per la revisione e l'approvazione. Tornare a Partner Center e selezionare **Go-live** per pubblicare l'offerta al pubblico.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
