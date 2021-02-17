---
title: Domande e risposte sull'app Microsoft Authenticator - Azure AD
description: Domande frequenti e risposte relative all'app di autenticazione Microsoft e alla verifica a due fattori.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 84dde96276414e98cbde05d6e046dce03227f60d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547860"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Domande frequenti sull'app Microsoft Authenticator

Questo articolo risponde a domande comuni sull'app Microsoft Authenticator. Se non viene visualizzata una risposta alla domanda, accedere al [forum dell'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

L'app Microsoft Authenticator ha sostituito l'app Azure Authenticator ed è l'app consigliata quando si usa Azure AD Multi-Factor Authentication. L'app Microsoft Authenticator è disponibile per [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) e [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="registering-a-device"></a>Registrazione di un dispositivo

**D**: sta registrando un dispositivo che accetta di concedere all'azienda o al servizio l'accesso al dispositivo?

**R**: la registrazione di un dispositivo consente al dispositivo di accedere ai servizi dell'organizzazione e non consente all'organizzazione di accedere al dispositivo.

### <a name="error-adding-account"></a>Errore durante l'aggiunta dell'account

**D**: quando si tenta di aggiungere l'account, viene ricevuto un messaggio di errore che informa che l'account che si sta provando ad aggiungere non è valido in questo momento. Contattare l'amministratore per risolvere il problema (convalida dell'univocità). " Cosa devo fare?

**R**: contattare l'amministratore e indicare che non è possibile aggiungere l'account all'autenticatore a causa di un problema di convalida dell'univocità. È necessario fornire il nome utente di accesso in modo che l'amministratore possa cercare l'utente nell'organizzazione.

### <a name="legacy-apns-support-deprecated"></a>Supporto di APNs legacy deprecato

**D**: poiché l'interfaccia binaria legacy per il servizio Apple Push Notification è stata deprecata nel 2020 novembre, come è possibile continuare a usare Microsoft Authenticator/Phone Factor per accedere?

**R**: [Apple ha annunciato la deprecazione](https://developer.apple.com/news/?id=11042019a) delle notifiche push che usano l'interfaccia binaria per i dispositivi iOS, ad esempio quelli usati dal fattore telefonico. Per continuare a ricevere notifiche push, è consigliabile che gli utenti aggiornino l'app Authenticator alla versione più recente dell'app. Nel frattempo, è possibile aggirarlo controllando manualmente le notifiche nell'app Authenticator.

### <a name="app-lock-feature"></a>Funzionalità di blocco app

**D**: che cos'è il blocco app e come è possibile usarlo per garantirne la sicurezza?

**R**: il blocco app consente di rendere più sicuri i codici di verifica monouso, le informazioni sulle app e le impostazioni dell'app. Quando il blocco app è abilitato, verrà richiesto di eseguire l'autenticazione usando il PIN del dispositivo o la biometria ogni volta che si apre Authenticator. Il blocco app consente anche di assicurarsi di essere l'unico utente che può approvare le notifiche richiedendo il PIN o la biometria ogni volta che viene approvata una notifica di accesso. È possibile attivare o disattivare blocco app nella pagina impostazioni di autenticazione. Per impostazione predefinita, il blocco app viene attivato quando si configura un PIN o una biometria nel dispositivo.<br><br>Sfortunatamente, non c'è alcuna garanzia che il blocco app arresti l'accesso dell'autenticatore da parte di un utente. Questo perché la registrazione del dispositivo può avvenire in altre posizioni all'esterno dell'autenticatore, ad esempio nelle impostazioni dell'account Android o nell'app Portale aziendale.

### <a name="windows-mobile-retired"></a>Windows Mobile ritirato

**D**: ho un dispositivo Windows Mobile e il Microsoft Authenticator su Windows Mobile è stato deprecato. È possibile continuare l'autenticazione con l'app?

**R**: tutte le autenticazioni che utilizzano il Microsoft Authenticator su Windows Mobile verranno ritirate dopo il 15 luglio 2020. Si consiglia vivamente di usare un metodo di autenticazione alternativo per evitare che gli account vengano bloccati.<br>Le opzioni alternative per gli utenti aziendali includono:<br><ul><li>Configurazione del Microsoft Authenticator per [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Configurazione di SMS per la](multi-factor-authentication-setup-phone-number.md) ricezione di codici di verifica.</li><li>Impostazione del numero di telefono per ricevere [chiamate telefoniche per verificarne l'identità](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Opzioni alternative per utenti Personal account Microsoft includono:<br><ul><li>Configurazione del Microsoft Authenticator per [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Configurazione di un metodo di accesso alternativo (SMS o posta elettronica) tramite l'aggiornamento delle info di sicurezza dalla [pagina sicurezza account Microsoft](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Screenshot Android

**D**: è possibile scattare screenshot dei codici OTP (One-time password) in Android Authenticator?

**R**: a partire dalla versione 6.2003.1704 di Authenticator Android, per impostazione predefinita tutti i codici OTP vengono nascosti ogni volta che viene eseguita una schermata dell'autenticatore. Se si desidera visualizzare i codici OTP negli screenshot o consentire ad altre app di acquisire la schermata di autenticazione, è possibile. È sufficiente attivare l'impostazione di **acquisizione schermo** in Authenticator e riavviare l'app.

### <a name="delete-stored-data"></a>Elimina dati archiviati

**D**: quali dati vengono archiviati dall'autenticatore per conto dell'utente e come è possibile eliminarli?

**R**: l'app Authenticator raccoglie tre tipi di informazioni:

- Informazioni sull'account fornite quando si aggiunge l'account. Questi dati possono essere rimossi attraversi la rimozione dell'account.
- Dati del log di diagnostica che rimangono solo nell'app fino a quando non si **inviano commenti e suggerimenti** nel menu principale dell'app per inviare i log a Microsoft. Questi log possono contenere dati personali, ad esempio indirizzi di posta elettronica, indirizzi del server o indirizzi IP. Possono inoltre contenere dati del dispositivo, ad esempio il nome del dispositivo e la versione del sistema operativo. I dati personali raccolti sono limitati alle informazioni necessarie per risolvere i problemi relativi alle app. È possibile esplorare questi file di log nell'app in qualsiasi momento per visualizzare le informazioni raccolte. Se si inviano i file di log, gli ingegneri delle app di autenticazione li useranno solo per risolvere i problemi segnalati dai clienti.
- Dati di uso non personali, come "aggiunta account di flusso in corso/account aggiunto" o "notifica approvata". Questi dati sono parte integrante delle decisioni di progettazione. L'utilizzo consente di determinare la posizione in cui è possibile migliorare le app in modi importanti. Quando si usa l'app per la prima volta, viene visualizzata una notifica di questa raccolta dati. Informa che è possibile disattivarla nella pagina **delle impostazioni** dell'app   . È possibile attivare o disattivare questa impostazione in qualsiasi momento.

### <a name="codes-in-the-app"></a>Codici nell'app

**D**: quali sono i codici nell'app per?

**R**: quando si apre Authenticator, verranno visualizzati gli account aggiunti come riquadri. Gli account aziendali o dell'Istituto di istruzione e gli account Microsoft personali avranno un numero di sei o otto cifre visibile nella visualizzazione a schermo intero dell'account (a cui si accede toccando il riquadro account). Per gli altri account, nella pagina **account** dell'app verrà visualizzato un numero di sei o otto cifre.<br>Questi codici verranno usati come password monouso per verificare che l'utente sia il proprietario. Dopo aver eseguito l'accesso con nome utente e password, digitare il codice di verifica associato a tale account. Ad esempio, se si usa Katy per accedere all'account contoso, è possibile toccare il riquadro account e quindi usare il codice di verifica 895823. Per l'account Outlook, è necessario seguire la stessa procedura.<br>Toccare il riquadro account contoso.<br>![Riquadri dell'account nell'app Authenticator](media/user-help-auth-app-faq/katy-signin.png)<br>Dopo aver toccato il riquadro Account Contoso, il codice di verifica è visibile a schermo intero.<br>![Codice di verifica nel riquadro dell'account in Authenticator](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Timer del conto alla rovescia

**D**: perché il numero accanto al codice mantiene il conteggio?

**R**: è possibile che venga visualizzato un timer di 30 secondi per il conteggio verso il basso accanto al codice di verifica attivo. Questo timer impedisce di accedere usando lo stesso codice due volte. Diversamente dalle password, questo numero non deve essere memorizzato dall'utente. Lo scopo è che solo qualcuno con accesso al telefono debba conoscere questo codice.

### <a name="grayed-account-tile"></a>Riquadro Account grigio

**D**: perché il riquadro dell'account è grigio?

**R**: alcune organizzazioni richiedono che l'autenticatore funzioni con Single Sign-on e protegga le risorse dell'organizzazione. In questo caso, l'account non viene usato per la verifica in due passaggi e viene visualizzato come grigio o inattivo. Questo tipo di account viene spesso denominato account "broker".

### <a name="device-registration"></a>Registrazione del dispositivo

**D**: che cos'è la registrazione del dispositivo?

**R**: l'organizzazione potrebbe richiedere la registrazione del dispositivo per tenere traccia dell'accesso alle risorse protette, ad esempio file e app. L'organizzazione può anche attivare l'accesso condizionale per ridurre il rischio di accesso indesiderato a tali risorse. È possibile annullare la registrazione del dispositivo tramite l'opzione **Impostazioni**, ma in questo caso si potrebbe perdere l'accesso ai messaggi e-mail in Outlook e ai file in OneDrive e non essere più in grado di usare l'accesso tramite telefono.

### <a name="verification-codes-when-connected"></a>Codici di verifica quando si è connessi

**D**: è necessario essere connessi a Internet o alla rete per ottenere e utilizzare i codici di verifica?

**R**: i codici non richiedono l'accesso a Internet o la connessione ai dati, quindi non è necessario il servizio telefonico per accedere. Inoltre, poiché l'app non resta in esecuzione quando viene chiusa, non consuma la batteria.

### <a name="no-notifications-when-app-is-closed"></a>Nessuna notifica quando l'app viene chiusa

**D**: perché vengono ricevute solo le notifiche quando l'app è aperta? Quando l'app viene chiusa, le notifiche non vengono ricevute.

**R**: se si ricevono notifiche, ma non un avviso, anche con la suoneria, è necessario controllare le impostazioni dell'app. Assicurarsi che l'app sia attivata per usare i suoni o la vibrazione per le notifiche. Se non si ricevono notifiche, è necessario controllare le condizioni seguenti:<ul><li>Il telefono è in modalità non disturbare o non interattiva? Queste modalità possono impedire alle app di inviare notifiche.</li><li>È possibile ricevere notifiche da altre app? In caso negativo, potrebbe essersi verificato un problema di connessione di rete nel telefono o del canale di notifiche Android o Apple. È possibile tentare di risolvere le connessioni di rete tramite le impostazioni del telefono. Potrebbe essere necessario comunicare con il provider di servizi per assistenza sul canale di notifiche Android o Apple.</li><li>Si ricevono notifiche per alcuni account nell'app, ma non per altri? In questo caso, rimuovere l'account problematico dall'app e aggiungerlo di nuovo consentendo le notifiche e quindi verificare se il problema è stato risolto.</li></ul>Se dopo aver tentato tutti questi passaggi i problemi persistono, inviare i file di log per la diagnostica. Aprire l'app, passare al menu di primo livello dell'app e quindi selezionare **Invia feedback**. Successivamente, accedere al forum dell' [app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) e indicare a Microsoft il problema che si sta riscontrando e i passaggi che sono stati tentati.

### <a name="switch-to-push-notifications"></a>Passa alle notifiche push

**D**: sto usando i codici di verifica nell'app, ma come posso passare alle notifiche push?

**R**: è possibile configurare le notifiche per l'account aziendale o dell'Istituto di istruzione (se consentito dall'amministratore) o per la account Microsoft personale. Le notifiche non funzioneranno per gli account di terze parti, ad esempio Google o Facebook.<br>Per passare il proprio account personale alle notifiche, è necessario registrare nuovamente il dispositivo con l'account. Passare ad **Aggiungi account**, selezionare **Account Microsoft personale** e quindi accedere usando il nome utente e la password.<br>Per l'account aziendale o dell'Istituto di istruzione, l'organizzazione decide se consentire o meno le notifiche con un solo clic.

### <a name="notifications-for-other-accounts"></a>Notifiche per altri account

**D**: le notifiche funzionano per gli account non Microsoft?

**R**: No, le notifiche funzionano solo con gli account Microsoft e gli account Azure Active Directory. Se l'ufficio o l'Istituto di istruzione USA Azure AD account, è possibile disattivare questa funzionalità.

### <a name="backup-and-recovery"></a>Backup e ripristino

**D**: è stato ottenuto un nuovo dispositivo o il dispositivo è stato ripristinato da un backup. Ricerca per categorie configurare di nuovo gli account in Authenticator?

**R**: se è stato attivato il **backup** nel dispositivo precedente, è possibile usare il backup precedente per recuperare le credenziali dell'account nel nuovo dispositivo iOS o Android. Per altre informazioni, vedere l'articolo [backup e ripristino delle credenziali dell'account con Authenticator](user-help-auth-app-backup-recovery.md) .

### <a name="lost-device"></a>Dispositivo perso

**D**: il dispositivo è stato smarrito o spostato in un nuovo dispositivo. come è possibile assicurarsi che le notifiche non continuino a essere inviate al dispositivo precedente?

**R**: l'aggiunta dell'autenticatore al nuovo dispositivo non rimuove automaticamente l'app dal dispositivo precedente. Anche l'eliminazione dell'app dal dispositivo precedente non è sufficiente. È necessario eliminare l'app dal dispositivo precedente e indicare a Microsoft o all'organizzazione di dimenticare e annullare la registrazione del dispositivo precedente.<ul><li>**Per rimuovere l'app da un dispositivo usando un account Microsoft personale.** Passare all'area di verifica in due passaggi della pagina [Sicurezza dell'account](https://account.microsoft.com/security)  e scegliere di disattivare la verifica per il dispositivo precedente.</li><li>**Per rimuovere l'app da un dispositivo usando un account Microsoft aziendale o dell'istituto di istruzione.** Passare all'area di verifica in due passaggi della [pagina app](https://myapps.microsoft.com/) o del portale personalizzato dell'organizzazione per disattivare la verifica per il dispositivo precedente.</li></ul>

### <a name="remove-account-from-app"></a>Rimuovi account dall'app

**D**: ricerca per categorie rimuovere un account dall'app?

**R**: toccare il riquadro account per l'account che si vuole rimuovere dall'app per visualizzare l'account a schermo intero. Toccare **Rimuovi account** per rimuovere l'account dall'app.<br>Se si dispone di un dispositivo registrato con l'organizzazione, potrebbe essere necessario un passaggio aggiuntivo per rimuovere l'account. In questi dispositivi, Authenticator viene registrato automaticamente come amministratore del dispositivo. Se si vuole disinstallare completamente l'app, è necessario prima annullare la registrazione dell'app nelle impostazioni relative.

### <a name="too-many-permissions"></a>Troppe autorizzazioni

**D**: perché l'app richiede così tante autorizzazioni?

**R**: di seguito è riportato l'elenco completo delle autorizzazioni che potrebbero essere richieste e il modo in cui vengono usate dall'app. Le autorizzazioni specifiche visualizzate dipendono dal tipo di telefono in uso.<ul><li>**Località**. A volte l'organizzazione vuole conoscere la propria posizione prima di consentire l'accesso ad alcune risorse. L'app richiederà questa autorizzazione solo se l'organizzazione dispone di criteri che richiedono la posizione.</li><li>**Usare hardware biometrico.** Alcuni account aziendali e dell'istituto di istruzione richiedono un PIN aggiuntivo al momento della verifica dell'identità. L'app richiede il consenso per usare il riconoscimento biometrico o facciale anziché immettere il PIN.</li><li>**Fotocamera.** Usata per eseguire la scansione dei codici a matrice quando si aggiunge un account aziendale, dell'istituto di istruzione o non Microsoft.</li><li>**Contatti e telefono.** L'app richiede questa autorizzazione per cercare account Microsoft aziendali o dell'Istituto di istruzione nel telefono e aggiungerli all'app.</li><li>**SMS.** Usato per verificare che il numero di telefono corrisponda al numero di record quando si accede con la account Microsoft personale per la prima volta. Viene inviato un SMS al telefono su cui è stata installata l'app che include un codice di verifica di 6-8 cifre. Non è necessario trovare questo codice e immetterlo perché l'autenticatore lo trova automaticamente nel messaggio di testo.</li><li>**Visualizzazione in altre app.** La notifica che si ottiene, che verifica l'identità, viene visualizzata anche in qualsiasi altra app in esecuzione.</li><li>**Ricezione di dati da Internet.** Questa autorizzazione è necessaria per l'invio di notifiche.</li><li>**Disabilitazione della sospensione del telefono.** Se si registra il dispositivo nell'organizzazione, l'organizzazione può modificare questo criterio nel telefono.</li><li>**Vibrazione di controllo.** È possibile scegliere se si vuole che il dispositivo vibri ogni volta che si riceve una notifica per la verifica dell'identità.</li><li>**Uso di hardware per la letture delle impronte digitali.** Alcuni account aziendali e dell'istituto di istruzione richiedono un PIN aggiuntivo al momento della verifica dell'identità. Per semplificare il processo, è possibile usare l'impronta digitale anziché immettere il PIN.</li><li> **Visualizzazione delle connessioni di rete.** Quando si aggiunge un account Microsoft, l'app richiede la connessione di rete o Internet.</li><li>**Lettura del contenuto dalla risorsa di archiviazione**. Questa autorizzazione viene usata solo quando si segnala un problema tecnico tramite le impostazioni dell'app. Vengono raccolte alcune informazioni dalla risorsa di archiviazione per diagnosticare il problema.</li><li>**Accesso di rete completo.** Questa autorizzazione è necessaria per l'invio di notifiche per la verifica dell'identità.</li><li>**Esecuzione all'avvio.** Se si riavvia il telefono, questa autorizzazione permette di continuare a ricevere notifiche per la verifica dell'identità.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Approva le richieste senza sbloccare

**D**: perché Authenticator consente di approvare una richiesta senza sbloccare il dispositivo?

**R**: non è necessario sbloccare il dispositivo per approvare le richieste di verifica perché è sufficiente verificare che l'utente disponga del telefono. Per la verifica in due passaggi è necessario dimostrare due elementi, una cosa che si conosce e una cosa. ovvero di conoscere la password. Si tratta del telefono (configurato con l'autenticatore e registrato come prova di autenticazione a più fattori). Pertanto, il telefono e l'approvazione della richiesta soddisfano i criteri per il secondo fattore di autenticazione.

### <a name="activity-notifications"></a>Notifiche di attività

**D**: perché si ricevono le notifiche relative all'attività dell'account?

**R**: le notifiche di attività vengono inviate all'autenticatore immediatamente quando viene apportata una modifica agli account Microsoft personali, contribuendo a garantire maggiore sicurezza. Queste notifiche sono state inviate in precedenza solo tramite posta elettronica e SMS. Per altre informazioni sulle notifiche sull'attività, vedere [Cosa succede se si verifica un accesso anomalo al tuo account](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Per modificare il punto in cui si ricevono le notifiche, accedere alla pagina [Dove possiamo contattarti con notifiche non critiche relative all'account?](https://account.live.com/SecurityNotifications/Update) dell'account.

### <a name="one-time-passcodes"></a>Codici di accesso monouso

**D**: i codici di accesso monouso non funzionano. Cosa devo fare?

**R**: assicurarsi che la data e l'ora del dispositivo siano corrette e che vengano sincronizzate automaticamente. Se la data e l'ora non sono corrette o non sono sincronizzate, il codice non funziona.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**D**: il sistema operativo Windows 10 Mobile è stato deprecato il 2019 dicembre. L'app Microsoft Authenticator sui sistemi operativi Windows Mobile sarà deprecata?

**R: l'** autenticatore in tutti i sistemi operativi Windows Mobile non sarà supportato dopo il 28 febbraio 2020. Gli utenti non saranno idonei a ricevere nuovi aggiornamenti per l'app dopo la data indicata sopra. Dopo il 28 febbraio 2020 i servizi Microsoft che attualmente supportano le autenticazioni che usano Microsoft Authenticator su tutti i sistemi operativi Windows Mobile inizieranno a ritirare il proprio supporto. Per eseguire l'autenticazione nei servizi Microsoft, si consiglia a tutti gli utenti di passare a un meccanismo di autenticazione alternativo prima di questa data.

### <a name="default-mail-app"></a>App di posta elettronica predefinita

**D**: durante l'accesso all'account aziendale o dell'Istituto di istruzione con l'app di posta elettronica predefinita fornita con iOS, viene richiesto all'autenticatore le informazioni di verifica della sicurezza. Dopo aver immesso le informazioni ed essere tornato all'app di posta elettronica, viene visualizzato un errore. In che modo è possibile risolvere questo problema?

**R**: questo problema si verifica più spesso perché l'accesso e l'app di posta elettronica si verificano in due diverse app, causando la mancata esecuzione del processo di accesso in background iniziale e l'interruzione. Per provare a risolvere questo problema, è consigliabile selezionare l'icona di **Safari** sul lato inferiore destro dello schermo quando si accede all'app di posta elettronica. Se si passa a Safari, l'intero processo di accesso viene eseguito in una singola app, consentendo la correttezza dell'operazione.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchos 7

**D**: perché si verificano problemi con Apple Watch su watchos 7?

**R**: si è verificato un problema con l'approvazione delle notifiche per watchos 7 e si sta lavorando con Apple per risolvere il problema. Nel frattempo, è necessario approvare tutte le notifiche che richiedono l'applicazione Microsoft Authenticator watchos sul telefono.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch non Visualizza gli account

**D**: perché non vengono visualizzati tutti gli account quando si apre authenticator nel Apple Watch?

**R**: l'autenticatore supporta solo account personali o aziendali o dell'Istituto di istruzione di Microsoft con notifiche push nell'app Apple Watch Companion. Per gli altri account, ad esempio Google o Facebook, è necessario aprire l'app Authenticator sul telefono per visualizzare i codici di verifica.

### <a name="apple-watch-notifications"></a>Notifiche Apple Watch

**D**: perché non è possibile approvare o negare le notifiche nel Apple Watch?

**R**: prima di tutto, assicurarsi di avere eseguito l'aggiornamento a Authenticator versione 6.0.0 o successiva nell'iPhone. Aprire quindi l'app complementare Microsoft Authenticator nel dispositivo Apple Watch e cercare tutti gli account sotto i quali è visualizzato il pulsante **Configura**. Completare il processo di installazione per approvare le notifiche per tali account.

### <a name="apple-watch-communication-error"></a>Errore di comunicazione Apple Watch

**D**: viene ricevuto un errore di comunicazione tra il Apple Watch e il telefono. Com'è possibile risolvere il problema?

**R**: questo errore si verifica quando la schermata di controllo passa alla modalità di sospensione prima di completare la comunicazione con il telefono.<br><b>Se l'errore si verifica durante l'installazione:</b><br>Provare a ripetere l'installazione, assicurandosi di mantenere attivo l'orologio fino al completamento del processo. Allo stesso tempo, aprire l'app sul telefono e rispondere a qualsiasi richiesta visualizzata.<br>Se il telefono e l'orologio non comunicano, è possibile provare a eseguire le azioni seguenti:<ol><li>Forzare la chiusura dell'app del telefono Microsoft Authenticator e aprirla di nuovo sull'iPhone.</li><li>Forzare la chiusura dell'app complementare nel dispositivo Apple Watch.<ol><li> Aprire l'app complementare Microsoft Authenticator nell'orologio</li><li>Tenere premuto il pulsante laterale fino a visualizzare la schermata **Spegni**.</li><li>Rilasciare il pulsante laterale e tenere premuta l'icona Digital Crown per forzare la chiusura dell'app attiva.</li></ol></li><li>Disattivare sia Bluetooth sia il Wi-Fi per il telefono e l'orologio e quindi riattivarli.</li><li>Riavviare l'iPhone e l'orologio.</li></ol><b>Se l'errore si verifica quando si tenta di approvare una notifica:</b><br>La volta successiva in cui si tenta di approvare una notifica nel dispositivo Apple Watch, mantenere attivo lo schermo fino al completamento della richiesta e fino a sentire il suono indicante l'esito positivo dell'operazione.

### <a name="apple-watch-companion-app-not-syncing"></a>Non è in Apple Watch la sincronizzazione dell'app complementare

**D**: perché non è Microsoft Authenticator app complementare per Apple Watch la sincronizzazione o la visualizzazione in My Watch?

**R**: se l'app non viene visualizzata nell'orologio, provare a eseguire le azioni seguenti: <ol><li>Assicurarsi che l'orologio esegua watchOS 4.0 o versioni successive.</li><li>Sincronizzare di nuovo l'orologio.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch app complementare arrestata in modo anomalo

**D**: l'app Companion Apple Watch si è arrestata in modo anomalo. è possibile inviare i log di arresto anomalo per l'indagine della causa?

**R**: per prima cosa è necessario assicurarsi di aver scelto di condividere le analisi con Microsoft. Gli utenti di TestFlight sono già iscritti. In caso contrario, è possibile passare a **Impostazioni > Privacy > Analisi** e selezionare le opzioni **Share iPhone & Watch analytics** (Condividi analisi iPhone e orologio) e **Share with App Developers** (Condividi con sviluppatori app).<br>Dopo l'iscrizione, è possibile provare a riprodurre l'arresto anomalo in modo che i log di arresto anomalo vengano inviati automaticamente a Microsoft per le indagini. Se non è possibile riprodurre l'arresto anomalo, tuttavia, è possibile copiare manualmente i file di log e inviarli a Microsoft.<ol><li>Aprire l'app dell'orologio nel telefono, passare a **Impostazioni > Generali** e quindi fare clic su **Copy Watch Analytics** (Copia analisi orologio).</li><li>Individuare l'arresto anomalo corrispondente in **Impostazioni > Privacy > Analisi > Dati di analisi** e quindi copiare manualmente il testo completo.</li><li>Aprire Authenticator sul telefono e incollare il testo copiato nella casella  **descrivere il problema** riscontrato in caso di **problemi?** nella pagina  **Invia commenti e suggerimenti** . </li></ol>

## <a name="autofill-with-authenticator"></a>Riempimento automatico con Authenticator

**D**: che cos'è il riempimento automatico con Authenticator?

**R**: l'app Authenticator Archivia ora in modo sicuro e riempie automaticamente le password delle app e dei siti Web visitati sul telefono. È possibile usare il riempimento automatico per sincronizzare e compilare automaticamente le password nei dispositivi iOS e Android. Dopo aver configurato l'app Authenticator come provider di riempimento automatico sul telefono, consente di salvare le password quando vengono immesse in un sito o in una pagina di accesso all'app. Le password vengono salvate come parte del [account Microsoft personale](https://account.microsoft.com/account) e sono disponibili anche quando si accede a Microsoft Edge con l'account Microsoft personale.

**D**: quali sono le informazioni che possono essere riempite dall'autenticatore?

**R: l'** autenticatore può compilare automaticamente i nomi utente e le password nei siti e nelle app visitate sul telefono.

**D**: ricerca per categorie attivare il riempimento automatico della password in Authenticator sul telefono?

**R**: attenersi alla seguente procedura:

1. Aprire l'app Authenticator.
1. Nella scheda **password** in authenticator selezionare **Accedi con Microsoft** e accedere usando [il account Microsoft](https://account.microsoft.com/account). Questa funzionalità supporta attualmente solo account Microsoft e non supporta ancora account aziendali o dell'Istituto di istruzione.

**D**: ricerca per categorie rendere autenticatore il provider di riempimento automatico predefinito sul telefono?

**R**: attenersi alla seguente procedura:

1. Aprire l'app Authenticator.
1. Nella scheda **password** nell'app selezionare **Accedi con Microsoft** e accedere usando [il account Microsoft](https://account.microsoft.com/account).
1. Eseguire una delle operazioni seguenti:

   - In iOS, in **Impostazioni**, selezionare **come attivare il riempimento automatico** nella sezione Impostazioni riempimento automatico per informazioni su come impostare Authenticator come provider di riempimento automatico predefinito.
   - In Android in **Impostazioni** selezionare **Imposta come provider di riempimento automatico** nella sezione Impostazioni riempimento automatico.

**D**: cosa accade se il **riempimento automatico** non è disponibile nelle impostazioni?

**R**: se il riempimento automatico non è disponibile in Authenticator, è possibile che non sia stato ancora consentito il riempimento automatico per il tipo di account o organizzazione. È possibile usare questa funzionalità in un dispositivo in cui l'account aziendale o dell'Istituto di istruzione non viene aggiunto. Per altre informazioni su come consentire il riempimento automatico per l'organizzazione, vedere [riempimento automatico per gli amministratori it](#autofill-for-it-admins).

**D**: ricerca per categorie arrestare la sincronizzazione delle password?

**R**: per interrompere la sincronizzazione delle password nell'app Authenticator, aprire **Impostazioni**  >  **riempimento automatico** impostazioni  >  **account di sincronizzazione**. Nella schermata successiva è possibile selezionare **Arresta sincronizzazione e Rimuovi tutti i dati di riempimento automatico**. Questa operazione eliminerà le password e altri dati di riempimento automatico dal dispositivo. La rimozione dei dati di riempimento automatico non influisce sull'autenticazione a più fattori.

**D**: in che modo le password sono protette dall'app Authenticator?

**R**: l'app Authenticator fornisce già un livello elevato di sicurezza per l'autenticazione a più fattori e la gestione degli account e la stessa barra di sicurezza elevata viene estesa anche alla gestione delle password.

- **L'autenticazione avanzata è necessaria per l'app Authenticator**: l'accesso all'autenticatore richiede un secondo fattore. Ciò significa che le password all'interno dell'app Authenticator sono protette anche se qualcuno dispone della password account Microsoft.
- **I dati di riempimento automatico sono protetti con biometria e codice** di accesso: prima che sia possibile compilare automaticamente la password in un'app o in un sito, l'autenticatore richiede un codice biometrico o dispositivo Ciò consente di aggiungere ulteriore sicurezza in modo che anche se un altro utente ha accesso al dispositivo, non può riempire o visualizzare la password, perché non è in grado di fornire l'input biometrico o PIN del dispositivo. Inoltre, un utente non è in grado di aprire la pagina password a meno che non fornisca biometrico o PIN, anche se disattiva il blocco app nelle impostazioni dell'app.
- **Password crittografate nel dispositivo: le** password nel dispositivo vengono crittografate e le chiavi di crittografia/decrittografia non vengono mai archiviate e generate sempre quando necessario. Le password vengono decrittografate solo quando l'utente vuole, ovvero durante il riempimento automatico o quando l'utente desidera visualizzare la password, che richiedono sia biometrica che PIN.
- **Sicurezza del cloud e della rete**: le password nel cloud vengono crittografate e decrittografate solo quando raggiungono il dispositivo. Le password vengono sincronizzate tramite una connessione HTTPS protetta tramite SSL, consentendo a un utente malintenzionato di intercettare dati sensibili durante la sincronizzazione. Viene inoltre garantita la verifica dell'integrità dei dati sincronizzati sulla rete mediante funzioni con hash crittografico (in particolare il codice di autenticazione messaggi basato su hash).

## <a name="autofill-for-it-admins"></a>Riempimento automatico per gli amministratori IT

**D**: i dipendenti o gli studenti devono usare la funzione di riempimento automatico delle password nell'app Authenticator?

**R**: Sì, il riempimento automatico funziona ora per la maggior parte degli utenti aziendali anche quando viene aggiunto un account aziendale o dell'Istituto di istruzione all'app Authenticator. È possibile compilare un modulo per configurare (consentire o negare) il riempimento automatico per l'organizzazione e [inviarlo al team Authenticator](https://aka.ms/ConfigureAutofillInAuthenticator).

**D**: la password dell'account aziendale o dell'Istituto di istruzione viene sincronizzata automaticamente?

**R**: No. Il riempimento automatico della password non sincronizza la password dell'account aziendale o dell'Istituto di istruzione per gli utenti. Quando gli utenti visitano un sito o un'app, l'autenticatore offrirà la password per il sito o l'app e la password verrà salvata solo quando l'utente sceglie.
  
**D**: è possibile consentire solo ad alcuni utenti dell'organizzazione di eseguire il riempimento automatico?

**R**: No. In questo momento le aziende possono abilitare solo il riempimento automatico delle password per tutti o nessuno dei dipendenti.

**D**: cosa accade se il dipendente o lo studente ha più account aziendali o dell'Istituto di istruzione? Ad esempio, il dipendente dispone di account di più aziende o scuole nell'Microsoft Authenticator.

**R**: per poter usare il proprietario dell'app, è necessario che tutte le aziende o le scuole aggiunte nell'app Authenticator siano consentite. L'unica eccezione a questa restrizione è rappresentata dal momento in cui il dipendente o lo studente aggiunge l'account aziendale o dell'Istituto di istruzione all'autenticazione a più fattori basata sul cloud Microsoft come [account esterno o di terze parti](user-help-auth-app-add-non-ms-account.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi durante il recupero del codice di verifica per l'account Microsoft personale, vedere la sezione **Risoluzione dei problemi del codice di verifica** dell'articolo [Informazioni di sicurezza e codici di verifica dell'account Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Per altre informazioni sulla verifica in due passaggi, vedere [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

- Per altri dettagli sulle informazioni di sicurezza, vedere [Panoramica (anteprima) delle informazioni di sicurezza](./security-info-setup-signin.md)

- Se questo articolo non ha fornito le risposte necessarie, saranno apprezzati commenti e suggerimenti. Visitare il [forum relativo all'app Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) per pubblicare una domanda e ricevere assistenza dalla community o lasciare un commento in questa pagina.
