---
title: Proteggere l'accesso esterno a Microsoft teams, SharePoint e OneDrive con Azure Active Directory
description: Accesso sicuro ai servizi Microsoft 365 come parte della sicurezza complessiva dell'accesso esterno.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565139"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Sicurezza dell'accesso esterno a Microsoft teams, SharePoint e OneDrive for business 

Microsoft teams, SharePoint e OneDrive for business sono tre dei modi più usati per collaborare e condividere contenuto con utenti esterni. Se i metodi "approvati" sono troppo restrittivi, gli utenti andranno al di fuori dei metodi approvati tramite posta elettronica del contenuto o impostando processi e applicazioni esterni non sicuri, ad esempio un DropBox personale o OneDrive. L'obiettivo è quello di bilanciare le esigenze di sicurezza con facilità di collaborazione.

Questo articolo illustra come determinare e configurare la collaborazione esterna per soddisfare gli obiettivi aziendali usando Microsoft teams e SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>La governance inizia in Azure Active Directory

La condivisione in Microsoft 365 è parzialmente regolata dalle [identità esterne | Impostazioni di collaborazione esterna](https://aad.portal.azure.com/) in Azure Active Directory (Azure ad). Se la condivisione esterna è disabilitata o limitata in Azure AD, sostituisce le impostazioni di condivisione configurate nel Microsoft 365. Un'eccezione è che se Azure AD integrazione B2B non è abilitata, è possibile configurare SharePoint e OneDrive per supportare la condivisione ad hoc tramite i codici di accesso monouso (OTP).

![Screenshot delle impostazioni di collaborazione esterna](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Accesso utente guest

Sono disponibili tre opzioni per l'accesso degli utenti guest, che controlla quali utenti guest possono visualizzare dopo essere stati invitati. 

Per impedire agli utenti guest di visualizzare i dettagli di altri utenti guest e di poter enumerare l'appartenenza al gruppo, scegliere gli utenti Guest hanno accesso limitato alle proprietà e alle appartenenze degli oggetti directory.

### <a name="guest-invite-settings"></a>Impostazioni di invito Guest

Queste impostazioni determinano gli utenti che possono invitare i guest e il modo in cui tali Guest possono essere invitati. Queste impostazioni sono abilitate solo se è abilitata l'integrazione con B2B.

È consigliabile abilitare gli amministratori e gli utenti nel ruolo mittente dell'invito Guest. Questa impostazione consente di configurare i processi di collaborazione controllati, come nell'esempio seguente.

* Il proprietario del team invia un ticket a cui viene assegnato il ruolo di invito Guest e

   * Diventa responsabile di tutti gli inviti Guest.

   * Accetta di non aggiungere direttamente utenti a SharePoint sottostante

   * È responsabile per eseguire verifiche di accesso regolari e revocare l'accesso in base alle esigenze.

* Centrale esegue le operazioni seguenti

   * Consente la condivisione esterna concedendo il ruolo richiesto al completamento del training.

   * Assegna Azure AD licenza P2 al proprietario del gruppo Microsoft 365 per abilitare le verifiche di accesso.
   * Crea una verifica di accesso del gruppo di Microsoft 365.

   * Conferma che si verificano verifiche di accesso.

   * Rimuove gli utenti aggiunti direttamente all'oggetto SharePoint sottostante.

 Impostare **Abilita i codici di accesso monouso della posta elettronica per i Guest (anteprima) e abilitare l'accesso self-service Guest tramite flussi utente** a **Sì**. Questa impostazione sfrutta i vantaggi dell'integrazione con Azure AD impostazioni di collaborazione esterna.

### <a name="collaboration-restrictions"></a>Restrizioni di collaborazione

Sono disponibili tre opzioni per le restrizioni di collaborazione. I requisiti aziendali stabiliscono la scelta.

* **Consenti l'invio di inviti a qualsiasi dominio** significa che qualsiasi utente può essere invitato a collaborare.

* **Negare gli inviti ai domini specificati** indica che qualsiasi utente esterno a tali domini può essere invitato a collaborare.

* **Consenti gli inviti solo ai domini specificati** significa che non è possibile invitare qualsiasi utente esterno a tali domini specificati. 

## <a name="govern-access-in-teams"></a>Governare l'accesso nei team

I [Team differenziano gli utenti esterni (chiunque all'esterno dell'organizzazione) e gli utenti Guest (quelli con account Guest)](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b). L'impostazione collaborazione viene gestita nel [portale di amministrazione dei team](https://admin.teams.microsoft.com/company-wide-settings/external-communications) in impostazioni a livello di organizzazione. 

> [!NOTE]
> Le impostazioni di collaborazione delle identità esterne in Azure Active Directory controllano le autorizzazioni valide. È possibile aumentare le restrizioni nei team, ma non ridurle rispetto a quanto impostato in Azure AD.

* **Impostazioni di accesso esterno**. Per impostazione predefinita, i team consentono l'accesso esterno, il che significa che l'organizzazione può comunicare con tutti i domini esterni. Se si vuole limitare o consentire domini specifici solo per i team, è possibile farlo qui.

* **Accesso Guest**. Accesso Guest controlla quali utenti guest possono eseguire nei team.

Per ulteriori informazioni sulla gestione dell'accesso esterno nei team, vedere le risorse seguenti.

* [Gestire l'accesso esterno in Microsoft Teams](/microsoftteams/manage-external-access)

* [Modelli di identità Microsoft 365 e Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Modelli di identità e autenticazione per Microsoft Teams](/MicrosoftTeams/identify-models-authentication)

* [Etichette di riservatezza per Microsoft Teams](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Governare l'accesso in SharePoint e OneDrive

Gli amministratori di SharePoint dispongono di molte impostazioni disponibili per la collaborazione. Le impostazioni a livello di organizzazione vengono gestite dall'interfaccia di amministrazione di SharePoint. Le impostazioni possono essere modificate per ogni sito di SharePoint. Si consiglia di usare le impostazioni a livello di organizzazione per i livelli di sicurezza minimi necessari e di aumentare la sicurezza dei siti specifici in base alle esigenze. Ad esempio, per un progetto ad alto rischio, è possibile limitare gli utenti a determinati domini e disabilitare la capacità dei membri di invitare i guest.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integrazione di SharePoint e One-Drive con Azure AD B2B

Come parte della strategia complessiva per la gestione della collaborazione esterna, è consigliabile [abilitare l'anteprima dell'integrazione di SharePoint e OneDrive con Azure ad B2B](/sharepoint/sharepoint-azureb2b-integration-preview) .

Azure AD B2B fornisce l'autenticazione e la gestione degli utenti guest. Con l'integrazione con SharePoint e OneDrive, [Azure ad i codici](../external-identities/one-time-passcode.md) di accesso monouso B2B vengono usati per la condivisione esterna di file, cartelle, voci di elenco, raccolte documenti e siti. Questa funzionalità offre un'esperienza aggiornata dall'esperienza del [destinatario della condivisione esterna protetta](/sharepoint/what-s-new-in-sharing-in-targeted-release)esistente.

> [!NOTE]
> Se si Abilita l'anteprima per Azure AD integrazione B2B, la condivisione di SharePoint e OneDrive è soggetta alle impostazioni di Azure AD relazioni organizzative, ad esempio i **membri possono invitare** e i **Guest possono invitare**.

### <a name="sharing-policies"></a>Criteri di condivisione

La *condivisione esterna* può essere impostata sia per SharePoint che per OneDrive. Le restrizioni OneDrive non possono essere più permissive delle impostazioni di SharePoint.

 ![Screenshot delle impostazioni di condivisione esterna in SharePoint e OneDrive](media/secure-external-access/9-sharepoint-settings.png)

Integrazione di SharePoint con Azure AD B2B modifica il modo in cui i controlli interagiscono con gli account.

* **Chiunque**. Non consigliata

   * Indipendentemente dallo stato di integrazione, l'abilitazione di collegamenti a chiunque indica che non verrà applicato alcun criterio di Azure quando viene usato questo tipo di collegamento. 

   * In uno scenario di collaborazione governata, non abilitare questa funzionalità. 
   > [!NOTE]
   > È possibile che si verifichi uno scenario in cui è necessario abilitare questa impostazione per un sito specifico, nel qual caso è possibile abilitarla e impostare la restrizione più elevata nei singoli siti.

* **Guest nuovi ed esistenti**. Consigliato se l'integrazione è abilitata.

   * **Con Azure ad integrazione B2B** abilitata, i guest nuovi ed esistenti avranno un account Guest Azure ad B2B che può essere gestito con criteri di Azure ad.

   * **Senza Azure ad integrazione B2B** abilitata, i nuovi Guest non avranno un account Azure ad B2B creato e non potranno essere gestiti da Azure ad. Se i guest esistenti hanno un account Azure AD B2B dipende dalla modalità di creazione del Guest.

* **Guest esistenti**. Consigliato se l'integrazione non è abilitata.

   * Con questa funzionalità, gli utenti possono condividere solo con altri utenti già presenti nella directory.

* **Solo gli utenti dell'organizzazione**. Sconsigliato quando è necessario collaborare con gli utenti esterni.

   * Indipendentemente dallo stato di integrazione, gli utenti saranno in grado di condividere solo con gli utenti dell'organizzazione. 

* **Limitare la condivisione esterna per dominio**. Per impostazione predefinita, SharePoint consente l'accesso esterno, il che significa che la condivisione è consentita con tutti i domini esterni. Se si desidera limitare o consentire domini specifici solo per SharePoint, è possibile farlo qui.

* **Consente solo agli utenti di gruppi di sicurezza specifici di condividere l'esterno**.  Questa impostazione limita gli utenti che possono condividere contenuto in SharePoint e OneDrive, mentre l'impostazione in Azure AD si applica a tutte le applicazioni. La limitazione di chi può condividere può essere utile se si desidera richiedere agli utenti di eseguire un training sulla condivisione sicura, quindi al termine aggiungerli a un gruppo di sicurezza di condivisione approvato. Se questa impostazione è selezionata e gli utenti non dispongono di un modo per accedere a un "condivisore approvato", potrebbero invece trovare modi non approvati per condividerli. 

* **Consentire ai guest di condividere gli elementi di cui non sono proprietari**. È consigliabile lasciare disabilitato.

* **Le persone che utilizzano un codice di verifica devono essere riautenticate dopo questo numero di giorni (il valore predefinito è 30)**. Si consiglia di abilitare questa impostazione.

### <a name="access-controls"></a>Controlli di accesso

L'impostazione dei controlli di accesso influirà su tutti gli utenti dell'organizzazione. Poiché l'utente potrebbe non essere in grado di controllare se gli utenti esterni dispongono di dispositivi conformi, questi controlli non verranno affrontati qui. 

* **Disconnessione della sessione inattiva**. Si consiglia di abilitare questo controllo, che consente di avvisare e disconnettersi gli utenti su dispositivi non gestiti dopo un periodo di inattività. È possibile configurare il periodo di inattività e l'avviso. 

* **Percorso di rete**. L'impostazione di questo controllo significa che è possibile consentire l'accesso solo agli indirizzi IP del modulo di proprietà dell'organizzazione. In scenari di collaborazione esterna, impostare questa impostazione solo se tutti i partner esterni accedono alle risorse solo all'interno della rete o tramite la VPN.

### <a name="file-and-folder-links"></a>Collegamenti a file e cartelle

Nell'interfaccia di amministrazione di SharePoint è inoltre possibile impostare la modalità di condivisione dei collegamenti di file e cartelle. È anche possibile configurare queste impostazioni per ogni sito. 

 ![Screenshot delle impostazioni dei collegamenti a file e cartelle](media/secure-external-access/9-file-folder-links.png)

Se è stata abilitata l'integrazione con Azure AD B2B, la condivisione di file e cartelle con quelli esterni all'organizzazione comporterà la creazione di un utente B2B quando si condividono file e cartelle.

Si consiglia di impostare il tipo di collegamento predefinito solo per le **persone all'interno dell'organizzazione** e le autorizzazioni predefinite per la **modifica**. In questo modo si garantisce che gli elementi vengano condivisi in modo ponderato. È quindi possibile personalizzare questa impostazione per le impostazioni predefinite per sito che soddisfano specifiche esigenze di collaborazione.

### <a name="anyone-links"></a>Collegamenti a chiunque

Non è consigliabile abilitare collegamenti a chiunque. In tal caso, è consigliabile impostare una scadenza e provare a limitarle a visualizzare le autorizzazioni. Se si scelgono le autorizzazioni di sola visualizzazione per file o cartelle, gli utenti non saranno in grado di modificare i collegamenti per includere i privilegi di modifica.

Per ulteriori informazioni su come gestire l'accesso esterno a SharePoint, vedere gli argomenti seguenti:

* [Panoramica della condivisione esterna di SharePoint](/sharepoint/external-sharing-overview)

* [Integrazione di SharePoint e OneDrive con Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md) .