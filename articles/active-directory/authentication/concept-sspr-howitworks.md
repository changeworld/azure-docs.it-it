---
title: Approfondimento sulla reimpostazione della password self-service - Azure Active Directory
description: Funzionamento della reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765918"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Funzionamento: reimpostazione self-service della password di Azure AD

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

> [!IMPORTANT]
> Questo articolo concettuale illustra a un amministratore il funzionamento della reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service ed è necessario accedere nuovamente all'account, passare a [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

## <a name="how-does-the-password-reset-process-work"></a>Come funziona il processo di reimpostazione della password?

Un utente può reimpostare o modificare la password usando il portale della reimpostazione della password [self-service.](https://aka.ms/sspr) Devono prima aver registrato i metodi di autenticazione desiderati. Quando un utente accede al portale della password self-service, la piattaforma Azure considera i fattori seguenti:

* come localizzare la pagina
* la validità dell'account
* l'organizzazione a cui l'utente appartiene
* Dove viene gestita la password dell'utente?
* se l'utente ha una licenza per usare la funzionalità

Quando un utente seleziona il collegamento Non è possibile accedere **all'account da un'applicazione** o da una pagina o passa direttamente a , la lingua usata nel portale SSPR si basa sulle [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) opzioni seguenti:

* Per impostazione predefinita, le impostazioni locali del browser vengono usate per visualizzare la SSPR nella lingua appropriata. L'esperienza di reimpostazione della password è localizzata nelle stesse [lingue Microsoft 365 supporta](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Se si vuole creare un collegamento alla reimpostazione della password di reimpostazione della password in una lingua localizzata specifica, aggiungere alla fine dell'URL di reimpostazione della password insieme `?mkt=` alle impostazioni locali necessarie.
    * Ad esempio, per specificare le impostazioni *locali es-us in spagnolo,* usare `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Quando il portale SSPR viene visualizzato nella lingua richiesta, all'utente viene richiesto di immettere un ID utente e passare un captcha. Azure AD ora verifica che l'utente sia in grado di usare la password SSPR eseguendo i controlli seguenti:

* Verifica che la funzionalità di SSPR sia abilitata per l'utente e che gli sia stata assegnata Azure AD licenza.
  * Se l'utente non è abilitato per la reimpostazione della password utente o non ha una licenza assegnata, l'utente deve contattare l'amministratore per reimpostare la password.
* Verificare che per l'account dell'utente siano stati definiti i metodi di autenticazione corretti, in conformità ai criteri dell'amministratore.
  * Se il criterio richiede un solo metodo, verificare che l'utente abbia i dati appropriati definiti per almeno uno dei metodi di autenticazione abilitati dai criteri dell'amministratore.
    * Se i metodi di autenticazione non sono configurati, è consigliabile contattare l'amministratore per reimpostare la password.
  * Se i criteri richiedono due metodi, verificare che l'utente abbia i dati appropriati definiti per almeno due dei metodi di autenticazione abilitati dai criteri di amministratore.
    * Se i metodi di autenticazione non sono configurati, è consigliabile contattare l'amministratore per reimpostare la password.
  * Se un ruolo di amministratore di Azure viene assegnato all'utente, vengono applicati i criteri di password complessa con due attività di controllo. Per altre informazioni, vedere [Differenze dei criteri di reimpostazione degli amministratori](concept-sspr-policy.md#administrator-reset-policy-differences).
* Verifica se la password dell'utente è gestita in locale, ad esempio se il tenant Azure AD usa l'autenticazione federata, pass-through o la sincronizzazione dell'hash delle password:
  * Se il writeback SSPR è configurato e la password dell'utente è gestita in locale, l'utente può procedere all'autenticazione e alla reimpostazione della password.
  * Se il writeback SSPR non viene distribuito e la password dell'utente viene gestita in locale, all'utente viene chiesto di contattare l'amministratore per reimpostare la password.

Se tutti i controlli precedenti vengono completati correttamente, l'utente viene guidato nel processo di reimpostazione o modifica della password.

> [!NOTE]
> La reimpostazione della password della password può inviare notifiche tramite posta elettronica agli utenti come parte del processo di reimpostazione della password. Questi messaggi di posta elettronica vengono inviati tramite il servizio di inoltro SMTP, che opera in modalità attivo-attivo in diverse aree.
>
> I servizi di inoltro SMTP ricevono ed elaborano il corpo della posta elettronica, ma non lo archiviano. Il corpo del messaggio di posta elettronica SSPR che potrebbe contenere le informazioni fornite dal cliente non viene archiviato nei log del servizio di inoltro SMTP. I log contengono solo metadati del protocollo.

Per iniziare a usare la SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare la reimpostazione della password self-service](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Richiedere agli utenti di registrarsi all'accesso

È possibile abilitare l'opzione per richiedere a un utente di completare la registrazione SSPR se usa l'autenticazione moderna o il Web browser per accedere a qualsiasi applicazione Azure AD. Questo flusso di lavoro include le applicazioni seguenti:

* Microsoft 365
* Portale di Azure
* Pannello di accesso
* Applicazioni federate
* Applicazioni personalizzate che usano Azure AD

Quando non è necessaria la registrazione, gli utenti non vengono richiesti durante l'accesso, ma possono eseguire la registrazione manualmente. Gli utenti possono visitare o selezionare il collegamento Registra per la [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) **reimpostazione della password**  nella scheda Profilo del Pannello di accesso.

![Opzioni di registrazione per la richiesta SSPR nel portale di Azure][Registration]

> [!NOTE]
> Gli utenti possono chiudere il portale di registrazione della SSPR selezionando **Annulla** o chiudendo la finestra. Tuttavia, viene richiesto di eseguire la registrazione ogni volta che effettuano l'accesso fino a quando non completano la registrazione.
>
> Questo interrupt per la registrazione per la SSPR non interrompe la connessione dell'utente se è già stato eseguito l'accesso.

## <a name="reconfirm-authentication-information"></a>Riconfermare le informazioni di autenticazione

Per assicurarsi che i metodi di autenticazione siano corretti quando è necessario reimpostare o modificare la password, è possibile richiedere agli utenti di confermare le informazioni registrate dopo un determinato periodo di tempo. Questa opzione è disponibile solo se si abilita **l'opzione Richiedi agli utenti di registrarsi all'accesso.**

Valori validi per richiedere all'utente di confermare che i metodi registrati sono *da 0* a *730* giorni. L'impostazione di questo *valore su 0* indica che agli utenti non viene mai richiesto di confermare le informazioni di autenticazione. Quando si usa l'esperienza di registrazione combinata, agli utenti verrà richiesto di confermare la propria identità prima di riconfermare le informazioni.

## <a name="authentication-methods"></a>Metodi di autenticazione

Quando un utente è abilitato per la SSPR, deve registrare almeno un metodo di autenticazione. È consigliabile scegliere due o più metodi di autenticazione in modo che gli utenti siano più flessibili nel caso in cui non siano in grado di accedere a un metodo quando necessario. Per altre informazioni, vedere [Che cosa sono i metodi di autenticazione?](concept-authentication-methods.md).

Per la password di SSPR sono disponibili i metodi di autenticazione seguenti:

* Notifica dell'app per dispositivi mobili
* Codice app per dispositivi mobili
* Posta elettronica
* Cellulare
* Telefono ufficio
* Domande di sicurezza

Gli utenti possono reimpostare la password solo se hanno registrato un metodo di autenticazione abilitato dall'amministratore.

> [!WARNING]
> Gli account assegnati ai *ruoli di amministratore* di Azure sono necessari per usare i metodi definiti nella sezione Differenze dei criteri di [reimpostazione dell'amministratore.](concept-sspr-policy.md#administrator-reset-policy-differences)

![Selezione dei metodi di autenticazione nel portale di Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Numero di metodi di autenticazione necessari

È possibile configurare il numero di metodi di autenticazione disponibili che un utente deve fornire per reimpostare o sbloccare la password. Questo valore può essere impostato su *uno o* *due*.

Gli utenti possono e devono registrare più metodi di autenticazione. Anche in questo caso, è consigliabile che gli utenti registrino due o più metodi di autenticazione in modo da avere maggiore flessibilità nel caso in cui non siano in grado di accedere a un metodo quando ne hanno bisogno.

Se un utente non ha il numero minimo di metodi necessari registrati quando tenta di usare la reimpostazione della password di reimpostazione della password, viene visualizzata una pagina di errore che indica a un amministratore di reimpostare la password. Fare attenzione se si aumenta il numero di metodi necessari da uno a due se gli utenti esistenti sono registrati per la SSPR e non sono quindi in grado di usare la funzionalità. Per altre informazioni, vedere la sezione seguente in [Modificare i metodi di autenticazione](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>App per dispositivi mobili e SSPR

Quando si usa un'app per dispositivi mobili come metodo per la reimpostazione della password, come l'app Microsoft Authenticator, si applicano le considerazioni seguenti:

* Quando gli amministratori richiedono l'uso di un solo metodo per la reimpostazione di una password, il codice di verifica è l'unica opzione disponibile.
* Quando gli amministratori richiedono l'uso di due metodi per reimpostare una password, gli utenti possono usare il codice di verifica **OR** di notifica oltre a qualsiasi altro metodo abilitato.

| Numero di metodi da reimpostare | Uno | Due |
| :---: | :---: | :---: |
| Funzionalità disponibili delle app per dispositivi mobili | Codice | Codice o notifica |

Gli utenti non hanno la possibilità di registrare l'app per dispositivi mobili durante la registrazione per la reimpostazione della password self-service da [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Gli utenti possono registrare l'app per dispositivi mobili [https://aka.ms/mfasetup](https://aka.ms/mfasetup) in o nella registrazione combinata delle informazioni di sicurezza all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> L'app Authenticator non può essere selezionata come unico metodo di autenticazione quando è necessario un solo metodo. Analogamente, l'app Authenticator e un solo metodo aggiuntivo non possono essere selezionati quando sono necessarie due metodi.
>
> Quando si configurano criteri SSPR che includono l'app Authenticator come metodo, è necessario selezionare almeno un metodo aggiuntivo quando è necessario un metodo e quando si configurano due metodi è necessario selezionare almeno due metodi aggiuntivi.
>
> Questo requisito è dovuto al fatto che l'esperienza di registrazione SSPR corrente non include l'opzione per registrare l'app di autenticazione. L'opzione per registrare l'app di autenticazione è inclusa nella nuova [esperienza di registrazione combinata](./concept-registration-mfa-sspr-combined.md).
>
> Consentire i criteri che usano solo l'app Authenticator (quando è necessario un metodo) o l'app Authenticator e un solo metodo aggiuntivo (quando sono necessari due metodi), potrebbe causare il blocco della registrazione degli utenti per la password SSPR fino a quando non sono configurati per l'uso della nuova esperienza di registrazione combinata.

### <a name="change-authentication-methods"></a>Modifica dei metodi di autenticazione

Cosa succede se si inizia con un criterio che ha solo un metodo di autenticazione registrato necessario per la reimpostazione o lo sblocco e si passa a due?

| Numero di metodi registrati | Numero di metodi necessari | Risultato |
| :---: | :---: | :---: |
| 1 o più | 1 | **Possibilità** di reimpostare o sbloccare |
| 1 | 2 | **Impossibilità** di reimpostare o sbloccare |
| 2 o più | 2 | **Possibilità** di reimpostare o sbloccare |

Anche la modifica dei metodi di autenticazione disponibili può causare problemi per gli utenti. Se si modificano i tipi di metodi di autenticazione che un utente può usare, si potrebbe impedire inavvertitamente agli utenti l'uso della reimpostazione password self-service se questi non dispongono della quantità minima di dati.

Si consideri lo scenario di esempio seguente:

1. Il criterio originale è configurato con due metodi di autenticazione necessari. Vengono usati solo il numero di telefono ufficio e le domande di sicurezza.
1. L'amministratore modifica i criteri in modo da non usare più domande di sicurezza, ma da consentire l'uso del telefono cellulare e di un indirizzo di posta elettronica alternativo.
1. Gli utenti senza il telefono cellulare o i campi di posta elettronica alternativi popolati ora non possono reimpostare le password.

## <a name="notifications"></a>Notifiche

Per migliorare la consapevolezza degli eventi relativi alle password, la reimpostazione della password utente consente di configurare le notifiche per gli utenti e gli amministratori delle identità.

### <a name="notify-users-on-password-resets"></a>Inviare notifiche agli utenti al momento della reimpostazione della password

Se questa opzione è impostata su **Sì,** gli utenti che reimpostano la password ricevono un messaggio di posta elettronica che informa che la password è stata modificata. Il messaggio di posta elettronica viene inviato tramite il portale SSPR agli indirizzi di posta elettronica primari e alternativi archiviati in Azure AD. La notifica dell'evento di reimpostazione non viene inviata ad altre persone.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

Se questa opzione è impostata su **Sì,** tutti gli altri amministratori di Azure ricevono un messaggio di posta elettronica all'indirizzo di posta elettronica principale archiviato in Azure AD. Questo messaggio notifica la modifica della password tramite il servizio di reimpostazione della password self-service da parte di un altro amministratore.

Si consideri lo scenario di esempio seguente:

* nell'ambiente sono presenti quattro amministratori.
* *L'amministratore A* reimposta la password tramite SSPR.
* Gli *amministratori B,* *C* e D ricevono *un* messaggio di posta elettronica per avvisarli della reimpostazione della password.

## <a name="on-premises-integration"></a>Integrazione locale

Se si dispone di un ambiente ibrido, è possibile configurare Azure AD Connect per scrivere gli eventi di modifica della password da Azure AD in una directory locale.

![La convalida del writeback delle password è abilitata e funzionante][Writeback]

Azure AD verifica la connettività ibrida corrente e fornisce uno dei messaggi seguenti nel portale di Azure:

* Il client writeback locale è in esecuzione.
* Azure AD è online e connesso al client di writeback locale. Tuttavia sembra che la versione installata di Azure AD Connect non sia aggiornata. Prendere in considerazione l'[aggiornamento di Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per assicurarsi di disporre delle funzionalità di connettività più recenti e di importanti correzioni di bug.
* Sfortunatamente, non è possibile controllare lo stato del client writeback locale perché la versione installata di Azure AD Connect non è aggiornata. [Aggiornare Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per essere in grado di controllare lo stato della connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. [Risoluzione dei problemi di Azure AD Connect](./troubleshoot-sspr-writeback.md) per ripristinare la connessione.
* Purtroppo non è possibile eseguire la connessione al client di writeback locale perché il writeback delle password non è stato configurato correttamente. [Configurare il writeback delle password](./tutorial-enable-sspr-writeback.md) per ripristinare la connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. Ciò può essere dovuto a errori temporanei nel sistema. Se il problema persiste, vedere [Risoluzione dei problemi di Azure AD Connect](./troubleshoot-sspr-writeback.md) per ripristinare la connessione.

Per iniziare a usare il writeback della reimpostazione della password self-service, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare il writeback della reimpostazione della password self-service](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Writeback delle password nella directory locale

È possibile abilitare il writeback delle password usando il portale di Azure. È anche possibile disabilitare temporaneamente il writeback delle password senza dover riconfigurare Azure AD Connect.

* Se l'opzione è impostata **su Sì,** il writeback è abilitato. Gli utenti federati, pass-through o sincronizzati con hash delle password possono reimpostare le password.
* Se l'opzione è impostata su **No,** il writeback è disabilitato. Gli utenti federati, pass-through o sincronizzati con hash delle password non sono in grado di reimpostare le password.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Allow users to unlock accounts without resetting their password

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Per offrire flessibilità, è possibile scegliere di consentire agli utenti di sbloccare gli account locali senza dover reimpostare la password. Usare questa impostazione per separare queste due operazioni.

* Se impostato su **Sì,** agli utenti viene data la possibilità di reimpostare la password e sbloccare l'account o di sbloccare l'account senza dover reimpostare la password.
* Se impostato su **No,** gli utenti possono solo eseguire un'operazione combinata di reimpostazione della password e sblocco dell'account.

### <a name="on-premises-active-directory-password-filters"></a>Filtri della password di Active Directory locali

SSPR esegue l'equivalente di una reimpostazione della password avviata dall'amministratore in Active Directory. Se si usa un filtro password di terze parti per applicare regole password personalizzate ed è necessario che questo filtro password sia selezionato durante la reimpostazione della password self-service di Azure AD, assicurarsi che la soluzione di filtro password di terze parti sia configurata per l'applicazione nello scenario di reimpostazione della password amministratore. [Azure AD la protezione con password per Active Directory Domain Services](concept-password-ban-bad-on-premises.md) è supportata per impostazione predefinita.

## <a name="password-reset-for-b2b-users"></a>Reimpostazione della password per utenti B2B

La modifica e la reimpostazione della password sono completamente supportate in tutte le configurazioni B2B. La reimpostazione della password di utenti B2B è supportata nei tre casi seguenti:

* **Utenti di** un'organizzazione partner con un tenant Azure AD esistente: se l'organizzazione con cui si collabora ha un tenant Azure AD esistente, vengono rispettati i criteri di reimpostazione della password abilitati nel tenant. Per garantire il corretto funzionamento della reimpostazione della password, l'organizzazione partner deve assicurarsi che sia abilitata la reimpostazione delle password self-service di Azure AD. Non sono addebitati costi aggiuntivi per Microsoft 365 clienti.
*  Utenti che effettuano l'iscrizione tramite iscrizione self-service: se l'organizzazione con cui si collabora ha usato la funzionalità di iscrizione [self-service](../enterprise-users/directory-self-service-signup.md) per accedere a un tenant, microsoft ha lasciato che reimposta la password con il messaggio di posta elettronica registrato.
* **Utenti B2B:** tutti i nuovi utenti B2B creati usando le nuove funzionalità [B2B di Azure AD](../external-identities/what-is-b2b.md) possono anche reimpostare le password con il messaggio di posta elettronica registrato durante il processo di invito.

Per testare questo scenario, passare a https://passwordreset.microsoftonline.com con uno di questi utenti partner. Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funziona come previsto.

> [!NOTE]
> Gli account Microsoft a cui è stato concesso l'accesso guest al tenant di Azure AD, ad esempio quelli di Hotmail.com, Outlook.com o altri indirizzi di posta elettronica personali, non possono usare la Azure AD SSPR. Tali account devono reimpostare le password usando le informazioni riportate nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare la reimpostazione della password self-service](tutorial-enable-sspr.md)

Gli articoli seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Configurare le opzioni di registrazione SSPR nel portale di Azure"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integrazione locale per la SSPR nel portale di Azure"
