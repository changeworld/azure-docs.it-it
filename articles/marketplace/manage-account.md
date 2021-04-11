---
title: Come gestire un account Marketplace commerciale nel centro per i partner Microsoft-Azure Marketplace
description: Informazioni su come gestire un account del marketplace commerciale nel Centro per i partner Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108342"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Come gestire il proprio account del marketplace commerciale nel Centro per i partner

**Ruoli appropriati**

- Proprietario
- Manager

Dopo aver [creato un account del Centro per i partner](./create-account.md), è possibile usare il [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per gestire l'account e le offerte.

## <a name="access-your-account-settings"></a>Accedere alle impostazioni dell'account

Se non è già stato fatto, l'utente o l'amministratore dell'organizzazione deve accedere alle impostazioni dell' [account](https://partner.microsoft.com/dashboard/account/management) per l'account del centro per i partner.

1. Accedere al dashboard del [Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel centro per i partner con l'account a cui si vuole accedere. Se si fa parte di più account e si è effettuato l'accesso con un altro, è possibile [cambiare account](switch-accounts.md).
1. In alto a destra selezionare **Impostazioni** (icona a forma di ingranaggio) e quindi selezionare **Impostazioni account**.

    [![Screenshot del menu delle impostazioni dell'account nel centro per i partner. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. In **Impostazioni account** selezionare **legale**. Selezionare quindi la scheda **Developer** per visualizzare i dettagli relativi all'account Marketplace commerciale.

    [![Screenshot della scheda Developer nella pagina Legal delle impostazioni dell'account. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Pagina Impostazioni account

Quando si seleziona **Impostazioni** e si espandono le **impostazioni dell'account**, la visualizzazione predefinita è **informazioni legali**. In questa pagina possono essere presenti fino a tre schede, a seconda dei programmi registrati in: _partner_, _rivenditore_ e _sviluppatore_.

La scheda **partner** , per i partner registrati in MPN, include:

- Tutte le informazioni aziendali legali, ad esempio il nome e l'indirizzo validi registrati per la società
- Contatto principale
- Località aziendali.

La scheda **Reseller** , per i partner che operano nel settore CSP, include:

- Informazioni di contatto principale
- Profilo di supporto clienti
- Informazioni sul programma

La scheda **Developer** , per i partner iscritti a qualsiasi programma per sviluppatori, presenta le informazioni seguenti:

- **Dettagli dell'account**: tipo di account e stato dell'account
- ID **editore**: ID venditore, ID utente, ID editore, tenant Azure ad e altro ancora
- **Informazioni di contatto**: nome visualizzato dell'editore, contatto del venditore (nome, indirizzo di posta elettronica, telefono e indirizzo) e responsabile approvazione della società (nome, indirizzo di posta elettronica, telefono)

### <a name="account-settings---developer-tab"></a>Impostazioni account-scheda Developer

Le informazioni seguenti descrivono le informazioni nella scheda Developer.

#### <a name="account-details"></a>Dettagli dell'account

Nella sezione _Dettagli account_ della scheda _Developer_ è possibile visualizzare le informazioni di base, ad esempio il **tipo di account** (società o utente singolo) e lo **stato di verifica** dell'account. Durante il processo di verifica dell'account, queste impostazioni visualizzeranno ogni passaggio necessario, tra cui la verifica dell'indirizzo di posta elettronica, la verifica dell'impiego e verifica dell'azienda.

#### <a name="publisher-ids"></a>ID editore

Nella sezione ID editore è possibile visualizzare l' **ID Symantec** (se applicabile), **ID venditore**, **ID utente**, **ID MPN** e **tenant Azure ad**. Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account sviluppatore e non possono essere modificati.

Se non si dispone di un ID Symantec, è possibile selezionare il collegamento per richiederne uno.

### <a name="contact-info"></a>Informazioni di contatto

Nella sezione _informazioni di contatto_ è possibile visualizzare il **nome visualizzato dell'editore**, le informazioni di contatto del **venditore** (il nome del contatto, la posta elettronica, il numero di telefono e l'indirizzo del venditore della società) e il **responsabile approvazione della società** (nome, indirizzo di posta elettronica e numero di telefono dell'utente con autorità per approvare le decisioni per la società).

È anche possibile selezionare il collegamento **Aggiorna** per modificare le informazioni di contatto, ad esempio nome visualizzato dell'editore e indirizzo di posta elettronica.

### <a name="account-settings-identifiers"></a>Identificatori delle impostazioni dell'account

In   >  **profilo organizzazione** impostazioni account selezionare **identificatori** per visualizzare le informazioni seguenti:

- **ID MPN**: qualsiasi ID MPN associato all'account
- **CSP**: ID MPN associati al programma CSP per l'account.
- **Server di pubblicazione**: ID venditore associati al proprio account
- **GUID di rilevamento**: qualsiasi GUID di rilevamento associato all'account

#### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci globali (GUID) sono numeri di riferimento univoci (con 32 cifre esadecimali) che possono essere usati per tenere traccia dell'utilizzo di Azure.

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni offerta e canale di distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare due GUID distinti:

- Prodotto A su Azure Marketplace
- Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È anche possibile tenere traccia dei GUID a un livello più granulare allineandosi a ogni piano dell'offerta.

Per altre informazioni, vedere la pagina relativa alla [Verifica dell'utilizzo dei clienti di Azure con GUID](azure-partner-customer-usage-attribution.md#faq).

### <a name="agreements"></a>Contratti

La pagina **contratti** consente di visualizzare un elenco dei contratti di pubblicazione autorizzati. Questi contratti sono elencati in base al nome e al numero di versione, inclusa la data di accettazione e il nome dell'utente che ha accettato il contratto.

Per accedere alla pagina dei contratti:

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
1. In alto a destra selezionare **Impostazioni**  >  **Impostazioni account**.
1. In **Impostazioni account** selezionare **contratti**.

È possibile che nella parte superiore della pagina venga visualizzato il messaggio **Azioni necessarie** se sono presenti aggiornamenti dei contratti che richiedono attenzione. Per accettare un contratto aggiornato, leggere prima la versione del contratto collegata, quindi selezionare **Accetto contratto**.

## <a name="set-up-a-payout-profile"></a>Configurare un profilo di pagamento

Un profilo di pagamento è il conto bancario a cui vengono inviati i proseguimenti dalle vendite. Questo account bancario deve trovarsi nello stesso paese o area geografica in cui è stato registrato l'account del centro per i partner. Per altre informazioni su un profilo di pagamento, vedere [creare e gestire incentivi per il pagamento e i profili fiscali nel centro per i partner](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) e [configurare l'account di pagamento e i moduli fiscali](/partner-center/set-up-your-payout-account).

Per configurare il profilo di pagamento:

1. Passare alla [pagina di panoramica del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel Centro per i partner.
2. Nella sezione **profilo** , accanto a **profilo di pagamento**, selezionare **Aggiorna**.
3. **Scegliere un metodo di pagamento**: conto bancario o PayPal.
4. **Aggiungere le informazioni di pagamento**: potrebbe essere necessario scegliere un tipo di conto (corrente o di risparmio) e immettere il nome del titolare del conto, il numero di conto, il numero di registrazione, l'indirizzo di fatturazione, il numero di telefono o l'indirizzo di posta elettronica PayPal. Per ulteriori informazioni sull'utilizzo di PayPal come metodo di pagamento dell'account e per sapere se è supportato nel mercato o nell'area geografica, vedere [informazioni su PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come avviene per la configurazione iniziale dell'account proventi. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

## <a name="tax-profile"></a>Profilo fiscale

Esaminare lo stato del profilo fiscale corrente, verificando che il **Tipo di entità** e le **Informazioni certificato fiscale** visualizzati siano corretti. Selezionare **Modifica** per aggiornare o completare i moduli necessari.

Per stabilire lo stato dell'imposta, è necessario specificare il paese o la regione di residenza e cittadinanza e completare i moduli fiscali appropriati associati al paese o all'area geografica.

Indipendentemente dal paese o dall'area geografica della residenza o della cittadinanza, è necessario compilare Stati Uniti moduli fiscali per vendere eventuali offerte tramite Microsoft. I partner che soddisfano determinati requisiti di residenza negli Stati Uniti devono compilare un modulo IRS W-9. Gli altri partner al di fuori degli Stati Uniti devono compilare un modulo IRS W-8. Questi moduli possono essere compilati online quando si completa il profilo fiscale.

Non è necessario un codice identificativo del contribuente statunitense per ricevere i pagamenti da Microsoft o per richiedere i vantaggi della convenzione fiscale.

I moduli fiscali possono essere completati e inviati elettronicamente nel Centro per i partner. Nella maggior parte dei casi non è necessario stampare e inviare per posta alcun modulo.

Paesi e aree geografiche diversi hanno requisiti fiscali diversi. L'importo esatto delle imposte da pagare dipende dai paesi e dalle aree geografiche in cui viene venduta l'offerta. Microsoft ha fatturato le vendite e usa le imposte per conto dell'utente in alcuni paesi e regioni. Questi paesi e regioni verranno identificati nel processo di visualizzazione dell'offerta. In altri paesi e regioni, a seconda della posizione in cui viene effettuata la registrazione, potrebbe essere necessario ripartire le vendite e utilizzare le imposte per le vendite direttamente all'autorità di tassazione locale. Gli introiti derivanti dalla vendita potrebbero anche essere tassabili come redditi. È consigliabile contattare l'autorità competente per il proprio paese o area geografica, che fornirà tutte le informazioni fiscali necessarie per le transazioni di vendita di Microsoft.

Per altre informazioni su un profilo fiscale, vedere [creare e gestire incentivi per il pagamento e i profili fiscali nel centro per i partner](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) e [configurare l'account di pagamento e i moduli fiscali](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Aliquote di ritenuta d'acconto

Le informazioni inviate nei moduli fiscali determinano l'aliquota di ritenuta d'acconto appropriata. L'aliquota di ritenuta d'acconto si applica solo alle vendite avvenute negli Stati Uniti. Le vendite in aree diverse dagli Stati Uniti non sono soggette a ritenuta. Le aliquote di ritenuta d'acconto variano, ma per la maggior parte degli sviluppatori registrati al di fuori degli Stati Uniti l'aliquota predefinita è del 30%. È possibile ridurre questa frequenza se il paese o l'area geografica ha accettato un accordo fiscale sul reddito con la Stati Uniti.

### <a name="tax-treaty-benefits"></a>Vantaggi della convenzione fiscale

Se non si risiede negli Stati Uniti, è possibile beneficiare dei vantaggi della convenzione fiscale. Questi vantaggi variano a seconda del paese e dell'area geografica e possono ridurre la quantità di imposte ritenute da Microsoft. È possibile richiedere i vantaggi della convenzione fiscale completando la parte II del modulo W-8BEN. È consigliabile rivolgersi alle autorità competenti del proprio paese o area geografica per determinare l'applicabilità di tali vantaggi alla propria situazione.

[Altre informazioni dettagliate sulle imposte per gli sviluppatori di app/giochi per Windows e per gli editori di Azure Marketplace](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Stato di blocco pagamenti

Per impostazione predefinita, Microsoft invia i pagamenti su base mensile. Tuttavia, è possibile mettere in attesa i pagamenti, in modo da impedire l'invio di pagamenti al proprio account. Se si sceglie di bloccare i pagamenti, i ricavi guadagnati continueranno a essere registrati e i dettagli saranno disponibili nel **Riepilogo proventi**. Non verranno tuttavia inviati pagamenti all'account fino a quando non sarà rimosso il blocco.

**Per inserire i pagamenti in attesa**:

1. Passare a **Impostazioni account**. 
1. Nella finestra di navigazione a sinistra espandere **pagamenti e imposte** e selezionare i **profili di pagamento e di imposta**.
1. Selezionare il programma per il quale si desidera mantenere i pagamenti, quindi selezionare la casella di controllo **Mantieni il pagamento** .

È possibile modificare lo stato di blocco dei pagamenti in qualsiasi momento, ma tenere presente che la decisione avrà effetto sul pagamento mensile successivo. Se ad esempio si vuole bloccare il pagamento di aprile, assicurarsi di impostare lo stato di blocco dei pagamenti su **Sì** entro la fine di marzo.

Dopo aver impostato lo stato di attesa del pagamento **su on**, tutti i pagamenti saranno in attesa **fino a quando** il dispositivo di scorrimento non viene disattivato. Così facendo, si viene inclusi nel successivo ciclo di pagamento mensile, a condizione che vengano raggiunte le soglie di pagamento applicabili. Se ad esempio i pagamenti sono stati bloccati ma si vuole che venga generato un pagamento a giugno, impostare lo stato di blocco dei pagamenti su **No** entro la fine di maggio.

> [!NOTE]
> La selezione **dello stato di mantenimento dei pagamenti** si applica a **tutte le** origini dei ricavi pagate tramite il centro per i partner Microsoft, tra cui Azure Marketplace, Microsoft AppSource, Microsoft Store, pubblicità e così via. Non è possibile selezionare stati di blocco diversi per ogni fonte dei ricavi.

## <a name="devices"></a>Dispositivi

Le impostazioni di gestione del dispositivo si applicano solo alla pubblicazione della piattaforma UWP (Universal Windows Platform). [Altre informazioni](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

## <a name="create-a-billing-profile"></a>Creare un profilo di fatturazione

Se si pubblica un'offerta [Dynamics 365 for Customer Engagement e Power Apps](./partner-center-portal/create-new-customer-engagement-offer.md) o [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md), è necessario completare il *profilo di fatturazione*.

L'indirizzo di fatturazione viene prepopolato in base alla persona giuridica ed è possibile aggiornarlo in un secondo momento. I campi TAX e VAT ID sono obbligatori per alcuni paesi e facoltativi per altri. Non è possibile modificare il nome del paese o dell'area geografica e il nome della società.

1. Passare a **Impostazioni account**.
1. Quindi, nel profilo di spostamento a sinistra espandere **profilo organizzazione** e selezionare **profilo di fatturazione**.


## <a name="multi-user-account-management"></a>Gestione di account multiutente

Il centro per i partner USA [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) per l'accesso e la gestione di account multiutente. L'istanza di Azure AD dell'organizzazione viene automaticamente associata all'account del Centro per i partner durante il processo di registrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere e gestire utenti](add-manage-users.md)
