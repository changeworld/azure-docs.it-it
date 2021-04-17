---
title: Guida alla risoluzione dei problemi di Azure Storage Explorer | Microsoft Docs
description: Panoramica delle tecniche di debug per Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568540"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Azure Storage Explorer

Microsoft Azure Storage Explorer è un'app autonoma che facilita l'uso dei dati con Archiviazione di Azure in Windows, macOS e Linux. L'app può connettersi ad account di archiviazione ospitati in Azure, cloud nazionali e Azure Stack.

Questa guida riepiloga le soluzioni per i problemi che si verificano comunemente in Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problemi relativi alle autorizzazioni del controllo degli accessi in base al ruolo di Azure

Il controllo degli accessi in base al ruolo di [Azure](../../role-based-access-control/overview.md) consente la gestione degli accessi altamente granulare delle risorse di Azure combinando set di autorizzazioni nei _ruoli_. Ecco alcune strategie per ottenere il funzionamento ottimale del controllo degli accessi in base al ruolo di Azure Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Ricerca per categorie accedere alle risorse in Storage Explorer?

Se si verificano problemi di accesso alle risorse di archiviazione tramite il controllo degli accessi in base al ruolo di Azure, è possibile che non siano stati assegnati i ruoli appropriati. Le sezioni seguenti descrivono le autorizzazioni Storage Explorer attualmente necessarie per l'accesso alle risorse di archiviazione. Se non si è certi di disporre dei ruoli o delle autorizzazioni appropriati, contattare l'amministratore dell'account Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problema relativo alle autorizzazioni "Lettura: Elenca/Ottieni account di archiviazione"

È necessario disporre dell'autorizzazione per elencare gli account di archiviazione. Per ottenere questa autorizzazione, è necessario avere il _ruolo Lettore._

#### <a name="list-storage-account-keys"></a>Ottenere chiavi degli account di archiviazione

Storage Explorer usare le chiavi dell'account per autenticare le richieste. È possibile ottenere l'accesso alle chiavi dell'account tramite ruoli più potenti, ad esempio il _ruolo Collaboratore._

> [!NOTE]
> Le chiavi di accesso concedono autorizzazioni senza restrizioni a chiunque le concedi. Pertanto, non è consigliabile hand out queste chiavi per gli utenti dell'account. Se è necessario revocare le chiavi di accesso, è possibile rigenerarle dal portale di Azure [.](https://portal.azure.com/)

#### <a name="data-roles"></a>Ruoli dei dati

È necessario assegnare almeno un ruolo che concede l'accesso alla lettura dei dati dalle risorse. Ad esempio, se si vuole elencare o scaricare i BLOB, è necessario almeno il ruolo Lettore dati BLOB _di_ archiviazione.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Perché è necessario un ruolo del livello di gestione per visualizzare le risorse in Storage Explorer?

Archiviazione di Azure dispone di due livelli di accesso: _gestione_ e _dati._ Le sottoscrizioni e gli account di archiviazione sono accessibili tramite il livello di gestione. È possibile accedere a contenitori, BLOB e altre risorse dati tramite il livello dati. Ad esempio, se si vuole ottenere un elenco degli account di archiviazione da Azure, inviare una richiesta all'endpoint di gestione. Se si vuole un elenco di contenitori BLOB in un account, inviare una richiesta all'endpoint di servizio appropriato.

I ruoli di Azure possono concedere le autorizzazioni per la gestione o l'accesso al livello dati. Il ruolo Lettore, ad esempio, concede l'accesso in sola lettura alle risorse del livello di gestione.

In senso stretto, il ruolo Lettore non fornisce autorizzazioni per il livello dati e non è necessario per accedere al livello dati.

Storage Explorer semplifica l'accesso alle risorse raccogliendo le informazioni necessarie per connettersi alle risorse di Azure. Ad esempio, per visualizzare i contenitori BLOB, Storage Explorer invia una richiesta "elenca contenitori" all'endpoint del servizio BLOB. Per ottenere tale endpoint, Storage Explorer ricerca nell'elenco delle sottoscrizioni e degli account di archiviazione a cui si ha accesso. Per trovare le sottoscrizioni e gli account di archiviazione, Storage Explorer necessario anche l'accesso al livello di gestione.

Se non si ha un ruolo che concede autorizzazioni al livello di gestione, Storage Explorer non può ottenere le informazioni necessarie per connettersi al livello dati.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Cosa succede se non è possibile ottenere le autorizzazioni del livello di gestione necessarie dall'amministratore?

Se si vuole accedere a contenitori BLOB, ADLS Gen2 contenitori, directory o code, è possibile connettersi a tali risorse usando le credenziali di Azure.

1. Aprire la finestra di dialogo Connetti.
1. Selezionare il tipo di risorsa a cui ci si vuole connettere.
1. Selezionare **Accedi con Azure Active Directory (Azure AD)**. Selezionare **Avanti**.
1. Selezionare l'account utente e il tenant associati alla risorsa a cui ci si connette. Selezionare **Avanti**.
1. Immettere l'URL della risorsa e un nome visualizzato univoco per la connessione. Selezionare **Avanti** e quindi **Connetti.**

Per altri tipi di risorse, non è attualmente disponibile una soluzione correlata al controllo degli accessi in base al ruolo di Azure. Come soluzione alternativa, è possibile richiedere un URL di firma di accesso condiviso e quindi collegarsi alla risorsa seguendo questa procedura:

1. Aprire la finestra di dialogo Connetti.
1. Selezionare il tipo di risorsa a cui ci si vuole connettere.
1. Selezionare **Firma di accesso condiviso .** Selezionare **Avanti**.
1. Immettere l'URL di firma di accesso condiviso ricevuto e immettere un nome visualizzato univoco per la connessione. Selezionare **Avanti** e quindi **Connetti.**
 
Per altre informazioni sul collegamento alle risorse, vedere [Connettersi a una singola risorsa.](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)

### <a name="recommended-azure-built-in-roles"></a>Ruoli predefiniti di Azure consigliati

Esistono diversi ruoli predefiniti di Azure che possono fornire le autorizzazioni necessarie per usare Storage Explorer. Alcuni di questi ruoli sono:
- [Proprietario:](../../role-based-access-control/built-in-roles.md#owner)consente di gestire tutti gli elementi, incluso l'accesso alle risorse.
- [Collaboratore:](../../role-based-access-control/built-in-roles.md#contributor)consente di gestire tutti gli elementi, escluso l'accesso alle risorse.
- [Lettore:](../../role-based-access-control/built-in-roles.md#reader)legge ed elenca le risorse.
- [Collaboratore account di archiviazione:](../../role-based-access-control/built-in-roles.md#storage-account-contributor)gestione completa degli account di archiviazione.
- [Proprietario dei dati del BLOB di](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)archiviazione: accesso completo Archiviazione di Azure contenitori BLOB e dati.
- [Collaboratore ai dati dei BLOB di](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)archiviazione: consente di leggere, scrivere ed eliminare Archiviazione di Azure contenitori e BLOB.
- [Lettore di dati del BLOB di](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)archiviazione: legge ed elenca Archiviazione di Azure contenitori e BLOB.

> [!NOTE]
> I ruoli Proprietario, Collaboratore e Collaboratore account di archiviazione concedono l'accesso alla chiave dell'account.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Errore: Certificato autofirmato nella catena di certificati (ed errori simili)

Gli errori del certificato si verificano in genere in una delle situazioni seguenti:

- L'app è connessa tramite _un proxy trasparente._ Ciò significa che un server, ad esempio il server aziendale, intercetta il traffico HTTPS, lo decrittografa e quindi lo crittografa usando un certificato autofirmato.
- Si sta eseguendo un'applicazione che inserisce un certificato TLS/SSL autofirmato nei messaggi HTTPS ricevuti. Esempi di applicazioni che inserisce certificati includono software antivirus e di ispezione del traffico di rete.

Quando Storage Explorer un certificato autofirmato o non attendibile, non sa più se il messaggio HTTPS ricevuto è stato modificato. Se si dispone di una copia del certificato autofirmato, è possibile indicare Storage Explorer di considerarlo attendibile seguendo questa procedura:

1. Ottenere una copia X.509 (.cer) con codifica Base 64 del certificato.
2. Passare a **Modifica** certificati SSL Importa certificati Certificati e quindi usare la selezione file per  >    >  trovare, selezionare e aprire il file cer.

Questo problema può verificarsi anche se sono presenti più certificati (radice e intermedio). Per correggere l'errore, è necessario aggiungere entrambi i certificati.

Se non si è certi della posizione da cui proviene il certificato, seguire questa procedura per trovarlo:

1. Installare OpenSSL.
    * [Windows:](https://slproweb.com/products/Win32OpenSSL.html)tutte le versioni light dovrebbero essere sufficienti.
    * Mac e Linux: devono essere inclusi nel sistema operativo.
2. Eseguire OpenSSL.
    * Windows: aprire la directory di installazione, **selezionare /bin/** e quindi fare doppio clic su **openssl.exe**.
    * Mac e Linux: eseguire `openssl` da un terminale.
3. Eseguire `s_client -showcerts -connect microsoft.com:443`.
4. Cercare i certificati autofirmati. Se non si è certi di quali certificati sono autofirmati, prendere nota di tutti gli elementi in cui l'oggetto e `("s:")` `("i:")` l'autorità emittente sono uguali.
5. Quando si trovano certificati autofirmati, copiare e incollare tutti gli elementi da (e incluso) `-----BEGIN CERTIFICATE-----` in un nuovo file con estensione `-----END CERTIFICATE-----` cer.
6. Aprire Storage Explorer e passare a **Modifica certificati**  >  **SSL Importa**  >  **certificati**. Usare quindi la selezione file per trovare, selezionare e aprire i file cer creati.

Se non è possibile trovare certificati autofirmati seguendo questa procedura, contattare Microsoft tramite lo strumento di feedback. È anche possibile aprire Storage Explorer dalla riga di comando usando il `--ignore-certificate-errors` flag . Quando viene aperto con questo flag, Storage Explorer gli errori del certificato.

## <a name="sign-in-issues"></a>Problemi di accesso

### <a name="understanding-sign-in"></a>Informazioni sull'accesso

Assicurarsi di aver letto la documentazione [accedere a Storage Explorer.](./storage-explorer-sign-in.md)

### <a name="frequently-having-to-reenter-credentials"></a>Spesso è necessario reimerare le credenziali

La necessità di reimmissire le credenziali è probabilmente il risultato dei criteri di accesso condizionale impostati dall'amministratore di AAD. Quando Storage Explorer le credenziali vengono nuovamente specificate nel pannello dell'account, verrà visualizzato un collegamento **Dettagli errore.** Fare clic su questo pulsante per Storage Explorer le credenziali. Gli errori dei criteri di accesso condizionale che richiedono il reentering delle credenziali possono avere un aspetto simile al seguente:
- Il token di aggiornamento è scaduto...
- È necessario usare l'autenticazione a più fattori per accedere...
- A causa di una modifica della configurazione apportata dall'amministratore...

Per ridurre la frequenza con cui è necessario reimporsi le credenziali a causa di errori come quelli elencati in precedenza, è necessario contattare l'amministratore di AAD.

### <a name="conditional-access-policies"></a>Criteri di accesso condizionale

Se si dispone di criteri di accesso condizionale che devono essere soddisfatti per l'account, assicurarsi di usare il valore **Browser Web** predefinito per l'impostazione **Accedi** con. Per informazioni su tale impostazione, vedere [Modifica della posizione di accesso.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Non è possibile acquisire il token, il tenant è filtrato

Se viene visualizzato un messaggio di errore che indica che non è possibile acquisire un token perché un tenant è filtrato, significa che si sta tentando di accedere a una risorsa che si trova in un tenant filtrato. Per rimuovere il filtro del tenant, passare al pannello **account** e verificare che la casella di controllo per il tenant specificato nell'errore sia selezionata. Per altre informazioni sul filtro dei tenant [in Storage Explorer.](./storage-explorer-sign-in.md#managing-accounts)

## <a name="authentication-library-failed-to-start-properly"></a>La libreria di autenticazione non è stata avviata correttamente

Se all'avvio viene visualizzato un messaggio di errore che indica che la libreria di autenticazione di Storage Explorer non è stata avviata correttamente, assicurarsi che l'ambiente di installazione soddisfi tutti [i prerequisiti](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites). Non soddisfare i prerequisiti è la causa più probabile di questo messaggio di errore.

Se si ritiene che l'ambiente di installazione soddisfi tutti i prerequisiti, [aprire un problema in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues/new) Quando si apre il problema, assicurarsi di includere:
- Il sistema operativo.
- Quale versione Storage Explorer che si sta tentando di usare.
- Se sono stati verificati i prerequisiti.
- [Log di autenticazione](#authentication-logs) da un avvio non riuscito di Storage Explorer. La registrazione dettagliata dell'autenticazione viene abilitata automaticamente dopo questo tipo di errore.

### <a name="blank-window-when-using-integrated-sign-in"></a>Finestra vuota quando si usa l'accesso integrato

Se si è  scelto di usare l'accesso integrato e viene visualizzata una finestra di accesso vuota, è probabile che sia necessario passare a un metodo di accesso diverso. Le finestre di dialogo di accesso vuote si verificano più spesso quando un server Active Directory Federation Services (ADFS) richiede Storage Explorer di eseguire un reindirizzamento non supportato da Electron.

Per passare a un metodo di  accesso diverso, modificare l'impostazione Accedi con in **Impostazioni**  >  **Accesso**  >  **applicazione**. Per informazioni sui diversi tipi di metodi di accesso, vedere [Modifica della posizione di accesso.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="reauthentication-loop-or-upn-change"></a>Ciclo di riautenticazione o modifica UPN

Se si è in un ciclo di riautenticazione o è stato modificato l'UPN di uno degli account, provare a seguire questa procedura:

1. Aprire Storage Explorer
2. Passare alla Guida per > reimpostazione
3. Assicurarsi che sia selezionata almeno l'opzione Autenticazione. È possibile deselezionare altri elementi che non si desidera reimpostare.
4. Fare clic sul pulsante Reimposta
5. Riavviare Storage Explorer e riprovare ad accedere.

Se si verificano ancora problemi dopo la reimpostazione, provare a seguire questa procedura:

1. Aprire Storage Explorer
2. Rimuovere tutti gli account e quindi chiudere Storage Explorer.
3. Eliminare la `.IdentityService` cartella dal computer. In Windows, la cartella si trova in `C:\users\<username>\AppData\Local`. Per Mac e Linux, è possibile trovare la cartella nella radice della directory dell'utente.
4. Se si esegue Mac o Linux, è anche necessario eliminare la voce Microsoft.Developer.IdentityService dall'archivio chiavi del sistema operativo. Nel Mac l'archivio chiavi è *l'applicazione Keychain Gnome.* In Linux l'applicazione è in genere denominata _Keyring,_ ma il nome potrebbe variare a seconda della distribuzione.
6. Riavviare Storage Explorer e riprovare ad accedere.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: errori keychain o nessuna finestra di accesso

Il portachiavi macOS può talvolta entrare in uno stato che causa problemi per la Storage Explorer di autenticazione. Per uscire da questo stato per keychain, seguire questa procedura:

1. Chiudere Azure Storage Explorer.
2. Aprire Keychain (premere COMANDO+BARRA SPAZIATRICE, digitare **keychain** e premere INVIO).
3. Selezionare il portachiavi "login".
4. Selezionare l'icona del lucchetto per bloccare il portachiavi. Il lucchetto apparirà bloccato al termine del processo. L'operazione potrebbe richiedere alcuni secondi, a seconda delle app aperte.

    ![Icona del lucchetto](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Aprire Esplora archivi.
6. Viene visualizzato un messaggio simile al seguente: "L'hub del servizio vuole accedere al portachiavi". Immettere la password dell'account amministratore Mac e selezionare **Consenti sempre** **(o** Consenti se **Always Allow** non è disponibile).
7. Provare a effettuare l'accesso.

### <a name="default-browser-doesnt-open"></a>Il browser predefinito non si apre

Se il browser predefinito non si apre quando si tenta di accedere, provare tutte le tecniche seguenti:
- Riavviare Storage Explorer
- Aprire il browser manualmente prima di avviare l'accesso
- Provare a **usare l'accesso integrato,** vedere [Modifica della](./storage-explorer-sign-in.md#changing-where-sign-in-happens) posizione di accesso per istruzioni su come eseguire questa operazione.

### <a name="other-sign-in-issues"></a>Altri problemi di accesso

Se nessuno dei precedenti si applica al problema di accesso o se non riesce a risolvere il problema di accesso, aprire un problema [in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Sottoscrizioni mancanti e tenant interrotti

Se non è possibile recuperare le sottoscrizioni dopo aver eseguito l'accesso, provare i metodi di risoluzione dei problemi seguenti:

* Verificare che l'account abbia accesso alle sottoscrizioni previste. È possibile verificare l'accesso accedendo al portale per l'ambiente di Azure che si sta tentando di usare.
* Assicurarsi di aver eseguito l'accesso tramite l'ambiente di Azure corretto (Azure, Azure China (21Vianet), Azure Germania, Azure US Government o Ambiente personalizzato).
* Se si è dietro un server proxy, assicurarsi di aver configurato correttamente il proxy Storage Explorer proxy.
* Provare a rimuovere e aggiungere nuovamente l'account.
* Se è presente un collegamento "Altre informazioni" o "Dettagli errore", controllare quali messaggi di errore vengono segnalati per i tenant che hanno esito negativo. Se non si è certi di come rispondere ai messaggi di errore, è possibile aprire un [problema in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Non è possibile rimuovere un account di archiviazione collegato o una risorsa

Se non è possibile rimuovere un account collegato o una risorsa di archiviazione tramite l'interfaccia utente, è possibile eliminare manualmente tutte le risorse collegate eliminando le cartelle seguenti:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Chiudere Storage Explorer prima di eliminare queste cartelle.

> [!NOTE]
> Se sono stati importati certificati SSL, eseguire il backup del contenuto della `certs` directory. In un secondo momento, è possibile usare il backup per reimportare i certificati SSL.

## <a name="proxy-issues"></a>Problemi di proxy

Storage Explorer supporta la connessione Archiviazione di Azure risorse tramite un server proxy. Se si verificano problemi di connessione ad Azure tramite proxy, ecco alcuni suggerimenti.

> [!NOTE]
> Storage Explorer supporta solo l'autenticazione di base con server proxy. Altri metodi di autenticazione, ad esempio NTLM, non sono supportati.

> [!NOTE]
> Storage Explorer non supporta i file di configurazione automatica del proxy per la configurazione delle impostazioni proxy.

### <a name="verify-storage-explorer-proxy-settings"></a>Verificare le Storage Explorer proxy

**L'impostazione → proxy → proxy** applicazione determina da quale origine Storage Explorer ottiene la configurazione del proxy.

Se si seleziona "Usa variabili di ambiente", assicurarsi di impostare le variabili di ambiente o ( le variabili di ambiente fanno distinzione tra maiuscole e minuscole, quindi assicurarsi di `HTTPS_PROXY` `HTTP_PROXY` impostare le variabili corrette). Se queste variabili non sono definite o non sono valide, Storage Explorer non userà un proxy. Riavviare Storage Explorer dopo aver modificato le variabili di ambiente.

Se si seleziona "Usa impostazioni proxy app", assicurarsi che le impostazioni del proxy in-app siano corrette.

### <a name="steps-for-diagnosing-issues"></a>Passaggi per la diagnosi dei problemi

Se si verificano ancora problemi, provare questi metodi di risoluzione dei problemi:

1. Se è possibile connettersi a Internet senza usare il proxy, verificare che Storage Explorer funzioni senza le impostazioni proxy abilitate. Se Storage Explorer si connette correttamente, è possibile che si sia verificato un problema con il server proxy. Collaborare con l'amministratore per identificare i problemi.
2. Verificare che le altre applicazioni che usano il server proxy funzionino come previsto.
3. Verificare che sia possibile connettersi al portale per l'ambiente di Azure che si sta tentando di usare.
4. Verificare di poter ricevere le risposte dagli endpoint del servizio. Immettere uno degli URL dell'endpoint nel browser. Se è possibile connettersi, si dovrebbe ricevere una `InvalidQueryParameterValue` risposta XML o simile.
5. Controllare se un altro utente che Storage Explorer con lo stesso server proxy può connettersi. Se possibile, potrebbe essere necessario contattare l'amministratore del server proxy.

### <a name="tools-for-diagnosing-issues"></a>Strumenti per la diagnosi dei problemi

Uno strumento di rete, ad esempio Fiddler, consente di diagnosticare i problemi.

1. Configurare lo strumento di rete come server proxy in esecuzione nell'host locale. Se è necessario continuare a lavorare dietro un proxy effettivo, potrebbe essere necessario configurare lo strumento di rete per la connessione tramite il proxy.
2. Controllare il numero della porta usato dallo strumento di rete.
3. Configurare Storage Explorer proxy per usare l'host locale e il numero di porta dello strumento di rete, ad esempio "localhost:8888".
 
Se impostato correttamente, lo strumento di rete registra le richieste di rete effettuate Storage Explorer agli endpoint di gestione e di servizio.
 
Se lo strumento di rete non sembra registrare il traffico Storage Explorer, provare a testare lo strumento con un'applicazione diversa. Ad esempio, immettere l'URL dell'endpoint per una delle risorse di archiviazione (ad esempio ) in un Web browser e si riceverà `https://contoso.blob.core.windows.net/` una risposta simile alla seguente:

  ![Esempio di codice](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  La risposta suggerisce che la risorsa esiste, anche se non è possibile accedervi.

Se lo strumento di rete mostra solo il traffico proveniente da altre applicazioni, potrebbe essere necessario modificare le impostazioni proxy in Storage Explorer. In caso contrario, è necessario modificare le impostazioni dello strumento.

### <a name="contact-proxy-server-admin"></a>Contattare l'amministratore del server proxy

Se le impostazioni proxy sono corrette, potrebbe essere necessario contattare l'amministratore del server proxy per:

* Assicurarsi che il proxy non blocchi il traffico verso la gestione di Azure o gli endpoint delle risorse.
* Verificare il protocollo di autenticazione usato dal server proxy. Storage Explorer supporta solo i protocolli di autenticazione di base. Storage Explorer non supporta i proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Messaggio di errore "Unable to Retrieve Children" (Impossibile recuperare gli elementi figlio)

Se si è connessi ad Azure tramite un proxy, verificare che le impostazioni del proxy siano corrette.

Se il proprietario di una sottoscrizione o di un account ha concesso l'accesso a una risorsa, verificare di disporre delle autorizzazioni di lettura o elenco per tale risorsa.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>La stringa di connessione non ha impostazioni di configurazione complete

Se viene visualizzato questo messaggio di errore, è possibile che non si abbia le autorizzazioni necessarie per ottenere le chiavi per l'account di archiviazione. Per verificare che questo sia il caso, passare al portale e individuare l'account di archiviazione. A tale scopo, fare clic con il pulsante destro del mouse sul nodo dell'account di archiviazione e **scegliere Apri nel portale.** Passare quindi al pannello **Chiavi di** accesso. Se non si hanno le autorizzazioni per visualizzare le chiavi, verrà visualizzato il messaggio "Non si ha accesso". Per risolvere questo problema, è possibile ottenere la chiave dell'account da un altro utente e collegarla tramite il nome e la chiave oppure chiedere una firma di accesso condiviso all'account di archiviazione e usarla per collegare l'account di archiviazione.

Se vengono visualizzati i codici dell'account, determinare un problema in GitHub per consentire la risoluzione del problema.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Errore durante l'aggiunta di una nuova connessione: TypeError: Impossibile leggere la proprietà 'version' di undefined

Se questo messaggio di errore viene visualizzato quando si tenta di aggiungere una connessione personalizzata, i dati di connessione archiviati nella gestione credenziali locale potrebbero essere danneggiati. Per risolvere questo problema, provare a eliminare le connessioni locali danneggiate e quindi aggiungerle di nuovo:

1. Avviare Storage Explorer. Dal menu passare a **Attiva/Disattiva**  >  **Strumenti di sviluppo**.
2. Nella scheda Applicazione della  finestra aperta passare ad Archiviazione locale **(lato** sinistro) > **file://**.
3. A seconda del tipo di connessione con cui si verifica un problema, cercarne la chiave e quindi copiarne il valore in un editor di testo. Il valore è una matrice dei nomi di connessione personalizzati, come il seguente:
    * Account di archiviazione
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Contenitori BLOB
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Condivisioni file
        * `StorageExplorer_CustomConnections_Files_v1`
    * Code
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabelle
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Dopo aver salvato i nomi di connessione correnti, impostare il valore in Strumenti di sviluppo su `[]` .

Se si vogliono mantenere le connessioni non danneggiate, è possibile usare la procedura seguente per individuare le connessioni danneggiate. Se non si desidera perdere tutte le connessioni esistenti, è possibile ignorare questi passaggi e seguire le istruzioni specifiche della piattaforma per cancellare i dati di connessione.

1. Da un editor di testo aggiungere nuovamente ogni nome di connessione Strumenti di sviluppo e quindi verificare se la connessione è ancora in uso.
2. Se una connessione funziona correttamente, non è danneggiata ed è possibile lasciarla in modo sicuro. Se una connessione non funziona, rimuoverne il valore Strumenti di sviluppo e registrarla in modo da poterla aggiungere di nuovo in un secondo momento.
3. Ripetere l'operazione fino a quando non sono state esaminate tutte le connessioni.

Dopo aver passato tutte le connessioni, per tutti i nomi delle connessioni che non vengono aggiunti di nuovo, è necessario cancellare i dati danneggiati (se presenti) e aggiungerli di nuovo usando i passaggi standard in Storage Explorer:

### <a name="windows"></a>[Windows](#tab/Windows)

1. Nel menu **Start** cercare Gestione credenziali **e** aprirlo.
2. Passare a **Credenziali di Windows**.
3. In **Credenziali generiche** cercare le voci che hanno la `<connection_type_key>/<corrupted_connection_name>` chiave , ad esempio `StorageExplorer_CustomConnections_Accounts_v1/account1` .
4. Eliminare queste voci e aggiungere di nuovo le connessioni.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Aprire Spotlight (COMANDO+BARRA SPAZIATRICE) e cercare **Accesso keychain.**
2. Cercare le voci che hanno la `<connection_type_key>/<corrupted_connection_name>` chiave (ad esempio, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Eliminare queste voci e aggiungere di nuovo le connessioni.

### <a name="linux"></a>[Linux](#tab/Linux)

La gestione delle credenziali locali varia a seconda della distribuzione di Linux. Se la distribuzione linux non fornisce uno strumento GUI incorporato per la gestione delle credenziali locale, è possibile installare uno strumento di terze parti per gestire le credenziali locali. Ad esempio, è possibile usare [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), uno strumento gui open source per la gestione delle credenziali locali Linux.

1. Aprire lo strumento di gestione delle credenziali locale e trovare le credenziali salvate.
2. Cercare le voci che hanno la `<connection_type_key>/<corrupted_connection_name>` chiave (ad esempio, `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Eliminare queste voci e aggiungere di nuovo le connessioni.
---

Se si verifica ancora questo errore dopo aver eseguito questi passaggi o se si vuole condividere ciò che si sospetta abbia danneggiato le [connessioni,](https://github.com/microsoft/AzureStorageExplorer/issues) aprire un problema nella pagina GitHub.

## <a name="issues-with-sas-url"></a>Problemi relativi all'URL SAS

Se ci si connette a un servizio tramite un URL di firma di accesso condiviso e si verifica un errore:

* Verificare che l'URL abbia le autorizzazioni necessarie per la lettura o l'elenco delle risorse.
* Verificare che l'URL non sia scaduto.
* Se l'URL SAS si basa su un criterio di accesso, verificare che i criteri di accesso non siano stati revocati.

Se si è collegato accidentalmente usando un URL di firma di accesso condiviso non valido e ora non è possibile scollegarsi, seguire questa procedura:

1. Quando si esegue Storage Explorer, premere F12 per aprire la finestra Strumenti di sviluppo.
2. Nella scheda **Applicazione** selezionare Archiviazione locale **file://**  >   nell'albero a sinistra.
3. Trovare la chiave associata al tipo di servizio dell'URI SAS che ha generato il problema. Ad esempio, se l'URI SAS non valido fa riferimento a un contenitore BLOB, cercare la chiave denominata `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Il valore della chiave deve essere una matrice JSON. Trovare l'oggetto associato all'URI non valido e quindi eliminarlo.
5. Premere CTRL + R per ricaricare Storage Explorer.

## <a name="linux-dependencies"></a>Dipendenze Linux

### <a name="snap"></a>Snap

Storage Explorer 1.10.0 e versioni successive è disponibile come snap da Snap Store. Lo snap Storage Explorer installa automaticamente tutte le relative dipendenze e viene aggiornato quando è disponibile una nuova versione dello snap. L'installazione Storage Explorer snap è il metodo di installazione consigliato.

Storage Explorer richiede l'uso di uno strumento di gestione delle password, che potrebbe essere necessario connettersi manualmente prima Storage Explorer funzioni correttamente. È possibile connettersi Storage Explorer al gestore delle password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>File con estensione tar.gz

È anche possibile scaricare l'applicazione come file con estensione tar.gz, ma è necessario installare manualmente le dipendenze.

Storage Explorer come indicato nel download con estensione tar.gz è supportato solo per le versioni seguenti di Ubuntu. Storage Explorer potrebbero funzionare in altre distribuzioni Linux, ma non sono ufficialmente supportate.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer richiede l'installazione di .NET Core nel sistema. È consigliabile usare .NET Core 2.1, ma Storage Explorer funzionerà anche con la versione 2.2.

> [!NOTE]
> Storage Explorer versione 1.7.0 e precedenti richiedono .NET Core 2.0. Se è installata una versione più recente di .NET Core, è necessario applicare [patch Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Se si esegue Storage Explorer 1.8.0 o versione successiva, è necessario almeno .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Scaricare il Storage Explorer file con estensione tar.gz.
2. Installare [il runtime di .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Scaricare il Storage Explorer file con estensione tar.gz.
2. Installare [il runtime di .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Scaricare il Storage Explorer file con estensione tar.gz.
2. Installare [il runtime di .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Molte librerie necessarie per Storage Explorer preinstallate con le installazioni standard di Canonical di Ubuntu. Alcune di queste librerie potrebbero mancare negli ambienti personalizzati. In caso di problemi di Storage Explorer, è consigliabile assicurarsi che nel sistema siano installati i pacchetti seguenti:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Applicazione di Storage Explorer patch per le versioni più recenti di .NET Core

Per Storage Explorer 1.7.0 o versioni precedenti, potrebbe essere necessario applicare patch alla versione di .NET Core usata da Storage Explorer:

1. Scaricare la versione 1.5.43 di StreamJsonRpc [da NuGet.](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) Cercare il collegamento "Scarica pacchetto" sul lato destro della pagina.
2. Dopo aver scaricato il pacchetto, modificarne l'estensione da `.nupkg` a `.zip` .
3. Decomprimere il pacchetto.
4. Aprire la cartella `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Copiare `StreamJsonRpc.dll` nei percorsi seguenti nella cartella Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Apri in Esplora risorse" dal portale di Azure non funziona

Se il **pulsante Apri in Esplora** risorse portale di Azure non funziona, assicurarsi di usare un browser compatibile. I browser seguenti sono stati testati per la compatibilità:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Raccolta di log

Quando si segnala un problema a GitHub, potrebbe essere richiesto di raccogliere alcuni log per diagnosticare il problema.

### <a name="storage-explorer-logs"></a>Storage Explorer log

A partire dalla versione 1.16.0, Storage Explorer registra vari elementi nei propri log applicazioni. È possibile accedere facilmente a questi log facendo clic su Guida > Apri directory log. Per impostazione predefinita, Storage Explorer log a un basso livello di dettaglio. Per modificare il livello di dettaglio, aggiungere una variabile di ambiente con il nome `STG_EX_LOG_LEVEL` e uno dei valori seguenti:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (livello predefinito)
- `verbose`
- `debug`

I log vengono suddivisi in cartelle per ogni sessione Storage Explorer esecuzione. Per tutti i file di log che è necessario condividere, è consigliabile posizionarli in un archivio ZIP, con file di sessioni diverse in cartelle diverse.

### <a name="authentication-logs"></a>Log di autenticazione

Per i problemi relativi all'accesso o alla Storage Explorer di autenticazione di Storage Explorer, è molto probabile che sia necessario raccogliere i log di autenticazione. I log di autenticazione vengono archiviati in:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS e Linux `~/.ServiceHub/logs`

In genere, è possibile seguire questa procedura per raccogliere i log:

1. Passare a Impostazioni > accesso > registrazione dettagliata dell'autenticazione. Se Storage Explorer l'avvio non riesce a causa di un problema con la libreria di autenticazione, questa operazione verrà eseguita per l'utente.
2. Chiudere Azure Storage Explorer.
1. Facoltativo/consigliato: cancellare i log esistenti dalla `logs` cartella. In questo modo si ridurrà la quantità di informazioni che è necessario inviare.
4. Aprire Storage Explorer e riprodurre il problema
5. Chiudere Storage Explorer
6. Comprimere il contenuto della `log` cartella.

### <a name="azcopy-logs"></a>Log di AzCopy

Se si verificano problemi durante il trasferimento dei dati, potrebbe essere necessario ottenere i log di AzCopy. I log di AzCopy possono essere trovati facilmente tramite due metodi diversi:
- Per i trasferimenti non riusciti ancora nel log attività, fare clic su "Vai al file di log AzCopy"
- Per i trasferimenti non riusciti in passato, passare alla cartella log AzCopy. Questa cartella è disponibile all'indirizzo:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS e Linux '~/.azcopy

### <a name="network-logs"></a>Log di rete

Per alcuni problemi è necessario fornire i log delle chiamate di rete effettuate da Storage Explorer. In Windows è possibile eseguire questa operazione usando Fiddler.

> [!NOTE]
> Le tracce di Fiddler possono contenere password immesse/inviate nel browser durante la raccolta della traccia. Assicurarsi di leggere le istruzioni su come disinfettare una traccia di Fiddler. Non caricare tracce di Fiddler in GitHub. Verrà spiegato dove è possibile inviare in modo sicuro la traccia di Fiddler.

Parte 1: Installare e configurare Fiddler

1. Installare Fiddler
2. Avviare Fiddler
3. Passare a Strumenti > opzioni
4. Fare clic sulla scheda HTTPS
5. Assicurarsi che l'opzione Capture CONNECTs and Decrypt HTTPS traffic (Acquisisci CONNESSIONI e decrittografa il traffico HTTPS) sia selezionata
6. Fare clic sul pulsante Azioni
7. Scegliere "Trust Root Certificate" (Certificato radice attendibile) e quindi "Yes" (Sì) nella finestra di dialogo successiva
8. Fare di nuovo clic sul pulsante Azioni
9. Scegliere "Esporta certificato radice sul desktop"
10. Passare al desktop
11. Trovare il file FiddlerRoot.cer
12. Fare doppio clic per aprire
13. Passare alla scheda "Dettagli"
14. Fare clic su "Copia nel file..."
15. Nell'esportazione guidata scegliere le opzioni seguenti
    - Codificato in base 64 X.509
    - Per nome file, Sfoglia... in C:\Users \<your user dir> \AppData\Roaming\StorageExplorer\certs ed è quindi possibile salvarlo con qualsiasi nome file
16. Chiudere la finestra del certificato
17. Avviare Storage Explorer
18. Passare a Modifica > Configura proxy
19. Nella finestra di dialogo scegliere "Usa impostazioni proxy app" e impostare l'URL su http://localhost e la porta su 8888
20. Fare clic su OK.
21. Riavviare Storage Explorer
22. È consigliabile iniziare a visualizzare le chiamate di rete da `storageexplorer:` un processo in Fiddler

Parte 2: Riprodurre il problema
1. Chiudere tutte le app diverse da Fiddler
2. Cancellare il log di Fiddler (icona X in alto a sinistra, vicino al menu Visualizza)
3. Facoltativo/consigliato: consentire a Fiddler di impostare per alcuni minuti, se vengono visualizzate le chiamate di rete, fare clic con il pulsante destro del mouse su di esse e scegliere "Filtra ora" > <process name> 'Nascondi'
4. Avviare Storage Explorer
5. Riprodurre il problema
6. Fare clic su File > Salva > tutte le sessioni, salvare in un punto da non dimenticare
7. Chiudere Fiddler e Storage Explorer

Parte 3: Sanitize the Fiddler trace
1. Fare doppio clic sulla traccia fiddler (file con estensione saz)
2. passeggiata `ctrl`+`f`
3. Nella finestra di dialogo visualizzata verificare che siano impostate le opzioni seguenti: Search = Requests and responses (Ricerca = Richieste e risposte), Examine = Headers and bodies (Esaminare = intestazioni e corpi)
4. Cercare le password usate durante la raccolta della traccia di fiddler, le voci evidenziate, fare clic con il pulsante destro del mouse e scegliere Rimuovi > sessioni selezionate
5. Se sono state immesse password nel browser durante la raccolta della traccia, ma non si trovano voci quando si usa CTRL+F e non si vogliono modificare le password usate per altri account, è possibile ignorare l'invio del file con estensione saz. È meglio essere sicuri che non essere spiacenti. :)
6. Salvare di nuovo la traccia con un nuovo nome
7. Facoltativo: eliminare la traccia originale

## <a name="next-steps"></a>Passaggi successivi

Se nessuna di queste soluzioni funziona correttamente, è possibile:
- Creare un ticket di supporto
- [Aprire un problema in GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues) A tale scopo, è anche possibile selezionare il pulsante Segnala il problema a **GitHub** nell'angolo in basso a sinistra.

![Commenti e suggerimenti](./media/storage-explorer-troubleshooting/feedback-button.PNG)