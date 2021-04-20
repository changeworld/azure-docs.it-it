---
title: Acquisizione da parte dell'amministratore di una directory non gestita - Azure AD | Microsoft Docs
description: Come assumere il controllo di un nome di dominio DNS in un'organizzazione Azure AD (tenant shadow).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 816f4645626675ae19a462ac8707e995c3b4045e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739369"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Acquisire la proprietà di una directory non gestita come amministratore in Azure Active Directory

Questo articolo illustra due modi per acquisire la proprietà di un nome di dominio DNS in una directory non gestita in Azure Active Directory (Azure AD). Quando un utente self-service si iscrive a un servizio cloud che usa Azure AD, viene aggiunto a una directory di Azure AD non gestita basata sul dominio di posta elettronica. Per altre informazioni sull'iscrizione self-service o "virale" per un servizio, vedere Che cos'è l'iscrizione [self-service](directory-self-service-signup.md) per Azure Active Directory?

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decidere come acquisire la proprietà di una directory non gestita
Durante il processo di acquisizione della proprietà da parte di un amministratore, è possibile dimostrare la proprietà come illustrato in [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md). Nelle sezioni successive viene illustrata l'esperienza di amministratore in modo più dettagliato; in ogni caso qui di seguito è riportato un riepilogo:

* Quando si esegue un'[acquisizione "interna" della proprietà da parte dell'amministratore](#internal-admin-takeover) di una directory di Azure non gestita, si viene aggiunti come amministratore globale della directory non gestita. Non viene eseguita la migrazione di utenti, domini o piani di servizio a nessun'altra directory amministrata dall'utente.

* Quando si esegue un'[acquisizione "esterna" della proprietà da parte dell'amministratore](#external-admin-takeover) di una directory di Azure non gestita, si aggiunge il nome di dominio DNS della directory non gestita alla directory di Azure gestita. Quando si aggiunge il nome di dominio, viene creato un mapping tra utenti e risorse nella directory di Azure gestita in modo che gli utenti possano continuare ad accedere ai servizi senza interruzioni. 

## <a name="internal-admin-takeover"></a>Acquisizione di amministrazione interna

Alcuni prodotti che includono SharePoint e OneDrive, ad esempio Microsoft 365, non supportano l'acquisizione esterna. Se si tratta di uno scenario specifico o se si è un amministratore e si vuole assumere il controllo di un'organizzazione Azure AD non gestita o "shadow" creata dagli utenti che hanno usato l'iscrizione self-service, è possibile eseguire questa operazione con un'acquisizione interna dell'amministratore.

1. Creare un contesto utente nell'organizzazione non gestita tramite l'registrazione per Power BI. Per semplificare l'esempio, questa procedura presuppone tale percorso.

2. Aprire il [sito di Power BI](https://powerbi.com) e selezionare **Inizia gratuitamente**. Immettere un account utente che usa il nome di dominio per l'organizzazione, ad esempio `admin@fourthcoffee.xyz`. Dopo avere immesso il codice di verifica, verificare la disponibilità del codice di conferma nella posta elettronica.

3. Nel messaggio di conferma da Power BI selezionare **Yes, that's me** (Sì, sono io).

4. Accedere al interfaccia di amministrazione di Microsoft 365 [con](https://portal.office.com/admintakeover) l'account Power BI utente. Viene visualizzato un messaggio che  indica di diventare l'amministratore del nome di dominio già verificato nell'organizzazione non gestita. Selezionare **Yes, I want to be the admin** (Sì, voglio essere l'amministratore).
  
   ![Primo screenshot per Become the Admin (Diventa l'amministratore)](./media/domains-admin-takeover/become-admin-first.png)
  
5. Aggiungere il record TXT per dimostrare di essere il proprietario del nome di dominio **fourthcoffee.xyz** nel registrar. In questo esempio si tratta di GoDaddy.com.
  
   ![Aggiungere un record TXT per il nome di dominio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando i record TXT DNS vengono verificati nel registrar dei nomi di dominio, è possibile gestire l'organizzazione Azure AD dns.

Dopo aver completato i passaggi precedenti, si è ora l'amministratore globale dell'organizzazione Fourth Coffee Microsoft 365. Per integrare il nome di dominio con gli altri servizi di Azure, è possibile rimuoverlo da Microsoft 365 e aggiungerlo a un'organizzazione gestita diversa in Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Aggiunta del nome di dominio a un'organizzazione gestita in Azure AD

1. Aprire il [interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).
2. Selezionare **la scheda** Utenti e creare un nuovo account utente con un nome come *fourthcoffeexyz.onmicrosoft.com \@ che* non usa il nome di dominio personalizzato. 
3. Assicurarsi che il nuovo account utente abbia privilegi di amministratore globale per l'Azure AD aziendale.
4. Aprire **la scheda** Domini nel interfaccia di amministrazione di Microsoft 365, selezionare il nome di dominio e selezionare **Rimuovi**. 
  
   ![Rimuovere il nome di dominio da Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se sono presenti utenti o gruppi in Microsoft 365 che fanno riferimento al nome di dominio rimosso, devono essere rinominati nel dominio .onmicrosoft.com. Se si forza l'eliminazione del nome di dominio, tutti gli utenti vengono rinominati automaticamente, in questo esempio in *modo \@* da fourthcoffeexyz.onmicrosoft.com .
  
6. Accedere all'interfaccia [Azure AD di amministrazione](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account di amministratore globale per l'Azure AD aziendale.
  
7. Selezionare **Nomi di dominio personalizzati** e quindi aggiungere il nome di dominio. Sarà necessario immettere i record TXT DNS per verificare la proprietà del nome di dominio. 
  
   ![dominio verificato come aggiunto a Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Tutti gli utenti del Power BI o del servizio azure Rights Management a cui sono assegnate licenze nell'organizzazione Microsoft 365 devono salvare i propri dashboard se il nome di dominio viene rimosso. Devono accedere con un nome utente come user fourthcoffeexyz.onmicrosoft.com *invece \@* di *fourthcoffee.xyz \@*.

## <a name="external-admin-takeover"></a>Acquisizione di amministrazione esterna

Se si gestisce già un'organizzazione con i servizi di Azure o Microsoft 365, non è possibile aggiungere un nome di dominio personalizzato se è già verificato in un'altra organizzazione Azure AD. Tuttavia, dall'organizzazione gestita in Azure AD è possibile assumere la proprietà di un'organizzazione non gestita come amministratore esterno. La procedura generale segue l'articolo [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md).

Quando si verifica la proprietà del nome di dominio, Azure AD rimuove il nome di dominio dall'organizzazione non gestita e lo sposta nell'organizzazione esistente. L'acquisizione esterna della proprietà di una directory non gestita da parte di un amministratore richiede lo stesso processo convalida di convalida TXT DNS dell'acquisizione interna della proprietà da parte di un amministratore, con la differenza che anche gli elementi seguenti vengono spostati con il nome di dominio:

- Utenti
- Sottoscrizioni
- Assegnazioni di licenze

### <a name="support-for-external-admin-takeover"></a>Supporto per l'acquisizione esterna della proprietà da parte dell'amministratore
L'acquisizione esterna della proprietà da parte dell'amministratore è supportata dai servizi online seguenti:

- Azure Rights Management
- Exchange Online

I piani di servizio supportati includono:

- PowerApps gratuito
- PowerFlow gratuito
- RMS per utenti singoli
- Microsoft Stream
- Versione di valutazione gratuita di Dynamics 365

L'acquisizione della proprietà da parte dell'amministratore esterno non è supportata per i servizi con piani di servizio che includono SharePoint, OneDrive o Skype For Business. ad esempio tramite una sottoscrizione gratuita di Office. 

Facoltativamente, è possibile usare l'opzione [ **ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) per rimuovere il nome di dominio dall'organizzazione non gestita e verificarlo nell'organizzazione desiderata. 

#### <a name="more-information-about-rms-for-individuals"></a>Altre informazioni su RMS per utenti singoli

Per [RMS](/azure/information-protection/rms-for-individuals) [per utenti](/azure/information-protection/plan-implement-tenant-key) singoli, quando l'organizzazione non gestita si trova nella stessa area dell'organizzazione di cui si è proprietari, la chiave dell'organizzazione di Azure Information Protection creata automaticamente e i modelli di protezione predefiniti vengono spostati con il nome di dominio. [](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates)

La chiave e i modelli non vengono spostati quando l'organizzazione non gestita si trova in un'area diversa. Ad esempio, se l'organizzazione non gestita si trova in Europa e l'organizzazione di cui si è proprietari si trova America del Nord.

Anche se RMS per utenti singoli è progettato per supportare l'autenticazione Azure AD per l'apertura di contenuti protetti, non impedisce agli utenti di proteggere a loro volta il contenuto. Se gli utenti hanno protetto il contenuto con la sottoscrizione RMS per utenti singoli e la chiave e i modelli non sono stati spostati, tale contenuto non è accessibile dopo l'acquisizione del dominio.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlet di Azure AD PowerShell per l'opzione ForceTakeover
È possibile visualizzare questi cmdlet usati in [Esempio di PowerShell](#powershell-example).

Cmdlet | Utilizzo
------- | -------
`connect-msolservice` | Quando richiesto, accedere all'organizzazione gestita.
`get-msoldomain` | Visualizza i nomi di dominio associati all'organizzazione corrente.
`new-msoldomain –name <domainname>` | Aggiunge il nome di dominio all'organizzazione come Non verificata (non è stata ancora eseguita alcuna verifica DNS).
`get-msoldomain` | Il nome di dominio è ora incluso nell'elenco dei nomi di dominio associati all'organizzazione gestita, ma è elencato come **Unverified**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornisce le informazioni da inserire nel nuovo record TXT DNS per il dominio (MS=xxxxx). La verifica potrebbe non essere eseguita immediatamente perché la propagazione del record TXT richiede tempo. Attendere quindi alcuni minuti prima di prendere in considerazione l'opzione **-ForceTakeover**. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se il nome di dominio continua a non essere verificato, è possibile procedere con l'opzione **-ForceTakeover**, che verifica che il record TXT sia stato creato e avvia il processo di acquisizione della proprietà.<li>L'opzione **-ForceTakeover** deve essere aggiunta al cmdlet solo quando si forza l'acquisizione di un amministratore esterno, ad esempio quando l'organizzazione non gestita dispone Microsoft 365 servizi che bloccano l'acquisizione.
`get-msoldomain` | Nell'elenco di domini il nome di dominio ora risulta **Verificato**.

> [!NOTE]
> L'organizzazione Azure AD non gestita viene eliminata 10 giorni dopo l'esercizio dell'opzione della forza di acquisizione esterna.

### <a name="powershell-example"></a>Esempio di PowerShell

1. Connettersi ad Azure AD usando le credenziali usate per rispondere all'offerta self-service:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Ottenere un elenco di domini:
  
   ```powershell
   Get-MsolDomain
   ```
3. Eseguire il cmdlet Get-MsolDomainVerificationDns per creare una richiesta di verifica:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Ad esempio:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copiare il valore (la richiesta di verifica) restituita da questo comando. Ad esempio:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Nello spazio dei nomi DNS pubblico creare un record txt DNS che contiene il valore copiato nel passaggio precedente. Il nome per questo record è quello del dominio padre, quindi se si crea questo record di risorse usando il ruolo DNS di Windows Server, lasciare il nome record vuoto e incollare solo il valore nella casella di testo.
6. Eseguire il cmdlet Confirm-MsolDomain per verificare la richiesta di verifica:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Ad esempio:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Una richiesta di verifica riporta al prompt senza errori.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md)
* [Come installare e configurare Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Informazioni di riferimento sui cmdlet di Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
