---
title: Transizione alla collaborazione governata con Azure Active Directory collaborazione B2B
description: Passa alla collaborazione governata con collaborazione B2B di Azure ad.
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
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648588"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Transizione alla collaborazione governata con Azure Active Directory collaborazione B2B 

Ottenere la collaborazione sotto controllo è fondamentale per proteggere l'accesso esterno alle risorse. Prima di procedere con questo articolo, assicurarsi di disporre di:

* [Determinazione del comportamento della sicurezza](1-secure-access-posture.md)

* [Lo stato corrente è stato individuato](2-secure-access-current-state.md)

* [Creazione di un piano di sicurezza](3-secure-access-plan.md)

* [Comprendere la collaborazione tra gruppi e sicurezza](4-secure-access-groups.md)

Al termine di queste operazioni, si è pronti per passare alla collaborazione controllata. In questo articolo viene illustrata la migrazione di tutta la collaborazione esterna in [Azure Active Directory collaborazione B2B](../external-identities/what-is-b2b.md) (Azure ad B2B). B2B di Azure ad è una funzionalità di [Azure ad identità esterne](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Controllare le collaborazioni dell'organizzazione

È necessario decidere se limitare le organizzazioni con cui gli utenti possono collaborare e chi all'interno dell'organizzazione può avviare la collaborazione. La maggior parte delle organizzazioni accetta l'approccio di consentire alle business unit di decidere con chi collabora e delegare l'approvazione e la supervisione in base alle esigenze. Ad esempio, alcune organizzazioni governative, di formazione e di servizi finanziari non consentono una collaborazione aperta. È possibile usare le funzionalità di Azure AD per definire l'ambito di collaborazione, come illustrato nella parte restante di questa sezione.

### <a name="determine-collaboration-partners"></a>Determinare partner di collaborazione

Prima di tutto, assicurarsi di avere documentato le organizzazioni con cui si sta lavorando e i domini per gli utenti delle organizzazioni. Un partner di collaborazione può avere più domini. Un partner può ad esempio avere più business unit con domini distinti.

Determinare quindi se si vuole abilitare la collaborazione futura con 

* qualsiasi dominio (più inclusivo)

* tutti i domini eccetto quelli negati in modo esplicito 

* solo domini specifici (più restrittivo)

> [!NOTE]
> Più restrittive sono le impostazioni di collaborazione, maggiore è la probabilità che gli utenti si trovino all'esterno del Framework di collaborazione approvato. Si consiglia di abilitare la collaborazione più ampia che le esigenze di sicurezza consentiranno e di esaminare attentamente la collaborazione, anziché essere eccessivamente restrittiva. 

Si noti inoltre che la limitazione a un singolo dominio può impedire inavvertitamente la collaborazione autorizzata con le organizzazioni, che hanno altri domini non correlati per gli utenti. Se, ad esempio, si esegue un'attività aziendale con un'organizzazione Contoso, il punto iniziale di contatto con Contoso potrebbe essere uno dei dipendenti degli Stati Uniti che hanno un messaggio di posta elettronica con un dominio ". com". Tuttavia, se si consente solo il dominio ". com", è possibile omettere inavvertitamente i dipendenti canadesi con dominio ". ca". 

In alcune circostanze è necessario consentire solo partner di collaborazione specifici. Ad esempio, un sistema universitario potrebbe voler solo consentire ai propri docenti di accedere a un tenant di risorse. In alternativa, un conglomerato può voler consentire a sussidiarie specifiche di collaborare tra loro per ottenere la conformità a un framework obbligatorio.

#### <a name="using-allow-and-deny-lists"></a>Utilizzo degli elenchi Consenti e nega

È possibile usare un elenco di indirizzi consentiti o negati per [limitare gli inviti agli utenti B2B](../external-identities/allow-deny-list.md) di organizzazioni specifiche. È possibile utilizzare solo un elenco Consenti o nega, non entrambi.

* Un [elenco Consenti](../external-identities/allow-deny-list.md) limita la collaborazione ai soli domini elencati; tutti gli altri domini sono in effetti nell'elenco di negazione.

* Un [elenco](../external-identities/allow-deny-list.md) di accesso negato consente la collaborazione con qualsiasi dominio non presente nell'elenco di negazione.

> [!IMPORTANT]
> Questi elenchi non si applicano agli utenti che si trovano già nella directory. Non si applicano anche a OneDrive for business e SharePoint consente l'uso di elenchi Deny distinti.

Alcune organizzazioni usano un elenco di domini noti ' Bad Actor ' forniti dal provider di sicurezza gestito per il relativo elenco di accesso negato. Se, ad esempio, l'organizzazione sta operando in modo legittimo con Contoso e utilizzando un dominio com, potrebbe essere presente un'organizzazione non correlata che utilizza il dominio contoso. org e tentando un attacco di phishing per rappresentare i dipendenti di contoso. 

## <a name="control-how-external-users-gain-access"></a>Controllare il modo in cui gli utenti esterni ottengono l'accesso

Esistono molti modi per collaborare con partner esterni usando Azure AD B2B. Per iniziare la collaborazione, è necessario invitare o altrimenti consentire al partner di accedere alle risorse. Gli utenti possono ottenere l'accesso rispondendo a:

* Riscattare un [invito inviato tramite un messaggio di posta elettronica](../external-identities/redemption-experience.md)o [un collegamento diretto per condividere](../external-identities/redemption-experience.md) una risorsa.

* Richiesta di accesso [tramite un'applicazione](../external-identities/self-service-sign-up-overview.md) creata

* Richiesta di accesso tramite il portale di [accesso personale](../governance/entitlement-management-request-access.md)

Quando si Abilita Azure AD B2B, si consente la possibilità di invitare gli utenti guest tramite collegamenti diretti e inviti di posta elettronica per impostazione predefinita. Gli inviti tramite OTP di posta elettronica e un portale self-service sono attualmente in anteprima e devono essere abilitati nelle identità esterne | Impostazioni di collaborazione esterna nel portale di Azure AD.

### <a name="control-who-can-invite-guest-users"></a>Controllare gli utenti che possono invitare gli utenti Guest

Determinare chi può invitare gli utenti guest ad accedere alle risorse.

* L'impostazione più restrittiva consiste nel consentire solo agli amministratori e agli utenti di concedere il [ruolo di invito Guest](../external-identities/delegate-invitations.md) per invitare i guest.

* Se i requisiti di sicurezza lo consentono, è consigliabile consentire a tutti gli utenti con un userType di membro di invitare i guest.

* Determinare se si desidera che gli utenti con userType Guest, ovvero il tipo di account predefinito per Azure AD utenti B2B, siano in grado di invitare altri guest. 

![Screenshot delle impostazioni degli inviti Guest.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Raccogliere informazioni aggiuntive sugli utenti esterni

Se si usa Azure AD gestione dei diritti, è possibile configurare le domande per la risposta degli utenti esterni. Le domande verranno quindi visualizzate agli approvatori per consentire loro di prendere una decisione. È possibile configurare diversi set di domande per ogni [criterio del pacchetto di accesso](../governance/entitlement-management-access-package-approval-policy.md) in modo che i responsabili approvazione possano disporre di informazioni rilevanti per l'accesso che sta approvando. Se, ad esempio, un pacchetto di accesso è destinato all'accesso del fornitore, al richiedente potrebbe essere richiesto il numero di contratto del fornitore. Un pacchetto di accesso diverso destinato ai fornitori può richiedere il proprio paese di origine.

Se si usa un portale self-service, è possibile usare i [connettori API](../external-identities/api-connectors-overview.md) per raccogliere attributi aggiuntivi sugli utenti durante l'iscrizione. È quindi possibile utilizzare potenzialmente tali attributi per assegnare l'accesso. Se, ad esempio, durante il processo di iscrizione si raccoglie il proprio ID fornitore, è possibile utilizzare tale attributo per assegnarli dinamicamente a un gruppo o a un pacchetto di accesso per tale fornitore. È possibile creare attributi personalizzati nel portale di Azure e usarli nei flussi utente di iscrizione self-service. Questi attributi possono anche essere scritti e letti usando l'[API Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Risolvere i problemi di riscatto dell'invito per Azure AD utenti

Sono disponibili tre istanze quando gli utenti Guest invitati da un partner di collaborazione con Azure AD avranno problemi a riscattare un invito.

* Se si usa un elenco Consenti e il dominio dell'utente non è incluso in un elenco Consenti.

* Se il tenant principale del partner di collaborazione presenta restrizioni dei tenant che impediscono la collaborazione con gli utenti esterni.

* Se l'utente non appartiene al tenant Azure AD del partner. Ad esempio, sono presenti utenti di contoso.com che si trovano solo in Active Directory (o in un altro IdP locale). potranno riscattare gli inviti solo tramite il processo OTP di posta elettronica. per altre informazioni, vedere il [flusso di riscatto dell'invito](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Controllare quali utenti esterni possono accedere

La maggior parte delle organizzazioni non è monolitica. Ovvero sono disponibili alcune risorse che possono essere condivise con gli utenti esterni e altre che non si desidera vengano accessibili agli utenti esterni. Pertanto, è necessario controllare l'accesso degli utenti esterni. Valutare l'opportunità di usare i [pacchetti di gestione dei diritti e di accesso per controllare l'accesso](6-secure-access-entitlement-managment.md) a risorse specifiche.

Per impostazione predefinita, gli utenti guest possono visualizzare informazioni e attributi sui membri tenant e altri partner, incluse le appartenenze ai gruppi. Prendere in considerazione se i requisiti di sicurezza richiedono la limitazione dell'accesso utente esterno a queste informazioni.

![Screenshot della configurazione delle impostazioni di collaborazione esterna.](media/secure-external-access/5-external-collaboration-settings.png)

Per gli utenti Guest sono consigliate le restrizioni seguenti.

* **Limitare l'accesso Guest ai gruppi di esplorazione e ad altre proprietà nella directory**

   * Usare le impostazioni di collaborazione esterna per limitare la possibilità di leggere i gruppi di cui non sono membri.

* **Blocca l'accesso alle app solo per i dipendenti**.

   * Creare un criterio di accesso condizionale per bloccare l'accesso alle applicazioni Azure AD-Integrated che sono appropriate solo per gli utenti non Guest. 

* **Blocca l'accesso al portale di Azure. È possibile eseguire rare eccezioni necessarie**. 

   * Creare un criterio di accesso condizionale che includa tutti gli utenti guest ed External, quindi [implementare i criteri per bloccare l'accesso](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Rimuovere gli utenti che non necessitano più di accesso

Valutare l'accesso corrente per poter [esaminare e rimuovere gli utenti che non necessitano più di accesso](../governance/access-reviews-external-users.md). Includere utenti esterni nel tenant come Guest e quelli con account membri. 

Alcune organizzazioni hanno aggiunto utenti esterni, ad esempio fornitori, partner e collaboratori come membri. Questi membri possono avere un attributo specifico o nomi utente che iniziano con, ad esempio

* v-per i fornitori

* p-per i partner

* c-per i terzisti

Valutare gli utenti esterni con account membro per determinare se è ancora necessario l'accesso. In tal caso, eseguire la transizione di questi utenti a Azure AD B2B, come descritto nella sezione successiva.

Potrebbero inoltre essere presenti utenti guest che non sono stati invitati tramite la gestione dei diritti o Azure AD B2B

Per trovare questi utenti, è possibile:

* [Trova utenti guest non invitati tramite la gestione dei diritti](../governance/access-reviews-external-users.md).

   * Viene fornito uno [script di PowerShell di esempio.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Eseguire la transizione di questi utenti a Azure AD utenti B2B, come descritto nella sezione seguente.

## <a name="transition-your-current-external-users-to-b2b"></a>Transizione degli utenti esterni correnti a B2B

Se non si usa Azure AD B2B, probabilmente si avranno utenti non dipendenti nel tenant. Si consiglia di eseguire la transizione di questi account a Azure AD account utente esterni B2B e quindi di modificare il UserType in Guest. In questo modo è possibile sfruttare i molti modi Azure AD e Microsoft 365 consentono di gestire gli utenti esterni in modo diverso. Alcuni di questi modi includono:

* Facile inclusione o esclusione di utenti guest nei criteri di accesso condizionale

* Facile inclusione o esclusione di utenti guest in Access Packages e Access revisioni

* Facile inclusione o esclusione dell'accesso esterno ai team, a SharePoint e ad altre risorse.

Per eseguire la transizione di questi utenti interni mantenendo le relative appartenenze correnti di accesso, UPN e gruppo, vedere [invitare utenti esterni a collaborazione B2B](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Rimuovere le autorizzazioni per i metodi di collaborazione indesiderati

Per completare la transizione alla collaborazione governata, è necessario rimuovere le autorizzazioni per i metodi di collaborazione indesiderati. La rimozione delle autorizzazioni è basata sul livello di controllo che si desidera esercitare sulla collaborazione e sul comportamento di sicurezza. Per informazioni sul controllo IT e sull'utente finale, vedere [determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md).

Di seguito sono riportati i veicoli di collaborazione che è possibile valutare.

### <a name="direct-invitation-through-microsoft-teams"></a>Invito diretto tramite Microsoft Teams

Per impostazione predefinita, i team consentono l'accesso esterno, il che significa che l'organizzazione può comunicare con tutti i domini esterni. Se si vuole limitare o consentire domini specifici solo per i team, è possibile farlo nel portale di [amministrazione dei team](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Condivisione diretta tramite SharePoint e OneDrive

La condivisione diretta tramite SharePoint e OneDrive può aggiungere utenti al di fuori del processo di gestione dei diritti. Per informazioni dettagliate su queste configurazioni [, vedere gestire l'accesso con Microsoft teams, SharePoint e OneDrive for business](9-secure-access-teams-sharepoint.md) . è anche possibile [bloccare l'uso di OneDrive personali dell'utente](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) , se lo si desidera.

### <a name="sending-documents-through-email"></a>Invio di documenti tramite posta elettronica

Gli utenti invieranno i documenti tramite posta elettronica a utenti esterni. Considerare come si vuole limitare e crittografare l'accesso a questi documenti usando le etichette di riservatezza. Per altre informazioni, vedere gestire l'accesso con le etichette di riservatezza.

### <a name="unsanctioned-collaboration-tools"></a>Strumenti di collaborazione non autorizzati

Il panorama degli strumenti di collaborazione è vasto. È probabile che gli utenti usino molti al di fuori delle proprie mansioni ufficiali, incluse piattaforme come Google Docs, DropBox, Slack o zoom. È possibile bloccare l'uso di questi strumenti da una rete aziendale a livello di firewall e con gestione di applicazioni mobili per i dispositivi gestiti dall'organizzazione. Tuttavia, questa operazione bloccherà anche le istanze approvate di queste piattaforme e non bloccherà l'accesso da dispositivi non gestiti. Blocca le piattaforme che non vuoi usare se necessario e crea criteri di business per evitare un utilizzo non autorizzato per le piattaforme che devi usare. 

Per ulteriori informazioni sulla gestione di applicazioni non approvate, vedere:

* [Governance delle app connesse](/cloud-app-security/governance-actions)

* [Approvazione e annullamento dell'approvazione di un'applicazione.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare il comportamento di sicurezza per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md)

5. [Passare a Azure ad B2B](5-secure-access-b2b.md) (si trova qui).

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)