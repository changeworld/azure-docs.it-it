---
title: Configurare Azure AD Multi-Factor Authentication - Azure Active Directory
description: Informazioni su come configurare le impostazioni Azure AD Multi-Factor Authentication nella portale di Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829657"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Configurare Azure AD Multi-Factor Authentication

Per personalizzare l'esperienza dell'utente finale Azure AD Multi-Factor Authentication, è possibile configurare opzioni per impostazioni come le soglie di blocco dell'account o gli avvisi e le notifiche di frode. Alcune impostazioni sono direttamente nel portale di Azure per Azure Active Directory (Azure AD) e altre in un portale Azure AD Multi-Factor Authentication.

Le impostazioni Azure AD Multi-Factor Authentication seguenti sono disponibili nel portale di Azure:

| Funzionalità | Descrizione |
| ------- | ----------- |
| [Blocco dell'account](#account-lockout) | Bloccare temporaneamente gli account dall'Azure AD Multi-Factor Authentication se sono presenti troppi tentativi di autenticazione negati in una riga. Questa funzionalità si applica solo agli utenti che effettuano l'autenticazione tramite PIN (server MFA). |
| [Blocca/Sblocca utenti](#block-and-unblock-users) | Impedire a utenti specifici di ricevere richieste Azure AD Multi-Factor Authentication. Eventuali tentativi di autenticazione per gli utenti bloccati vengono negati automaticamente. Gli utenti rimangono bloccati per 90 giorni dal momento in cui vengono bloccati o sbloccati manualmente. |
| [Avviso di illecito](#fraud-alert) | Configurare le impostazioni che consentono agli utenti di segnalare richieste di verifica fraudolente. |
| [Notifications](#notifications) | Abilita le notifiche degli eventi dal server MFA. |
| [Token OATH](concept-authentication-oath-tokens.md) | Usato in ambienti basati su cloud Azure AD MFA per gestire i token OATH per gli utenti. |
| [Impostazioni telefonata](#phone-call-settings) | Permette di configurare le impostazioni correlate a chiamate telefoniche e messaggi di saluto per gli ambienti cloud e locali. |
| Providers | Mostra tutti i provider di autenticazione esistenti che possono essere stati associati all'account. Non è stato possibile creare nuovi provider di autenticazione fino al 1° settembre 2018 |

![Portale di Azure - Impostazioni di Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Blocco dell'account

Per evitare ripetuti tentativi di autenticazione a più fattori come parte di un attacco, le impostazioni di blocco dell'account consentono di specificare il numero di tentativi non riusciti consentiti prima che l'account venga bloccato per un periodo di tempo. Le impostazioni di blocco dell'account vengono applicate solo quando viene immesso un codice pin per la richiesta MFA.

Sono disponibili le impostazioni seguenti:

* Numero di smentite dell'autenticazione a più fattori per attivare il blocco dell'account
* Minuti prima della reimpostazione del contatore di blocco dell'account
* Minuti fino a quando l'account non viene sbloccato automaticamente

Per configurare le impostazioni di blocco dell'account, completare le impostazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Passare **a** Azure Active Directory  >  **blocco**  >  **dell'account MFA**  >  **di sicurezza**.
1. Immettere i valori di richiesta per l'ambiente e quindi selezionare **Salva**.

    ![Screenshot delle impostazioni di blocco dell'account nel portale di Azure](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Bloccare e sbloccare utenti

Se il dispositivo di un utente è stato smarrito o rubato, è possibile Azure AD tentativi di Autenticazione a più fattori per l'account associato. Tutti Azure AD tentativi di Multi-Factor Authentication per gli utenti bloccati vengono negati automaticamente. Gli utenti restano bloccati per 90 giorni dal momento in cui vengono bloccati. È stato pubblicato un video su [come bloccare e sbloccare gli utenti nel tenant](https://www.youtube.com/watch?v=WdeE1On4S1o) per illustrare come eseguire questa operazione.

### <a name="block-a-user"></a>Bloccare un utente

Per bloccare un utente, seguire questa procedura:

1. Passare ad **Azure Active Directory** > **Sicurezza** > **MFA** > **Blocca/Sblocca utenti**.
1. Selezionare **Aggiungi** per bloccare un utente.
1. Immettere il nome utente per l'utente bloccato come `username@domain.com` , quindi specificare un commento nel *campo* Motivo.
1. Quando si è pronti, **selezionare OK** per bloccare l'utente.

### <a name="unblock-a-user"></a>Sbloccare un utente

Per sbloccare un utente, seguire questa procedura:

1. Passare ad **Azure Active Directory** > **Sicurezza** > **MFA** > **Blocca/Sblocca utenti**.
1. Nella colonna *Azione* accanto all'utente desiderato selezionare **Sblocca**.
1. Immettere un commento nel campo *Motivo dello sblocco*.
1. Al termine, selezionare **OK per** sbloccare l'utente.

## <a name="fraud-alert"></a>Avviso di illecito

La funzionalità di avviso di illecito consente agli utenti di segnalare tentativi fraudolenti di accesso alle risorse. Quando viene ricevuta una richiesta di autenticazione a più fattori sconosciuta e sospetta, gli utenti possono segnalare il tentativo di frode usando l'app Microsoft Authenticator o tramite il telefono.

Sono disponibili le opzioni di configurazione degli avvisi di frode seguenti:

* Blocca **automaticamente** gli utenti che segnalano frodi: se un utente segnala un'frode, i tentativi di autenticazione Azure AD MFA per l'account utente vengono bloccati per 90 giorni o fino a quando un amministratore non sblocca il proprio account. Un amministratore può verificare gli accessi usando il report sugli accessi ed eseguire le azioni necessarie per prevenire illeciti nel futuro. Un amministratore può quindi [sbloccare](#unblock-a-user) l'account dell'utente.
* **Codice per segnalare un errore durante** il messaggio di saluto iniziale: quando gli utenti ricevono una telefonata per eseguire l'autenticazione a più fattori, in genere premono per confermare **#** l'accesso. Per segnalare un illecito, l'utente immette un codice prima di premere **#** . Il codice predefinito è **0**, ma è possibile personalizzarlo.

   > [!NOTE]
   > I messaggi vocali predefiniti di Microsoft invitano gli utenti a premere **0#** per inviare un avviso di illecito. Se si vuole usare un codice diverso da **0**, registrare e caricare messaggi vocali personalizzati con istruzioni adeguate per l'utente.

Per abilitare e configurare gli avvisi di frode, seguire questa procedura:

1. Passare ad **Azure Active Directory** > **Sicurezza** > **MFA** > **Avviso di illecito**.
1. Impostare l'impostazione *Consenti agli utenti di inviare avvisi di illeciti* su **Attivo**.
1. Configurare *l'impostazione Blocca automaticamente gli utenti che segnalano frodi* o *Codice per segnalare* un'frode durante il messaggio di saluto iniziale in base alle esigenze.
1. Al termine, selezionare **Salva**.

### <a name="view-fraud-reports"></a>Visualizzare le segnalazioni di illeciti

Selezionare **Azure Active Directory** > **Accessi** > **Dettagli di autenticazione**. La segnalazione di illeciti è ora inclusa nel report di Accessi di Azure AD standard e verrà visualizzata in **"Dettagli del risultato"** come codice illecito immesso con accesso a Multi-Factor Authentication negato.
 
## <a name="notifications"></a>Notifiche

Le notifiche tramite posta elettronica possono essere configurate quando gli utenti segnalano avvisi di frode. Queste notifiche vengono in genere inviate agli amministratori delle identità, in quanto è probabile che le credenziali dell'account dell'utente siano compromesse. L'esempio seguente mostra l'aspetto di un messaggio di posta elettronica di notifica di avviso di frode:

![Messaggio di posta elettronica di notifica di avviso di frode di esempio](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Per configurare le notifiche di avviso di frode, completare le impostazioni seguenti:

1. Passare a **Azure Active Directory**  >  **notifiche di Security**  >  **Multi-Factor**  >  **Authentication.**
1. Immettere l'indirizzo di posta elettronica da aggiungere nella casella successiva.
1. Per rimuovere un indirizzo di posta elettronica esistente, selezionare **l'opzione ...** accanto all'indirizzo di posta elettronica desiderato, quindi selezionare **Elimina**.
1. Al termine, selezionare **Salva**.

## <a name="oath-tokens"></a>Token OATH

Azure AD supporta l'uso di token SHA-1 OATH-TOTP che aggiornano i codici ogni 30 o 60 secondi. I clienti possono acquistare questi token dal fornitore preferito.

I token hardware OATH TOTP sono in genere dotati di una chiave privata o di un seme pre-programmato nel token. Queste chiavi devono essere inserite in Azure AD come descritto nella procedura seguente. Le chiavi private sono limitate a 128 caratteri e ciò potrebbe non essere compatibile con tutti i token. La chiave privata può contenere solo i caratteri *a-z* o *A-Z* e i numeri *1-7* e deve essere codificata in *Base32*.

I token hardware OATH TOTP programmabili di cui è possibile generare un nuovo seme possono anche essere configurati con Azure AD nel flusso di configurazione del token software.

I token hardware OATH sono supportati come parte di un'anteprima pubblica. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Pannello di caricamento dei token OATH nei token OATH di MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Dopo aver acquisito i token, è necessario caricarli in un formato di file con valori delimitati da virgole (CSV) includendo l'UPN, il numero di serie, la chiave privata, l'intervallo di tempo, il produttore e il modello come illustrato nell'esempio seguente:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Assicurarsi di includere la riga di intestazione nel file CSV.

Dopo aver formattato correttamente il file CSV, un amministratore può accedere al portale di Azure e passare ad **Azure Active Directory > Sicurezza > MFA > Token OATH** e caricare il file CSV risultante.

A seconda delle dimensioni del file CSV, potrebbero essere richiesti alcuni minuti per l'elaborazione. Per ottenere lo stato corrente, selezionare il pulsante **Aggiorna**. Se sono presenti errori nel file, è possibile scaricare un file CSV che elenca gli eventuali errori da risolvere. I nomi dei campi nel file CSV scaricato sono diversi da quelli della versione caricata.

Dopo aver risolto eventuali errori, l'amministratore può quindi attivare ogni chiave selezionando **Attiva** per il token da attivare e immettendo l'OTP visualizzato nel token.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

## <a name="phone-call-settings"></a>Impostazioni telefonata

Se gli utenti ricevono chiamate telefoniche per richieste MFA, è possibile configurarne l'esperienza, ad esempio l'ID chiamante o il saluto vocale che ascoltano.

Nel Stati Uniti, se non è stato configurato l'ID chiamante MFA, le chiamate vocali di Microsoft provengono dai numeri seguenti. Se si usano filtri per la posta indesiderata, assicurarsi di escludere questi numeri:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Quando Azure AD chiamate Multi-Factor Authentication vengono effettuate tramite la rete telefonica pubblica, talvolta le chiamate vengono instradati tramite un operatore che non supporta l'ID chiamante. Per questo, l'ID chiamante non è garantito, anche se Azure AD Multi-Factor Authentication lo invia sempre. Questo vale sia per le telefonate che per gli SMS forniti da Azure AD Multi-Factor Authentication. Se è necessario verificare che un SMS sia Azure AD Multi-Factor Authentication, vedere Quali codici brevi SMS vengono usati per [l'invio di messaggi?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Per configurare il proprio numero ID chiamante, seguire questa procedura:

1. Passare a **Azure Active Directory** > **Sicurezza** > **MFA** > **Impostazioni telefonata**.
1. Impostare il **numero ID chiamante MFA** sul numero che gli utenti devono visualizzare sul telefono. Sono consentiti solo numeri degli Stati Uniti.
1. Al termine, selezionare **Salva**.

### <a name="custom-voice-messages"></a>Messaggi vocali personalizzati

È possibile usare registrazioni o saluti personalizzati per Azure AD Multi-Factor Authentication con la funzionalità di messaggi vocali personalizzati. Questi messaggi possono essere usati in aggiunta o per sostituire le registrazioni Microsoft predefinite.

Prima di iniziare, tenere presente le seguenti restrizioni:

* I formati di file supportati *sono wav* e *mp3.*
* La dimensione massima dei file è 1 MB.
* I messaggi di autenticazione devono avere una durata inferiore a 20 secondi. Se i messaggi durano più di 20 secondi, è possibile che la verifica abbia esito negativo. L'utente potrebbe non rispondere prima della fine del messaggio e la verifica potrebbe scadere.

### <a name="custom-message-language-behavior"></a>Comportamento per la lingua del messaggio personalizzato

Quando un messaggio vocale personalizzato viene riprodotto all'utente, la lingua del messaggio dipende dai fattori seguenti:

* Lingua dell'utente corrente.
  * Lingua rilevata dal browser dell'utente.
  * Altri scenari di autenticazione possono presentare comportamenti diversi.
* Lingua degli eventuali messaggi personalizzati disponibili.
  * Questa lingua viene scelta dall'amministratore, quando viene aggiunto un messaggio personalizzato.

Ad esempio, se è presente un solo messaggio personalizzato, con la lingua tedesca:

* Un utente che esegue l'autenticazione in lingua tedesca ascolterà il messaggio personalizzato in tedesco.
* Il messaggio inglese standard verrà riprodotto per un utente che esegue l'autenticazione in lingua inglese.

### <a name="custom-voice-message-defaults"></a>Valori predefiniti dei messaggi vocali personalizzati

Gli script di esempio seguenti possono essere usati per creare messaggi personalizzati. Queste frasi sono le impostazioni predefinite se non si configurano messaggi personalizzati:

| Nome messaggio | Script |
| --- | --- |
| Autenticazione riuscita | L'accesso è stato verificato correttamente. Arrivederci. |
| Richiesta interno | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per continuare. |
| Conferma illecito | È stato inviato un avviso di illecito. Per sbloccare l'account, contattare l'help desk IT della società. |
| Messaggio introduttivo illecito (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. Se non si è avviato personalmente la procedura di verifica, è possibile che qualcuno stia tentando di accedere all'account. Premere 0 seguito da cancelletto per inviare un avviso di illecito, in modo da informare il team IT della società e bloccare ulteriori tentativi di verifica. |
| Segnalato illecito - È stato inviato un avviso di illecito. | Per sbloccare l'account, contattare l'help desk IT della società. |
| Activation | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. |
| Autenticazione negata - Nuovo tentativo | Verifica non consentita. |
| Nuovo tentativo (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. |
| Messaggio introduttivo (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. |
| Messaggio introduttivo (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Immettere il PIN seguito da cancelletto per completare la verifica. |
| Messaggio introduttivo illecito (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft.  Immettere il PIN seguito da cancelletto per completare la verifica. Se non si è avviato personalmente la procedura di verifica, è possibile che qualcuno stia tentando di accedere all'account. Premere 0 seguito da cancelletto per inviare un avviso di illecito, in modo da informare il team IT della società e bloccare ulteriori tentativi di verifica. |
| Nuovo tentativo (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Immettere il PIN seguito da cancelletto per completare la verifica. |
| Richiesta interno dopo i numeri | Se si risponde già a questo interno, premere cancelletto per continuare. |
| Autenticazione negata | Al momento non è possibile effettuare l'accesso. Riprovare più tardi. |
| Messaggio introduttivo di attivazione (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. |
| Nuovo tentativo di attivazione (standard) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Premere cancelletto per completare la verifica. |
| Messaggio introduttivo di attivazione (PIN) | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Immettere il PIN seguito da cancelletto per completare la verifica. |
| Richiesta interno prima dei numeri | Grazie per aver usato il sistema di verifica dell'accesso Microsoft. Trasferire la chiamata all'interno... |

### <a name="set-up-a-custom-message"></a>Configurare un messaggio personalizzato

Per usare messaggi personalizzati, seguire questa procedura:

1. Passare a **Azure Active Directory** > **Sicurezza** > **MFA** > **Impostazioni telefonata**.
1. Selezionare **Aggiungi messaggio di saluto**.
1. Scegliere il **tipo di** messaggio di saluto, ad esempio *Greeting (standard) o* Authentication  *successful*.
1. Selezionare la **lingua**, in base alla sezione precedente sul [comportamento del linguaggio dei messaggi personalizzato](#custom-message-language-behavior).
1. Cercare e selezionare un file *audio mp3* o *wav* da caricare.
1. Quando si è pronti, **selezionare Aggiungi** e quindi **Salva.**

## <a name="mfa-service-settings"></a>Impostazioni del servizio MFA

Le impostazioni per le password delle app, gli indirizzi IP attendibili, le opzioni di verifica e l'autenticazione a più fattori per Azure AD Multi-Factor Authentication sono disponibili nelle impostazioni del servizio. Questo è più di un portale legacy e non fa parte del normale portale Azure AD.

Per accedere alle impostazioni del servizio, è possibile usare il portale di Azure passando a **Azure Active Directory** > **Sicurezza** > **MFA** > **Attività iniziali** > **Configura** > **Altre impostazioni di MFA basate sul cloud**. Verrà visualizzata una nuova finestra o scheda con opzioni *aggiuntive per le impostazioni del* servizio.

## <a name="trusted-ips"></a>Indirizzi IP attendibili

La _funzionalità Indirizzi IP_ attendibili di Azure AD Multi-Factor Authentication ignora le richieste di autenticazione a più fattori per gli utenti che a un intervallo di indirizzi IP definito. È possibile impostare intervalli IP attendibili per gli ambienti locali, in modo che quando gli utenti si trovare in una di queste posizioni, non Azure AD richiesta di Multi-Factor Authentication. La _funzionalità Indirizzi IP attendibili_ di Azure AD Multi-Factor Authentication richiede Azure AD Premium'edizione P1. 

> [!NOTE]
> Gli indirizzi IP attendibili possono includere intervalli IP privati solo quando si usa il server MFA. Per le applicazioni basate Azure AD Multi-Factor Authentication, è possibile usare solo intervalli di indirizzi IP pubblici.
>
> Gli intervalli IPv6 sono supportati solo [nell'interfaccia Posizione denominata (anteprima).](../conditional-access/location-condition.md)

Se l'organizzazione distribuisce l'estensione del server dei criteri di rete per garantire l'autenticazione a più fattori ad applicazioni locali, si prega di notare che gli indirizzi IP originali sembreranno sempre il server dei criteri di rete attraverso cui passa il tentativo di autenticazione.

| Tipo di tenant di Azure AD | Opzioni della funzionalità IP attendibile |
|:--- |:--- |
| Gestita |**Intervallo specifico di indirizzi IP:** gli amministratori specificano un intervallo di indirizzi IP che possono ignorare l'autenticazione a più fattori per gli utenti che a loro accesso dalla intranet aziendale. È possibile configurare un massimo di 50 intervalli IP attendibili.|
| Federato |**Tutti gli utenti federati:** tutti gli utenti federati che a esere connessi dall'interno dell'organizzazione possono ignorare l'autenticazione a più fattori. Gli utenti ignorano la verifica usando un'attestazione emessa da Active Directory Federation Services (AD FS).<br/>**Intervallo specifico di indirizzi IP:** gli amministratori specificano un intervallo di indirizzi IP che possono ignorare l'autenticazione a più fattori per gli utenti che a loro accesso dalla intranet aziendale. |

Il bypass IP attendibile funziona solo dall'interno della intranet aziendale. Se si seleziona l'opzione Tutti gli utenti **federati** e un utente accede dall'esterno della intranet aziendale, l'utente deve eseguire l'autenticazione usando l'autenticazione a più fattori. Il processo è lo stesso anche se l'utente presenta un'attestazione AD FS.

### <a name="end-user-experience-inside-of-corpnet"></a>Esperienza dell'utente finale all'interno della rete aziendale

Quando la funzionalità indirizzi IP attendibili è disabilitata, è necessaria l'autenticazione a più fattori per i flussi del browser. Le password dell'app sono necessarie per applicazioni rich client meno recenti.

Quando vengono usati indirizzi IP attendibili, l'autenticazione a più fattori non è necessaria per i flussi del browser. Le password delle app non sono necessarie per le applicazioni rich client meno recenti, a condizione che l'utente non abbia creato una password dell'app. Quando si usa una password dell'app, questa rimane obbligatoria.

### <a name="end-user-experience-outside-corpnet"></a>Esperienza dell'utente finale all'esterno della rete aziendale

Indipendentemente dal fatto che l'indirizzo IP attendibile sia definito, è necessaria l'autenticazione a più fattori per i flussi del browser. Le password dell'app sono necessarie per applicazioni rich client meno recenti.

### <a name="enable-named-locations-by-using-conditional-access"></a>Abilitare le località denominate con l'accesso condizionale

È possibile usare le regole di accesso condizionale per definire i percorsi denominati seguendo questa procedura:

1. Nel portale di Azure cercare e selezionare Azure Active Directory **,** quindi passare a **Sicurezza**  >  **Accesso condizionale** Percorsi  >  **denominati**.
1. Selezionare **Nuova località**.
1. Immettere un nome per la località.
1. Selezionare **Contrassegna come posizione attendibile**.
1. Immettere l'intervallo IP nella notazione CIDR per l'ambiente, ad esempio *40.77.182.32/27.*
1. Selezionare **Create** (Crea).

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Abilitare la funzionalità Indirizzi IP attendibili con l'accesso condizionale

Per abilitare gli IP attendibili usando i criteri di accesso condizionale, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare Azure Active Directory **,** quindi passare a **Sicurezza**  >   **Accesso condizionale** Percorsi  >  **denominati**.
1. Selezionare **Configura indirizzi IP attendibili MFA**.
1. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una delle due opzioni seguenti:

   * **Per le richieste degli utenti federati originate dalla Intranet dell'utente**: per scegliere questa opzione, selezionare la casella di controllo. Tutti gli utenti federati che esetendono l'accesso dalla rete aziendale ignorano l'autenticazione a più fattori usando un'attestazione emessa da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Per le richieste da un intervallo specifico di IP pubblici**: per scegliere questa opzione, immettere gli indirizzi IP nella casella di testo usando la notazione CIDR.
      * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
      * Per un singolo indirizzo IP, usare una notazione simile a **xxx.xxx.xxx.xxx/32**.
      * Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che a loron di accesso da questi indirizzi IP ignorano l'autenticazione a più fattori.

1. Selezionare **Salva**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Abilitare la funzionalità Indirizzi IP attendibili tramite le impostazioni del servizio

Se non si vogliono usare i criteri di accesso condizionale per  abilitare gli indirizzi IP attendibili, è possibile configurare le impostazioni del servizio per Azure AD Multi-Factor Authentication seguendo questa procedura:

1. Nel portale di Azure cercare e selezionare Azure Active Directory **e** quindi scegliere **Utenti.**
1. Selezionare **Multi-Factor Authentication**.
1. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
1. Nella pagina **Impostazioni servizio**, in **Indirizzi IP attendibili** scegliere una o entrambe le opzioni seguenti:

   * **Per le richieste degli utenti federati originate dalla Intranet dell'utente**: per scegliere questa opzione, selezionare la casella di controllo. Tutti gli utenti federati che a ogni accesso dalla rete aziendale ignorano l'autenticazione a più fattori usando un'attestazione rilasciata da AD FS. Assicurarsi che AD FS abbia una regola per aggiungere l'attestazione intranet al traffico appropriato. Se la regola non esiste, creare la regola seguente in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **For requests from a specified range of IP address subnets** (Per le richieste provenienti da un intervallo specificato di subnet di indirizzi IP): per scegliere questa opzione, immettere gli indirizzi IP nella casella di testo usando la notazione CIDR.
      * Per gli indirizzi IP nell'intervallo da xxx.xxx.xxx.1 a xxx.xxx.xxx.254, usare una notazione, ad esempio **xxx.xxx.xxx.0/24**.
      * Per un singolo indirizzo IP, usare una notazione simile a **xxx.xxx.xxx.xxx/32**.
      * Immettere fino a 50 intervalli di indirizzi IP. Gli utenti che a ogni accesso da questi indirizzi IP ignorano l'autenticazione a più fattori.

1. Selezionare **Salva**.

## <a name="verification-methods"></a>Metodi di verifica

È possibile scegliere i metodi di verifica disponibili per gli utenti nel portale delle impostazioni del servizio. Quando gli utenti registrano i propri account per Azure AD Multi-Factor Authentication, scelgono il metodo di verifica preferito tra le opzioni abilitate. Le indicazioni per il processo di registrazione utente sono disponibili in [Configurare l'account per l'autenticazione a più fattori.](../user-help/multi-factor-authentication-end-user-first-time.md)

Sono disponibili i metodi di verifica seguenti:

| Metodo | Descrizione |
|:--- |:--- |
| Chiamata al telefono |Invia una chiamata vocale automatizzata. Per l’autenticazione, l'utente risponde alla chiamata e preme # sul tastierino telefonico. Il numero di telefono non viene sincronizzato con Active Directory locale. |
| SMS al telefono |Invia un messaggio di testo contenente un codice di verifica. All'utente viene richiesto di immettere il codice di verifica nell'interfaccia di accesso. Questo processo è denominato SMS unidirezionale. L'SMS bidirezionale significa che l'utente deve disporre il testo in un codice specifico. L'SMS bidirezionale è deprecato e non è supportato a partire dal 14 novembre 2018. Gli amministratori devono abilitare un altro metodo per gli utenti che in precedenza usavano l'SMS bidirezionale.|
| Notifica tramite app per dispositivi mobili |Invia una notifica push al telefono o al dispositivo registrato. L'utente visualizza la notifica e seleziona **Verifica** per completare la verifica. L'app Microsoft Authenticator è disponibile per [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Codice di verifica dall'app per dispositivi mobili o dal token hardware |L'app Microsoft Authenticator genera un nuovo codice di verifica OATH ogni 30 secondi. L'utente immette il codice di verifica nell'interfaccia di accesso. L'app Microsoft Authenticator è disponibile per [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Per altre informazioni, vedere Quali metodi di autenticazione e verifica sono [disponibili in Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Abilitare e disabilitare i metodi di verifica

Per abilitare o disabilitare i metodi di verifica, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare Azure Active Directory **e** quindi scegliere **Utenti.**
1. Selezionare **Multi-Factor Authentication**.
1. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
1. Nella pagina **Impostazioni servizio**, in **Opzioni di verifica** selezionare o deselezionare i metodi da mettere a disposizione per gli utenti.
1. Fare clic su **Salva**.

## <a name="remember-multi-factor-authentication"></a>Memorizza Multi-Factor Authentication

La _funzionalità memorizza Multi-Factor Authentication_ consente agli utenti di ignorare le verifiche successive per un numero di giorni specificato, dopo aver eseguito l'accesso a un dispositivo usando Multi-Factor Authentication. Per migliorare l'usabilità e ridurre al minimo il numero di volte in cui un utente deve eseguire l'autenticazione a più fattori nello stesso dispositivo, selezionare una durata di 90 giorni o più.

> [!IMPORTANT]
> Se un dispositivo o un account viene compromesso, la memorizzazione di Multi-Factor Authentication per i dispositivi attendibili può influire sulla sicurezza. Se un account aziendale viene compromesso o un dispositivo attendibile viene smarrito o rubato, è necessario [revocare le sessioni MFA](howto-mfa-userdevicesettings.md).
>
> L'azione di ripristino revoca lo stato attendibile a tutti i dispositivi e l'utente deve eseguire nuovamente l'autenticazione a più fattori. È anche possibile indicare agli utenti di ripristinare Multi-Factor Authentication nei propri dispositivi, come indicato in Gestire le impostazioni per [l'autenticazione a più fattori.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Funzionamento della funzionalità

La funzionalità di memorizzazione di Multi-Factor Authentication imposta un cookie permanente nel browser quando l'utente seleziona l'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) al momento dell'accesso. L'utente non visualizza più la richiesta di Multi-Factor Authentication da questo browser fino alla scadenza del cookie. Se l'utente apre un altro browser sullo stesso dispositivo o cancella i cookie, la verifica verrà richiesta di nuovo.

L'opzione **Don't ask again for X days** (Non visualizzare più il messaggio per X giorni) non viene visualizzata nelle applicazioni non basate su browser, indipendentemente dal fatto che l'app supporti l'autenticazione moderna. Queste app usano _token di aggiornamento_ che creano nuovi token di accesso ogni ora. Quando un token di aggiornamento viene convalidato, Azure AD verifica che l'ultima autenticazione a più fattori sia stata verificata entro il numero di giorni specificato.

La funzionalità riduce il numero di autenticazioni per le app Web, che in genere viene richiesta ogni volta. La funzionalità può aumentare il numero di autenticazioni per i client di autenticazione moderna che normalmente vengono richieste ogni 180 giorni, se è configurata una durata inferiore. Può anche aumentare il numero di autenticazioni in combinazione con i criteri di accesso condizionale.

> [!IMPORTANT]
> La funzionalità memorizza **Multi-Factor Authentication** non  è compatibile con la funzionalità Mantieni l'accesso di AD FS, quando gli utenti eseguono l'autenticazione a più fattori per il AD FS tramite Azure server Multi-Factor Authentication o una soluzione di autenticazione a più fattori di terze parti.
>
> Se si seleziona **Mantieni l'accesso** in AD FS e si contrassegna il dispositivo come attendibile per Multi-Factor Authentication, l'utente non esegue automaticamente la verifica quando terminano i giorni per la **memorizzazione di Multi-Factor Authentication**. Azure AD richiede una nuova autenticazione a più fattori, ma AD FS restituisce un token con l'attestazione e la data originali di Multi-Factor Authentication, anziché eseguire nuovamente l'autenticazione a più fattori. **Questa reazione attiva un ciclo di verifica tra Azure AD e AD FS.**
>
> La funzionalità **Memorizza Multi-Factor Authentication** non è compatibile con gli utenti B2B e non sarà visibile per questi utenti durante l'accesso ai tenant invitati.
>

### <a name="enable-remember-multi-factor-authentication"></a>Abilitare la funzionalità Memorizza Multi-Factor Authentication

Per abilitare e configurare l'opzione che consente agli utenti di ricordare lo stato dell'autenticazione a più fattori e ignorare le richieste, seguire questa procedura:

1. Nel portale di Azure cercare e selezionare Azure Active Directory **e** quindi scegliere **Utenti.**
1. Selezionare **Multi-Factor Authentication**.
1. In Multi-Factor Authentication selezionare **Impostazioni servizio**.
1. Nella pagina **Impostazioni servizio,** in Memorizza autenticazione a più **fattori** selezionare l'opzione Consenti agli utenti di ricordare l'autenticazione a più fattori nei dispositivi **attendibili.**
1. Impostare il numero di giorni per consentire ai dispositivi attendibili di ignorare l'autenticazione a più fattori. Per un'esperienza utente ottimale, estendere la durata a *90* o più giorni.
1. Selezionare **Salva**.

### <a name="mark-a-device-as-trusted"></a>Contrassegnare un dispositivo come attendibile

Dopo aver abilitato la funzionalità memorizza Multi-Factor Authentication, gli utenti possono contrassegnare un dispositivo come attendibile al momento **dell'accesso selezionando l'opzione Non chiedere più**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui metodi disponibili per l'uso in Azure AD Multi-Factor Authentication, vedere Quali metodi di autenticazione e verifica [sono disponibili in Azure Active Directory?](concept-authentication-methods.md)
