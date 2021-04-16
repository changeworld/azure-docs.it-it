---
title: Risolvere i problemi relativi all'accesso Single Sign-On basato su password in Azure Active Directory
description: Risolvere i problemi relativi a Azure AD'app configurata per l'accesso Single Sign-On basato su password.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376478"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Risolvere i problemi di Single Sign-On basato su password in Azure AD

Per usare l'accesso Single Sign-On (SSO) basato su password in App personali, è necessario installare l'estensione del browser. L'estensione viene scaricata automaticamente quando si seleziona un'app configurata per l'accesso SSO basato su password. Per informazioni sull'uso App personali dal punto di vista dell'utente finale, vedere App personali [guida del portale.](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>App personali'estensione del browser non installata
Assicurarsi che l'estensione del browser sia installata. Per altre informazioni, vedere [Pianificare una distribuzione Azure Active Directory App personali distribuzione](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Single Sign-On non configurato
Assicurarsi che sia configurato l'accesso Single Sign-On basato su password. Per altre informazioni, vedere [Configurare l'accesso Single Sign-On basato su password.](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Utenti non assegnati
Assicurarsi che l'utente sia assegnato all'app. Per altre informazioni, vedere [Assegnare un utente o un gruppo a un'app.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Le credenziali vengono compilate, ma l'estensione non le invia

Questo problema si verifica in genere se il fornitore dell'applicazione ha modificato di recente la pagina di accesso per aggiungere un campo, ha modificato un identificatore usato per rilevare i campi nome utente e password o ha modificato l'esperienza di accesso alla propria applicazione. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni nelle integrazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Nel caso in cui una di queste integrazioni non funzioni correttamente, aprire un caso di supporto in modo che il problema possa essere risolto il più rapidamente possibile.

**Se l'utente è in contatto con il fornitore dell'applicazione,** può inviargli la soluzione offerta in modo che Microsoft possa lavorare con loro per integrare nativamente la loro applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Le credenziali vengono compilate e inoltrate, ma un messaggio nella pagina indica che le credenziali sono errate

Per risolvere questo problema, provare innanzitutto queste soluzioni:

- Chiedere all'utente di provare ad **accedere direttamente al sito Web dell'applicazione** con le credenziali archiviate.

  * Se l'accesso funziona, fare clic  sul pulsante Aggiorna  credenziali nel  riquadro dell'applicazione nella sezione App di [App personali](https://myapps.microsoft.com/) per aggiornarle al nome utente e alla password funzionanti più recenti.

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante **Aggiorna credenziali**.

- Se è stato l'utente ad assegnarsi le credenziali, chiedergli di **verificare che la password dell'applicazione non sia scaduta** ed eventualmente **aggiornarla** accedendo all'applicazione direttamente.

  * Dopo aver aggiornato la password nell'applicazione, richiedere  all'utente di  fare clic  sul pulsante Aggiorna credenziali nel riquadro dell'applicazione nella sezione App di App personali per aggiornarle al nome utente e alla password funzionanti più [recenti.](https://myapps.microsoft.com/)

  * Se è stato un amministratore ad assegnare le credenziali per questo utente, individuare l'assegnazione dell'applicazione dell'utente o del gruppo passando alla scheda **Utenti gruppi** dell'applicazione, selezionando l'assegnazione e facendo clic sul pulsante **Aggiorna credenziali**.

- Assicurarsi che l App personali del browser sia in esecuzione e abilitata nel browser dell'utente.

- Assicurarsi che gli utenti non tentino di accedere all'applicazione da App personali in **modalità in incognito, inprivate o privata.** L App personali non è supportata in queste modalità.

Se il problema persiste, è possibile che sia stata apportata una modifica sul lato applicazione che ha interrotto temporaneamente l'integrazione dell'applicazione con Azure AD. Il fornitore dell'applicazione potrebbe, ad esempio, aver introdotto nella pagina uno script con un comportamento che varia tra l'input manuale e l'input automatico e che causa l'interruzione nell'integrazione automatica. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Controllare se la pagina di accesso dell'applicazione è cambiata di recente o richiede un campo aggiuntivo

Se la pagina di accesso dell'applicazione è cambiata drasticamente, talvolta è possibile che si verifichi un'interruzione dell'integrazione. Un esempio è quando il fornitore dell'applicazione aggiunge un campo di accesso, un captcha o l'autenticazione a più fattori alle proprie esperienze. In molti casi Microsoft può collaborare con i fornitori di applicazioni per risolvere i problemi rapidamente.

Mentre Microsoft dispone di tecnologie per rilevare automaticamente le interruzioni delle integrazioni delle applicazioni, potrebbe non essere possibile rilevare i problemi immediatamente oppure i problemi richiedono tempo per essere risolti. Quando un'integrazione non funziona correttamente, è possibile aprire un caso di supporto per risolvere il problema nel più breve tempo possibile. 

**Se l'utente è inoltre in contatto con il fornitore dell'applicazione,** **può inviargli la soluzione offerta da Microsoft** affinché si possa collaborare a integrare in modo nativo l'applicazione con Azure Active Directory. Per iniziare, è possibile indirizzare il fornitore all'[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

## <a name="capture-sign-in-fields-for-an-app"></a>Acquisire i campi di accesso per un'app

L'acquisizione dei campi di accesso è supportata solo per le pagine di accesso abilitate per HTML. Non è supportato per le pagine di accesso non standard, ad esempio quelle che usano Adobe Flash o altre tecnologie non abilitate per HTML.

Esistono due modi per acquisire i campi di accesso per le app personalizzate:

- **L'acquisizione automatica dei** campi di accesso funziona bene con la maggior parte delle pagine di accesso abilitate per HTML, se usano *ID DIV* noti per i campi nome utente e password. Il codice HTML nella pagina viene raschiato per trovare GLI ID DIV che soddisfano determinati criteri. I metadati vengono salvati in modo che possano essere riprodotti nell'app in un secondo momento.

- **L'acquisizione manuale dei campi di** accesso viene usata se il fornitore *dell'app non etichetta i* campi di input di accesso. L'acquisizione manuale viene usata anche se il fornitore esegue il rendering di più *campi che non possono essere rilevati automaticamente.* Azure Active Directory (Azure AD) può archiviare i dati per tutti i campi presenti nella pagina di accesso, se si è in grado di indicare la posizione di tali campi nella pagina.

In generale, se l'acquisizione automatica dei campi di accesso non funziona, provare l'opzione manuale.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Acquisire automaticamente i campi di accesso per un'app

Per configurare l'accesso SSO basato su password usando l'acquisizione automatica dei campi di accesso, seguire questa procedura:
1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o co-amministratore.
2. Nel riquadro di spostamento a sinistra selezionare **Tutti** i servizi per aprire l'Azure AD predefinita.
3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.
4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.
5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.
   > [!NOTE]
   > Se l'app desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le** applicazioni. Impostare **l'opzione** Mostra su "Tutte le applicazioni".
6. Selezionare l'app che si vuole configurare per l'accesso SSO.
7. Dopo il caricamento dell'app, selezionare **Single Sign-On** nel riquadro di spostamento a sinistra.
8. Selezionare **Modalità di accesso basato su** password.
9. Immettere **l'URL di accesso,** ovvero l'URL della pagina in cui gli utenti immettono il nome utente e la password per accedere. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL specificato.*
10. Selezionare **Salva**.
    La pagina viene automaticamente ritasciata per le caselle di input relative a nome utente e password. È ora possibile usare Azure AD per trasmettere in modo sicuro le password a tale app usando l'App personali del browser.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Acquisire manualmente i campi di accesso per un'app

Per acquisire manualmente i campi di accesso, è necessario che sia installata l App personali del browser. Inoltre, il browser non può essere in esecuzione in modalità *privata,* *in incognito* *o* privata.

Per configurare l'accesso SSO basato su password per un'app usando l'acquisizione manuale dei campi di accesso, seguire questa procedura:
1. Aprire il [portale di Azure](https://portal.azure.com/). Accedere come amministratore globale o co-amministratore.
2. Nel riquadro di spostamento a sinistra selezionare **Tutti** i servizi per aprire l'Azure AD predefinita.
3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi **selezionare Azure Active Directory**.
4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.
5. Selezionare **Tutte le** applicazioni per visualizzare un elenco delle app.
   > [!NOTE] 
   > Se l'app desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le** applicazioni. Impostare **l'opzione** Mostra su "Tutte le applicazioni".
6. Selezionare l'app che si vuole configurare per l'accesso SSO.
7. Dopo il caricamento dell'app, selezionare **Single Sign-On** nel riquadro di spostamento a sinistra.
8. Selezionare **Modalità di accesso basato su** password.
9. Immettere **l'URL di accesso,** ovvero la pagina in cui gli utenti immettono il nome utente e la password per l'accesso. *Assicurarsi che i campi di accesso siano visibili nella pagina per l'URL specificato.*
10. Selezionare **Configure appname Password Single Sign-On Settings (Configurare le impostazioni di Single Sign-On di *&lt; appname &gt;* Password).**
11. Selezionare **Rileva manualmente i campi di accesso**.
14. Selezionare **OK**.
15. Selezionare **Salva**.
16. Seguire le istruzioni per usare App personali.


## <a name="troubleshoot-problems"></a>Risolvere i problemi

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Viene visualizzato l'errore "Non è stato possibile trovare campi di accesso in tale URL"

Questo messaggio di errore viene visualizzato quando il rilevamento automatico dei campi di accesso ha esito negativo. Per risolvere il problema, provare il rilevamento manuale dei campi di accesso. Vedere la [sezione Acquisire manualmente i campi di accesso per un'applicazione](#manually-capture-sign-in-fields-for-an-app) di questo articolo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Viene visualizzato l'errore "Impossibile salvare la configurazione dell'accesso Single Sign-On"

Raramente, l'aggiornamento della configurazione SSO ha esito negativo. Per risolvere il problema, provare a salvare di nuovo la configurazione.

Se si continua a ricevere l'errore, aprire un caso di supporto. Includere le informazioni descritte nelle sezioni Visualizzare i dettagli delle notifiche del [portale](#view-portal-notification-details) e Inviare i dettagli di notifica [a](#send-notification-details-to-a-support-engineer-to-get-help) un tecnico del supporto per ottenere assistenza in questo articolo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Non è possibile rilevare manualmente i campi di accesso per l'app

È possibile osservare i comportamenti seguenti quando il rilevamento manuale non funziona:
- Il processo di acquisizione manuale sembra funzionare, ma i campi acquisiti non sono corretti.
- I campi corretti non vengono evidenziati durante l'esecuzione del processo di acquisizione.
- Il processo di acquisizione consente di accedere alla pagina di accesso dell'app come previsto, ma non accade nulla.
- L'acquisizione manuale sembra funzionare, ma l'accesso Single Sign-On non si verifica quando gli utenti passano all'app da App personali.

Se si verifica uno di questi problemi, eseguire le operazioni seguenti:
- Assicurarsi di avere la versione più recente dell'estensione del browser App personali installata *e abilitata.*
- Assicurarsi che il browser non sia *in modalità in incognito,* *inPrivate* o *Privata* durante il processo di acquisizione. L App personali'estensione non è supportata in queste modalità.
- Assicurarsi che gli utenti non tentino di accedere all'app da App personali *in incognito,* *in modalità privata* o *privata.*
- Provare di nuovo a eseguire il processo di acquisizione manuale. Assicurarsi che i marcatori rossi siano sopra i campi corretti.
- Se il processo di acquisizione manuale sembra smettere di rispondere o la pagina di accesso non risponde, provare a ripetere il processo di acquisizione manuale. Ma questa volta, dopo aver completato il processo, premere F12 per aprire la console per sviluppatori del browser. Selezionare la **scheda della console.** Digitare **window.location=" l'URL di accesso specificato durante la configurazione *dell'app &lt; &gt;*"** e quindi premere INVIO. In questo modo viene forzato un reindirizzamento della pagina che termina il processo di acquisizione e archivia i campi acquisiti.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Non è possibile aggiungere un altro utente all'app SSO basata su password

L'app SSO basata su password ha un limite di 48 utenti. Pertanto, ha un limite di 48 chiavi per le coppie nome utente/password per ogni app.
Per aggiungere altri utenti, è possibile:
-   Aggiungere un'altra istanza dell'app
-   Rimuovere prima gli utenti che non usano più l'app

## <a name="request-support"></a>Richiedere supporto 
Se viene visualizzato un messaggio di errore quando si configura l'accesso SSO e si assegnano gli utenti, aprire un ticket di supporto. Includere il maggior numero possibile di informazioni seguenti:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e intervallo di tempo in cui si è verificato l'errore
-   Tracce Fiddler

### <a name="view-portal-notification-details"></a>Visualizzare i dettagli delle notifiche del portale

Per visualizzare i dettagli di qualsiasi notifica del portale, seguire questa procedura:
1. Selezionare **l'icona** Notifiche (la campana) nell'angolo superiore destro del portale di Azure.
2. Selezionare una notifica che mostra uno *stato di* errore. (Hanno un "!") rosso.
   > [!NOTE]
   > Non è possibile selezionare le notifiche nello *stato Operazione* completata o *In* corso.
3. Viene aperto il riquadro **Dettagli notifica**. Leggere le informazioni per informazioni sul problema.
5. Se è ancora necessaria assistenza, condividere le informazioni con un tecnico del supporto o con il gruppo di prodotti. Selezionare **l'icona** di copia a destra della casella **Copia errore** per copiare i dettagli della notifica da condividere.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Inviare i dettagli della notifica a un tecnico del supporto tecnico per ottenere assistenza

È importante condividere tutti  i dettagli elencati in questa sezione con il supporto in modo che possano essere utili rapidamente. Per registrarlo, è possibile acquisire uno screenshot o selezionare **Copia errore**.

Le informazioni seguenti illustrano il significato di ogni elemento di notifica e forniscono esempi.

#### <a name="essential-notification-items"></a>Elementi di notifica essenziali

- **Titolo:** titolo descrittivo della notifica.

   Esempio: *Impostazioni proxy applicazione*

- **Descrizione:** cosa si è verificato in seguito all'operazione.

   Esempio: *l'URL interno immesso è già in uso da un'altra applicazione.*

- **ID notifica:** ID univoco della notifica.

    Esempio: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID richiesta client:** l'ID richiesta specifico creato dal browser.

    Esempio: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Timestamp UTC:** timestamp del momento in cui si è verificata la notifica, in formato UTC.

    Esempio: *2017-03-23T19:50:43.7583681Z*

- **ID transazione** interno: ID interno usato per cercare l'errore nei sistemi.

    Esempio: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** l'utente che ha eseguito l'operazione.

    Esempio: *tperkins \@ f128.info*

- **ID tenant:** ID univoco del tenant di cui è membro l'utente che ha eseguito l'operazione.

    Esempio: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID oggetto utente: ID** univoco dell'utente che ha eseguito l'operazione.

    Esempio: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Elementi di notifica dettagliati

- **Nome visualizzato:**(può essere vuoto) un nome visualizzato più dettagliato per l'errore.

    Esempio: *Impostazioni proxy dell'applicazione*

- **Status**: stato specifico della notifica.

    Esempio: *Operazione non riuscita*

- **ID oggetto:**(può essere vuoto) l'ID oggetto su cui è stata eseguita l'operazione.

   Esempio: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Dettagli:** descrizione dettagliata di ciò che si è verificato in seguito all'operazione.

    Esempio: *l'URL interno <https://bing.com/> ' ' non è valido perché è già in uso.*

- **Errore di copia:** consente di selezionare **l'icona** di copia a destra della casella di testo Copia errore per copiare i dettagli della notifica per supporto. 

    Esempio:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Pianificare una distribuzione di App personali](my-apps-deployment-plan.md)
