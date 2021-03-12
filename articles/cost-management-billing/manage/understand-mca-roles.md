---
title: Ruoli di fatturazione per i Contratti del cliente Microsoft - Azure
description: Questo articolo offre informazioni sui ruoli di fatturazione per gli account di fatturazione di Azure per i contratti dei clienti Microsoft.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011664"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Informazioni sui ruoli amministrativi per i contratti dei clienti Microsoft in Azure

Per gestire l'account di fatturazione per un contratto del cliente Microsoft, usare i ruoli descritti nelle sezioni seguenti. Questi ruoli si aggiungono ai ruoli predefiniti disponibili in Azure per controllare l'accesso alle risorse. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

Per informazioni su come controllare l'accesso all'account di fatturazione di Microsoft Customer Agreement (MCA), guardare il video su come [gestire l'accesso al proprio account di fatturazione di MCA](https://www.youtube.com/watch?v=9sqglBlKkho) .

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>Definizioni dei ruoli di fatturazione

La tabella seguente descrive i ruoli di fatturazione usati per gestire l'account di fatturazione, i profili di fatturazione e le sezioni delle fatture.

|Ruolo|Descrizione|
|---|---|
|Proprietario dell'account di fatturazione|Consente di gestire tutti gli elementi per l'account di fatturazione|
|Collaboratore per l'account di fatturazione|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni nell'account di fatturazione|
|Lettore per l'account di fatturazione|Fornisce una visualizzazione di sola lettura di tutti gli elementi nell'account di fatturazione|
|Proprietario del profilo di fatturazione|Consente di gestire tutti gli elementi per il profilo di fatturazione|
|Collaboratore per il profilo di fatturazione|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni per il profilo di fatturazione|
|Lettore per il profilo di fatturazione|Fornisce una visualizzazione di sola lettura di tutti gli elementi nel profilo di fatturazione|
|Gestione fatture|Consente di visualizzare e pagare le fatture per il profilo di fatturazione|
|Proprietario della sezione della fattura|Consente di gestire tutti gli elementi nella sezione della fattura|
|Collaboratore per la sezione della fattura|Consente di gestire tutti gli elementi ad eccezione delle autorizzazioni nella sezione della fattura|
|Ruolo con autorizzazioni di lettura per la sezione della fattura|Fornisce una visualizzazione di sola lettura di tutti gli elementi nella sezione della fattura|
|Autore di sottoscrizioni di Azure|Creare sottoscrizioni di Azure|

## <a name="billing-account-roles-and-tasks"></a>Ruoli e attività dell'account di fatturazione

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. I ruoli dell'account di fatturazione hanno il livello più elevato di autorizzazioni e gli utenti di questi ruoli ottengono la visibilità delle informazioni sui costi e sulla fatturazione per l'intero account. Assegnare questi ruoli solo agli utenti che devono visualizzare le fatture e tenere traccia dei costi per l'intero account, ad esempio i membri dei team Finance e contabilità. Per altre informazioni, vedere [Informazioni sull'account di fatturazione](../understand/mca-overview.md#your-billing-account).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto dell'account di fatturazione.

### <a name="manage-billing-account-permissions-and-properties"></a>Gestire le autorizzazioni e le proprietà dell'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizzare le assegnazioni di ruolo per l'account di fatturazione|✔|✔|✔|
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire l'account di fatturazione|✔|✘|✘|
|Visualizzare le proprietà dell'account di fatturazione, ad esempio indirizzo, contratti e altro ancora|✔|✔|✔|
|Aggiornare le proprietà dell'account di fatturazione, ad esempio venduto, nome visualizzato e altro ancora|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Gestire i profili di fatturazione per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizza tutti i profili di fatturazione per l'account|✔|✔|✔|
|Crea nuovi profili di fatturazione|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Gestire le fatture per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizza tutte le fatture per l'account|✔|✔|✔|
|Pagare le fatture con la carta di credito|✔|✔|✘|
|Scaricare le fatture, i file di utilizzo di Azure, gli elenchi prezzi e i documenti fiscali|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Gestisci i prodotti per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizza tutti i prodotti acquistati per l'account|✔|✔|✔|
|Gestisci la fatturazione per prodotti come Annulla, disattiva il rinnovo automatico e altro ancora|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Gestire le sottoscrizioni per l'account di fatturazione

|Attività|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione|
|---|---|---|---|
|Visualizza tutte le sottoscrizioni di Azure create per l'account di fatturazione|✔|✔|✔|
|Crea nuove sottoscrizioni di Azure|✔|✔|✘|
|Annulla le sottoscrizioni di Azure|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Ruoli e attività del profilo di fatturazione

Ogni account di fatturazione ha almeno un profilo di fatturazione. Il primo profilo di fatturazione viene configurato quando si esegue l'iscrizione per usare Azure. Viene generata una fattura mensile per il profilo di fatturazione che contiene tutti gli addebiti associati del mese precedente. È possibile configurare più profili di fatturazione in base alle esigenze. Gli utenti con ruoli in un profilo di fatturazione possono visualizzare i costi, impostare budget, gestire e pagare le fatture. Assegnare questi ruoli agli utenti responsabili della gestione del budget e del pagamento delle fatture per il profilo di fatturazione, ad esempio i membri dei team di amministrazione aziendali dell'organizzazione. Per altre informazioni, vedere [Informazioni sui profili di fatturazione](../understand/mca-overview.md#billing-profiles).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto del profilo di fatturazione.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gestire le autorizzazioni, le proprietà e i criteri del profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare le assegnazioni di ruolo per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire il profilo di fatturazione|✔|✘|✘|✘|✔|✘|✘|
|Visualizzare le proprietà del profilo di fatturazione come il numero di ordine di acquisto, la fatturazione e altro ancora|✔|✔|✔|✔|✔|✔|✔|
|Aggiornare le proprietà del profilo di fatturazione |✔|✔|✘|✘|✔|✔|✘|
|Visualizza i criteri applicati al profilo di fatturazione come gli acquisti di prenotazione di Azure, gli acquisti in Azure Marketplace e altro ancora|✔|✔|✔|✔|✔|✔|✔|
|Applicare criteri al profilo di fatturazione |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gestire le fatture per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le fatture per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Pagare le fatture con la carta di credito|✔|✔|✘|✔|✔|✘|✘|
|Scaricare le fatture, i file sull'utilizzo e gli addebiti di Azure, gli elenchi prezzi e i documenti fiscali per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gestire le sezioni della fattura per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sezioni della fattura per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Creare nuove sezioni della fattura per il profilo di fatturazione|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Gestisci prodotti per profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizza tutti i prodotti acquistati per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Gestisci la fatturazione per prodotti come Annulla, disattiva il rinnovo automatico e altro ancora|✔|✔|✘|✘|✔|✔|✘|
|Modificare il profilo di fatturazione per i prodotti|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Gestire i metodi di pagamento per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare i metodi di pagamento per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Gestisci metodi di pagamento come la sostituzione della carta di credito, lo scollegamento della carta di credito e altro ancora|✔|✔|✘|✘|✔|✔|✘|
|Tenere traccia del saldo dei crediti di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gestire le sottoscrizioni per il profilo di fatturazione

|Attività|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione|Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione
|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per il profilo di fatturazione|✔|✔|✔|✔|✔|✔|✔|
|Crea nuove sottoscrizioni di Azure|✔|✔|✘|✘|✔|✔|✘|
|Annulla le sottoscrizioni di Azure|✘|✘|✘|✘|✘|✘|✘|
|Modificare il profilo di fatturazione per le sottoscrizioni di Azure|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Ruoli e attività della sezione della fattura

Per impostazione predefinita, ogni profilo di fatturazione contiene una sezione di fattura. Per raggruppare i costi per la fattura del profilo di fatturazione, è possibile creare più sezioni della fattura.  Gli utenti con ruoli in una sezione relativa alla fattura possono controllare chi crea sottoscrizioni di Azure ed effettuare altri acquisti. Assegnare questi ruoli agli utenti che configurano l'ambiente Azure per i team dell'organizzazione, ad esempio i responsabili e gli architetti tecnici. Per altre informazioni,, vedere [Informazioni sulla sezione della fattura](../understand/mca-overview.md#invoice-sections).

Le tabelle seguenti indicano il ruolo necessario per completare le attività nel contesto delle sezioni della fattura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gestire le autorizzazioni e le proprietà della sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione |Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visualizzare le assegnazioni di ruolo per la sezione della fattura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Concedere ad altri utenti le autorizzazioni per visualizzare e gestire la sezione della fattura|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Visualizzare le proprietà della sezione della fattura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Aggiornare le proprietà della sezione della fattura|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Gestire i prodotti per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione |Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visualizza tutti i prodotti acquistati per la sezione fattura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Gestisci la fatturazione per prodotti come Annulla, disattiva il rinnovo automatico e altro ancora|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Modificare la sezione della fattura per i prodotti|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gestire le sottoscrizioni per la sezione della fattura

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione |Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visualizzare tutte le sottoscrizioni di Azure per la sezione della fattura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Creare sottoscrizioni di Azure|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Annulla le sottoscrizioni di Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Sezione relativa alla modifica della fattura per la sottoscrizione di Azure|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Richiedere la proprietà della fatturazione delle sottoscrizioni da utenti in altri account di fatturazione|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Ruoli e attività di fatturazione della sottoscrizione

La tabella seguente indica il ruolo necessario per completare le attività nel contesto di una sottoscrizione.

|Attività|Proprietario della sezione della fattura|Collaboratore per la sezione della fattura|Ruolo con autorizzazioni di lettura per la sezione della fattura|Autore di sottoscrizioni di Azure|Proprietario del profilo di fatturazione|Collaboratore per il profilo di fatturazione|Lettore per il profilo di fatturazione |Gestione fatture|Proprietario dell'account di fatturazione|Collaboratore per l'account di fatturazione|Lettore per l'account di fatturazione 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Creare sottoscrizioni|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Aggiornare il centro di costo per la sottoscrizione|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Modificare la sezione della fattura per la sottoscrizione|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Modificare il profilo di fatturazione per la sottoscrizione|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Annulla le sottoscrizioni di Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gestire i ruoli di fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Selezionare **Controllo di accesso (IAM)** in un ambito come l'account di fatturazione, il profilo di fatturazione o la sezione della fattura, a cui si vuole concedere l'accesso.

4. Nella pagina Controllo di accesso (IAM) sono elencati gli utenti e i gruppi assegnati a ogni ruolo per tale ambito.

   ![Screenshot che mostra l'elenco degli amministratori dell'account di fatturazione](./media/understand-mca-roles/billing-list-admins.png)

5. Per concedere l'accesso a un utente, selezionare **Aggiungi** nella parte superiore della pagina. Nell'elenco a discesa Ruolo selezionare un ruolo. Immettere l'indirizzo di posta elettronica dell'utente a cui si vuole concedere l'accesso. Selezionare **Salva** per assegnare il ruolo.

   ![Screenshot che mostra l'aggiunta di un amministratore a un account di fatturazione](./media/understand-mca-roles/billing-add-admin.png)

6. Per rimuovere l'accesso per un utente, selezionare l'utente con l'assegnazione di ruolo che si vuole rimuovere. Selezionare Rimuovi.

   ![Screenshot che mostra la rimozione di un amministratore da un account di fatturazione](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico
Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Introduzione all'account di fatturazione per il contratto del cliente Microsoft](../understand/mca-overview.md)
- [Creare un'altra sottoscrizione di Azure per il contratto del cliente Microsoft](create-subscription.md)
