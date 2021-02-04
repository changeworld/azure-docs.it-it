---
title: Risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory
description: Informazioni su come risolvere i problemi relativi al runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 3735d7b21faf62905ed4c06dbef80c9737e7329e
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551077"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Risolvere i problemi relativi al runtime di integrazione self-hosted

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per il runtime di integrazione self-hosted (IR) in Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Raccogli log IR indipendenti da Azure Data Factory

Per le attività non riuscite eseguite in un runtime di integrazione self-hosted o in un runtime di integrazione condiviso, Azure Data Factory supporta la visualizzazione e il caricamento dei log degli errori. Per ottenere l'ID della segnalazione errori, seguire le istruzioni riportate qui, quindi immettere l'ID del report per cercare problemi noti correlati.

1. In Data Factory selezionare **esecuzioni pipeline**.

1. In **esecuzioni attività**, nella colonna **errore** , selezionare il pulsante evidenziato per visualizzare i log attività, come illustrato nello screenshot seguente:

    ![Screenshot della sezione "esecuzioni attività" del riquadro "tutte le esecuzioni di pipeline".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    I log attività vengono visualizzati per l'esecuzione dell'attività non riuscita.

    ![Screenshot dei log attività per l'attività non riuscita.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Per ulteriore assistenza, selezionare **Invia log**.
 
   Viene aperta la **condivisione dei log del runtime di integrazione self-hosted (IR) con** la finestra Microsoft.

    ![Screenshot della finestra "condividere i log del runtime di integrazione self-hosted (IR) con Microsoft".](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Consente di selezionare i log che si desidera inviare. 
    * Per un runtime di integrazione *self-hosted*, è possibile caricare i log relativi all'attività non riuscita o a tutti i log nel nodo IR self-hosted. 
    * Per un runtime di integrazione *condiviso*, è possibile caricare solo i log relativi all'attività non riuscita.

1. Quando i log vengono caricati, tenere un record dell'ID del report per un uso successivo se è necessaria ulteriore assistenza per risolvere il problema.

    ![Screenshot dell'ID report visualizzato nella finestra stato caricamento per i log IR.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> La visualizzazione e il caricamento di log vengono eseguite in tutte le istanze del runtime di integrazione self-hosted online. Se sono presenti log mancanti, assicurarsi che tutte le istanze di runtime di integrazione Self-Hosted siano online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Errore generale del runtime di integrazione self-hosted

### <a name="out-of-memory-issue"></a>Problema di memoria insufficiente

#### <a name="symptoms"></a>Sintomi

Si verifica un errore OutOfMemoryException (memoria insufficiente) quando si tenta di eseguire un'attività di ricerca con un IR collegato o un runtime di integrazione self-hosted.

#### <a name="cause"></a>Causa

Una nuova attività può generare un errore di memoria insufficiente se il computer IR sperimenta un utilizzo elevato di memoria temporanea. Il problema potrebbe essere causato da un volume elevato di attività simultanee e l'errore si verifica in base alla progettazione.

#### <a name="resolution"></a>Soluzione

Controllare l'utilizzo delle risorse e l'esecuzione di attività simultanee nel nodo IR. Modificare il tempo interno e il trigger delle esecuzioni di attività per evitare un'esecuzione eccessiva in un singolo nodo IR nello stesso momento.

### <a name="concurrent-jobs-limit-issue"></a>Problemi nelle limiti dei processi simultanei

#### <a name="symptoms"></a>Sintomi

Quando si tenta di aumentare il limite di processi simultanei dall'interfaccia Azure Data Factory, il processo si blocca nello stato di *aggiornamento* .

Scenario di esempio: il valore del numero massimo di processi simultanei è attualmente impostato su 24 e si desidera aumentare il numero in modo che i processi possano essere eseguiti più velocemente. Il valore minimo che è possibile immettere è 3 e il valore massimo è 32. Aumentare il valore da 24 a 32, quindi selezionare il pulsante **Aggiorna** . Il processo viene bloccato nello stato di *aggiornamento* , come illustrato nello screenshot seguente. Si aggiorna la pagina e il valore viene comunque visualizzato come 24. Non è stato aggiornato a 32 come previsto.

![Screenshot del riquadro nodi del runtime di integrazione, che Visualizza il processo bloccato nello stato "aggiornamento".](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

Il limite per il numero di processi simultanei dipende dalla memoria e dal core della logica del computer. Provare a modificare il valore verso il basso in un valore, ad esempio 24, quindi visualizzare il risultato.

> [!TIP] 
> - Per altre informazioni sul conteggio dei core logici e per determinare il numero di core logici del computer, vedere [quattro modi per trovare il numero di core nella CPU in Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Per informazioni su come calcolare Math. log, vedere il [calcolo logaritmico](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Problema relativo al certificato SSL a disponibilità elevata del runtime di integrazione self-hosted

#### <a name="symptoms"></a>Sintomi

Il nodo di lavoro IR self-hosted ha segnalato l'errore seguente:

"Impossibile eseguire il pull degli stati condivisi dal nodo primario NET. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. svc/. ID attività: XXXXX il certificato X. 509 CN = ABC. cloud. Corp. Microsoft. com, OU = Test, O = creazione catena Microsoft non riuscita. Impossibile verificare una catena di trust del certificato utilizzato. Sostituire il certificato o modificare certificateValidationMode. La funzione di revoca non è stata in grado di controllare la revoca perché il server di revoca era offline.

#### <a name="cause"></a>Causa

Quando si gestiscono i case correlati a un handshake SSL/TLS, è possibile che si verifichino alcuni problemi relativi alla verifica della catena di certificati. 

#### <a name="resolution"></a>Soluzione

- Ecco un modo rapido e intuitivo per risolvere un errore di compilazione della catena di certificati X. 509:
 
    1. Esportare il certificato, che deve essere verificato. A tale scopo, seguire questa procedura:
    
       a. In Windows selezionare **Start**, iniziare a digitare **certificati**, quindi selezionare **Gestisci certificati computer**.
       
       b. In Esplora file, nel riquadro sinistro, cercare il certificato che si desidera controllare, fare clic con il pulsante destro del mouse su di esso e quindi selezionare **tutte le attività**  >  **Esporta**.
    
        ![Screenshot del controllo "tutte le attività" > "Esporta" per un certificato nel riquadro "Gestisci certificati computer".](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copiare il certificato esportato nel computer client. 
    3. Sul lato client, in una finestra del prompt dei comandi, eseguire il comando seguente. Assicurarsi di sostituire *\<certificate path>* e *\<output txt file path>* con i percorsi effettivi.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Ad esempio:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Verificare la presenza di errori nel file TXT di output. È possibile trovare il riepilogo degli errori alla fine del file TXT.

        Ad esempio: 

        ![Screenshot di un riepilogo degli errori alla fine del file TXT.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se non viene visualizzato un errore alla fine del file di log, come illustrato nello screenshot seguente, è possibile considerare che la catena di certificati è stata compilata correttamente nel computer client.
        
        ![Screenshot di un file di log che non Mostra errori.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Se nel file di certificato è configurata un'estensione AIA (Authority Information Access), CDP (punto di distribuzione CRL) o OCSP (Online Certificate Status Protocol), è possibile archiviarla in modo più intuitivo:
 
    1. Ottenere queste informazioni controllando i dettagli del certificato, come illustrato nello screenshot seguente:
    
        ![Screenshot dei dettagli del certificato.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Eseguire il comando seguente. Assicurarsi di sostituire *\<certificate path>* con il percorso effettivo del certificato.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Verrà visualizzato lo strumento di recupero URL. 
        
    1. Per verificare i certificati con le estensioni dei nomi di file AIA, CDP e OCSP, selezionare **Recupera**.

        ![Screenshot dello strumento di recupero URL e del pulsante Recupera.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        La catena di certificati è stata compilata correttamente se lo stato del certificato da AIA è *verificato* e lo stato del certificato da CDP o OCSP è *verificato*.

        Se si verifica un errore quando si tenta di recuperare AIA o CDP, collaborare con il team di rete per preparare il computer client per la connessione all'URL di destinazione. Sarà sufficiente se è possibile verificare il percorso HTTP o il percorso LDAP (Lightweight Directory Access Protocol).

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Il runtime di integrazione self-hosted non può caricare il file o l'assembly

#### <a name="symptoms"></a>Sintomi

Viene visualizzato il seguente messaggio di errore:

"Impossibile caricare il file o l'assembly ' XXXXXXXXXXXXXXXX, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' o una delle relative dipendenze. Non è possibile trovare il file specificato. ID attività: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "
 
Di seguito è riportato un messaggio di errore più specifico: 

"Impossibile caricare il file o l'assembly ' System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX ' o una delle relative dipendenze. Non è possibile trovare il file specificato. ID attività: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Causa

In Process Monitor è possibile visualizzare i risultati seguenti:

[![Screenshot dell'elenco di percorsi in Process Monitor.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> In Process Monitor è possibile impostare i filtri come illustrato nello screenshot seguente.
>
> Il messaggio di errore precedente indica che la DLL System. ValueTuple non si trova nella cartella *global assembly cache* (GAC) correlata, nella cartella C:\Programmi\Microsoft *Integration Runtime\4.0\Gateway* o nella cartella c:\Programmi\Microsoft *Integration Runtime\4.0\Shared*
>
> In pratica, il processo carica la DLL prima dalla cartella *GAC* , quindi dalla cartella *condivisa* e infine dalla cartella del *gateway* . Pertanto, è possibile caricare la DLL da qualsiasi percorso utile.

<br>

![Screenshot della pagina "filtro di monitoraggio del processo", che elenca i filtri per la DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Soluzione

Il file di *System.ValueTuple.dll* è presente nella cartella *c:\Programmi\Microsoft Integration Runtime\4.0\Gateway\DataScan* Per risolvere il problema, copiare il file *System.ValueTuple.dll* nella cartella C:\Programmi\Microsoft *Integration Runtime\4.0\Gateway*

È possibile utilizzare lo stesso metodo per risolvere altri problemi di file o assembly mancanti.

#### <a name="more-information-about-this-issue"></a>Ulteriori informazioni su questo problema

Il motivo per cui viene visualizzato il *System.ValueTuple.dll* in *%windir%\Microsoft.NET\assembly* e *%windir%\assembly* è che si tratta di un comportamento .NET. 

Nell'errore seguente è possibile vedere chiaramente che l'assembly *System. ValueTuple* è mancante. Questo problema si verifica quando l'applicazione tenta di controllare l'assembly *System.ValueTuple.dll* .
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," message ":" l'inizializzatore di tipo per ' npgsql. PoolManager ' ha generato un'eccezione. "," EventType ": 0," Category ": 5," data ": {} ," msgid ": null," exceptionType ":" System. TypeInitializationException "," Source ":" npgsql "," StackTrace ":" "," InnerEventInfos ": [{" code ": 0," message ":" Impossibile caricare il file o l'assembly ' System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = xxxxxxxxx ' o una delle relative dipendenze. Il sistema non è in grado di trovare il file specificato. "," EventType ": 0," Category ": 5," data ": {} ," msgid":null,"exceptionType":"System. io. FileNotFoundException "," Source ":" npgsql "," StackTrace ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
Per ulteriori informazioni sulla GAC, vedere [global assembly cache](/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Manca la chiave di autenticazione del runtime di integrazione self-hosted

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted passa improvvisamente offline senza una chiave di autenticazione e il registro eventi Visualizza il messaggio di errore seguente: 

"La chiave di autenticazione non è ancora stata assegnata"

![Screenshot del riquadro eventi di Integration Runtime che indica che la chiave di autenticazione non è ancora assegnata.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- Il nodo IR indipendente o il runtime di integrazione self-hosted logico nel portale di Azure è stato eliminato.
- È stata eseguita una disinstallazione pulita.

#### <a name="resolution"></a>Soluzione

Se nessuna delle cause precedenti si applica, è possibile passare alla cartella *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* per verificare se il file di *configurazione* è stato eliminato. Se è stata eliminata, seguire le istruzioni riportate nell'articolo NetWrix [rilevare chi ha eliminato un file dai file server Windows](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Screenshot del riquadro dei dettagli del registro eventi per controllare il file di configurazione.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Non è possibile usare il runtime di integrazione self-hosted per collegare due archivi dati locali

#### <a name="symptoms"></a>Sintomi

Dopo aver creato l'IRs self-hosted per gli archivi dati di origine e di destinazione, è necessario connettere i due IRs per completare un'attività di copia. Se gli archivi dati sono configurati in reti virtuali diverse oppure gli archivi dati non riescono a comprendere il meccanismo del gateway, si riceve uno degli errori seguenti: 

* "Impossibile trovare il driver dell'origine nell'IR di destinazione"
* "Impossibile accedere all'origine dal runtime di integrazione di destinazione"
 
#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è progettato come nodo centrale di un'attività di copia, non come agente client che deve essere installato per ogni archivio dati.
 
In questo caso, è necessario creare il servizio collegato per ogni archivio dati con lo stesso runtime di integrazione e il runtime di integrazione deve essere in grado di accedere a entrambi gli archivi dati attraverso la rete. Non importa se il runtime di integrazione è installato nell'archivio dati di origine o nell'archivio dati di destinazione o in un terzo computer. Se vengono creati due servizi collegati con un altro IRs ma usati nella stessa attività di copia, viene usato il runtime di integrazione di destinazione ed è necessario installare i driver per entrambi gli archivi dati nel computer IR di destinazione.

#### <a name="resolution"></a>Soluzione

Installare i driver per gli archivi dati di origine e di destinazione nel runtime di integrazione di destinazione e verificare che sia in grado di accedere all'archivio dati di origine.
 
Se il traffico non può passare attraverso la rete tra due archivi dati (ad esempio, sono configurati in due reti virtuali), è possibile che non si completi la copia in un'attività anche con il runtime di integrazione installato. Se non è possibile completare la copia in un'unica attività, è possibile creare due attività di copia con due IRs, ciascuna in uno sfogo: 
* Copiare un runtime di integrazione dall'archivio dati 1 all'archivio BLOB di Azure
* Copiare un altro IR dall'archiviazione BLOB di Azure all'archivio dati 2. 

Questa soluzione potrebbe simulare la necessità di usare il runtime di integrazione per creare un Bridge che connette due archivi dati disconnessi.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Il problema di sincronizzazione delle credenziali causa la perdita delle credenziali da disponibilità elevata

#### <a name="symptoms"></a>Sintomi

Se le credenziali dell'origine dati "XXXXXXXXXX" vengono eliminate dal nodo corrente di Integration Runtime con payload, viene visualizzato il messaggio di errore seguente:

"Quando si elimina il servizio di collegamento in portale di Azure o l'attività ha il payload errato, creare nuovamente il nuovo servizio di collegamento con le credenziali".

#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted è compilato in modalità a disponibilità elevata con due nodi, ma i nodi non sono in uno stato di sincronizzazione delle credenziali. Ciò significa che le credenziali archiviate nel nodo dispatcher non vengono sincronizzate con altri nodi del ruolo di lavoro. Se si verifica un failover dal nodo Dispatcher al nodo di lavoro e le credenziali sono presenti solo nel nodo Dispatcher precedente, l'attività non riuscirà quando si tenta di accedere alle credenziali e si riceverà l'errore precedente.

#### <a name="resolution"></a>Soluzione

L'unico modo per evitare questo problema consiste nel verificare che i due nodi si trovino nello stato di sincronizzazione delle credenziali. Se non sono sincronizzati, è necessario immettere nuovamente le credenziali per il nuovo Dispatcher.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Non è possibile scegliere il certificato perché manca la chiave privata

#### <a name="symptoms"></a>Sintomi

* È stato importato un file PFX nell'archivio certificati.
* Quando si seleziona il certificato tramite l'interfaccia utente di Configuration Manager IR, viene visualizzato il messaggio di errore seguente:

   "Impossibile modificare la modalità di crittografia delle comunicazioni Intranet. È probabile che il certificato ' \<*certificate name*> ' non disponga di una chiave privata idonea per lo scambio delle chiavi o che il processo non disponga dei diritti di accesso per la chiave privata. Per informazioni dettagliate, vedere l'eccezione interna. "

    ![Screenshot del riquadro Impostazioni Integration Runtime Configuration Manager, in cui è visualizzato un messaggio di errore "mancata chiave privata".](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- L'account utente ha un livello di privilegio basso e non può accedere alla chiave privata.
- Il certificato è stato generato come una firma ma non come scambio di chiave.

#### <a name="resolution"></a>Soluzione

* Per utilizzare l'interfaccia utente, utilizzare un account con privilegi appropriati per l'accesso alla chiave privata.  
* Importare il certificato eseguendo il comando seguente:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Configurazione del runtime di integrazione self-hosted

### <a name="integration-runtime-registration-error"></a>Errore di registrazione di Integration Runtime 

#### <a name="symptoms"></a>Sintomi

In alcuni casi potrebbe essere necessario eseguire un runtime di integrazione self-hosted in un account diverso per uno dei seguenti motivi:
- I criteri aziendali non consentono l'account del servizio.
- È richiesta l'autenticazione.

Dopo aver modificato l'account del servizio nel riquadro del servizio, il runtime di integrazione smette di funzionare e viene ricevuto il messaggio di errore seguente:

"Si è verificato un errore nel nodo Integration Runtime (self-hosted) durante la registrazione. Impossibile connettersi al servizio Host Integration Runtime (self-hosted) ".

![Screenshot della finestra di Configuration Manager Integration Runtime che mostra un errore di registrazione IR.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Molte risorse vengono concesse solo all'account del servizio. Quando si modifica l'account del servizio in un altro account, le autorizzazioni di tutte le risorse dipendenti rimangono invariate.

#### <a name="resolution"></a>Soluzione

Per verificare l'errore, passare al registro eventi di Integration Runtime.

![Screenshot del registro eventi IR, che indica che si è verificato un errore di Runtime.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Se l'errore nel registro eventi è "UnauthorizedAccessException", eseguire le operazioni seguenti:

    1. Controllare l'account del servizio di accesso *DIAHostService* nel pannello del servizio Windows.

        ![Screenshot del riquadro delle proprietà dell'account del servizio di accesso.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verificare se l'account del servizio di accesso dispone di autorizzazioni di lettura/scrittura per la cartella *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* .

        - Per impostazione predefinita, se l'account di accesso al servizio non è stato modificato, deve disporre di autorizzazioni di lettura/scrittura.

            ![Screenshot del riquadro autorizzazioni del servizio.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Se è stato modificato l'account di accesso al servizio, attenuare il problema effettuando le operazioni seguenti:
 
            a. Eseguire una disinstallazione pulita del runtime di integrazione self-hosted corrente.   
            b. Installare i bit del runtime di integrazione self-hosted.  
            c. Modificare l'account del servizio effettuando le operazioni seguenti:  

             i. Passare alla cartella di installazione del runtime di integrazione self-hosted e quindi passare alla cartella *Microsoft Integration Runtime\4.0\Shared*  
             ii. Aprire una finestra del prompt dei comandi utilizzando privilegi elevati. Sostituire *\<user>* e *\<password>* con il nome utente e la password personali, quindi eseguire il comando seguente:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Se si desidera passare all'account LocalSystem, assicurarsi di utilizzare il formato corretto per l'account: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                *Non* usare questo formato:`dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Facoltativamente, poiché il sistema locale ha privilegi più elevati rispetto all'amministratore, è anche possibile modificarlo direttamente in "servizi".  
             v. È possibile usare un utente locale/di dominio per l'account di accesso del servizio IR.            

            d. Registrare il runtime di integrazione.

* Se l'errore è "servizio" Integration Runtime servizio "(DIAHostService) non è stato avviato. Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema, "eseguire le operazioni seguenti:

    1. Controllare l'account del servizio di accesso *DIAHostService* nel pannello del servizio Windows.
    
        ![Screenshot del riquadro "Accedi" per l'account del servizio.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verificare che l'account del servizio di accesso disponga dell'autorizzazione **Accedi come servizio** per l'avvio del servizio Windows:

        ![Screenshot del riquadro delle proprietà "accesso come servizio".](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Ulteriori informazioni

Se nessuno dei due modelli di risoluzione precedenti si applica nel caso specifico, provare a raccogliere i registri eventi di Windows seguenti: 
- Registri applicazioni e servizi > Integration Runtime
- Log di Windows > applicazione

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Il pulsante Register per registrare un runtime di integrazione self-hosted non è stato trovato    

#### <a name="symptoms"></a>Sintomi

Quando si registra un runtime di integrazione self-hosted, il pulsante **registra** non viene visualizzato nel riquadro Configuration Manager.

![Screenshot del riquadro Configuration Manager, che visualizza un messaggio che indica che il nodo di Integration Runtime non è registrato.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

Al momento del rilascio di Integration Runtime 3,0, il pulsante **Register** sui nodi di Integration Runtime è stato rimosso per consentire un ambiente più pulito e sicuro. Se un nodo è stato registrato in un runtime di integrazione, se è online o meno, registrarlo di nuovo in un altro runtime di integrazione disinstallando il nodo precedente, quindi installare e registrare il nodo.

#### <a name="resolution"></a>Soluzione

1. Nel pannello di controllo disinstallare il runtime di integrazione esistente.

    > [!IMPORTANT] 
    > Nel processo seguente selezionare **Sì**. Non conserva i dati durante il processo di disinstallazione.

    ![Screenshot del pulsante "Sì" per eliminare tutti i dati utente dal runtime di integrazione.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se non si dispone del file MSI del programma di installazione di Integration Runtime, passare all' [area download](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) per scaricare il runtime di integrazione più recente.
1. Installare il file MSI e registrare il runtime di integrazione.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Impossibile registrare il runtime di integrazione self-hosted a causa di localhost    

#### <a name="symptoms"></a>Sintomi

Non è possibile registrare il runtime di integrazione self-hosted in un nuovo computer quando si usa get_LoopbackIpOrName.

**Debug:** Si è verificato un errore di Runtime.
L'inizializzatore di tipo per "Microsoft.DataTransfer.DIAgentHost.DataSourceCache" ha generato un'eccezione.
Si è verificato un errore irreversibile durante la ricerca nel database.
 
**Dettagli eccezione:** System. TypeInitializationException: l'inizializzatore di tipo per ' Microsoft. datatransfer. DIAgentHost. DataSourceCache ' ha generato un'eccezione. ---> System .NET. Sockets. SocketException: si è verificato un errore irreversibile durante una ricerca nel database in System .NET. DNS. funzione getaddrinfo (nome stringa).

#### <a name="cause"></a>Causa

Il problema si verifica in genere quando è in corso la risoluzione del localhost.

#### <a name="resolution"></a>Soluzione

Usare l'indirizzo IP localhost 127.0.0.1 per ospitare il file e risolvere il problema.

### <a name="self-hosted-setup-failed"></a>Installazione self-hosted non riuscita    

#### <a name="symptoms"></a>Sintomi

Non è possibile disinstallare un runtime di integrazione esistente, installare un nuovo IR o aggiornare un runtime di integrazione esistente a un nuovo IR.

#### <a name="cause"></a>Causa

L'installazione del runtime di integrazione dipende dal servizio Windows Installer. È possibile che si verifichino problemi di installazione per i motivi seguenti:
- Spazio su disco disponibile insufficiente.
- Mancanza di autorizzazioni.
- Il servizio Windows NT è bloccato.
- L'utilizzo della CPU è troppo elevato.
- Il file MSI è ospitato in un percorso di rete lento.
- Alcuni file di sistema o registri sono stati modificati inavvertitamente.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>L'account del servizio IR non è riuscito a recuperare l'accesso al certificato

#### <a name="symptoms"></a>Sintomi

Quando si installa un runtime di integrazione self-hosted tramite Microsoft Integration Runtime Configuration Manager, viene generato un certificato con un'autorità di certificazione attendibile (CA). Non è stato possibile applicare il certificato per crittografare la comunicazione tra due nodi e viene visualizzato il seguente messaggio di errore: 

"Impossibile modificare la modalità di crittografia delle comunicazioni Intranet: non è stato possibile concedere a Integration Runtime account del servizio l'accesso al certificato ' \<*certificate name*> '. Codice errore 103 "

![Screenshot che Visualizza il messaggio di errore "... Non è stato possibile concedere l'accesso al certificato dell'account di servizio Integration Runtime ".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Causa

Il certificato utilizza l'archiviazione del provider di archiviazione chiavi (KSP), che non è ancora supportata. Fino a oggi, il runtime di integrazione self-hosted supporta solo l'archiviazione del provider del servizio di crittografia (CSP).

#### <a name="resolution"></a>Soluzione

In questo caso si consiglia di usare i certificati CSP.

**Soluzione 1** 

Per importare il certificato, eseguire il comando seguente:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Screenshot del comando certutil per importare il certificato.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Soluzione 2** 

Per convertire il certificato, eseguire i comandi seguenti:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Prima e dopo la conversione:

![Screenshot del risultato prima della conversione del certificato.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Screenshot del risultato dopo la conversione del certificato.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Runtime di integrazione self-hosted versione 5. x
Per l'aggiornamento alla versione 5. x del Azure Data Factory runtime di integrazione self-hosted, è necessario **.NET Framework Runtime 4.7.2** o versione successiva. Nella pagina di download sono disponibili collegamenti di download per la versione 4. x più recente e le ultime due versioni 5. x. 

Per i clienti Azure Data Factory V2:
- Se l'aggiornamento automatico è acceso ed è già stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, il runtime di integrazione self-hosted verrà aggiornato automaticamente alla versione 5. x più recente.
- Se l'aggiornamento automatico è acceso e non è stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, il runtime di integrazione self-hosted non verrà aggiornato automaticamente alla versione 5. x più recente. Il runtime di integrazione self-hosted resterà nella versione 4. x corrente. È possibile visualizzare un avviso per un aggiornamento del runtime .NET Framework nel portale e nel client del runtime di integrazione self-hosted.
- Se l'aggiornamento automatico è disattivato ed è già stato eseguito l'aggiornamento del runtime di .NET Framework a 4.7.2 o versione successiva, è possibile scaricare manualmente la versione 5. x più recente e installarla nel computer.
- Se l'aggiornamento automatico è disattivato e non è stato eseguito l'aggiornamento di .NET Framework Runtime a 4.7.2 o versione successiva. Quando si tenta di installare manualmente il runtime di integrazione self-hosted 5. x e si registra la chiave, sarà necessario aggiornare prima la versione di .NET Framework Runtime.


Per i clienti Azure Data Factory V1:
- Il runtime di integrazione self-hosted 5. X non supporta Azure Data Factory V1.
- Il runtime di integrazione self-hosted verrà aggiornato automaticamente alla versione più recente di 4. x. E la versione più recente di 4. x non scadrà. 
- Se si prova a installare manualmente il runtime di integrazione self-hosted 5. x e si registra la chiave, si riceverà una notifica che il runtime di integrazione self-hosted 5. x non supporta Azure Data Factory V1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problemi di connettività IR self-hosted

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Il runtime di integrazione self-hosted non è in grado di connettersi al servizio cloud

#### <a name="symptoms"></a>Sintomi

Quando si tenta di registrare il runtime di integrazione self-hosted, Configuration Manager Visualizza il messaggio di errore seguente:

"Si è verificato un errore del nodo Integration Runtime (self-hosted) durante la registrazione".

![Screenshot del messaggio "il nodo Integration Runtime (self-hosted) ha rilevato un errore durante la registrazione".](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

Il runtime di integrazione self-hosted non riesce a connettersi al back-end del servizio Azure Data Factory. Questo problema è in genere causato da impostazioni di rete nel firewall.

#### <a name="resolution"></a>Soluzione

1. Verificare se il servizio Integration Runtime è in esecuzione. In caso contrario, andare al passaggio 2.
    
   ![Screenshot che indica che il servizio IR self-hosted è in esecuzione.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se nel runtime di integrazione self-hosted non è configurato alcun proxy, che è l'impostazione predefinita, eseguire il comando di PowerShell seguente nel computer in cui è installato il runtime di integrazione self-hosted:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > L'URL del servizio può variare a seconda del percorso dell'istanza di data factory. Per trovare l'URL del servizio, selezionare **AAD interfaccia utente**  >  **connessioni**  >  **runtime di integrazione**  >  **Modifica nodi IR indipendenti**  >    >  **visualizzare gli URL del servizio**.
            
    La risposta prevista è la seguente:
            
    ![Screenshot della risposta del comando di PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se non si riceve la risposta prevista, usare uno dei metodi seguenti, a seconda delle esigenze:
            
    * Se viene visualizzato il messaggio "Non è stato possibile risolvere il nome remoto", significa che è presente un problema a livello di DNS (Domain Name System). Per risolvere il problema, contattare il team di rete.
    * Se si riceve un messaggio "certificato SSL/TLS non attendibile", [controllare il certificato](https://wu2.frontend.clouddatahub.net/) per verificare se è attendibile nel computer, quindi installare il certificato pubblico usando Gestione certificati. Questa azione dovrebbe attenuare il problema.
    * Passare a   >    >  **registri applicazioni e servizi** del Visualizzatore eventi di Windows  >  **Integration Runtime** e verificare la presenza di eventuali errori causati da DNS, da una regola del firewall o dalle impostazioni di rete aziendale. Se si rileva un errore di questo tipo, chiudere forzatamente la connessione. Poiché ogni azienda ha le proprie impostazioni di rete personalizzate, contattare il team di rete per risolvere questi problemi.

1. Se il proxy è stato configurato nel runtime di integrazione self-hosted, verificare che il server proxy possa accedere all'endpoint del servizio. Per un comando di esempio, vedere [PowerShell, Web Requests, and Proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

La risposta prevista è la seguente:
            
![Screenshot della risposta prevista del comando di PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerazioni sul proxy:
> * Verificare se il server proxy deve essere inserito nell'elenco dei destinatari sicuri. In tal caso, verificare che [questi domini](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) siano nell'elenco Destinatari attendibili.
> * Verificare che il certificato SSL/TLS "wu2.frontend.clouddatahub.net/" sia attendibile nel server proxy.
> * Se si usa l'autenticazione Active Directory sul proxy, sostituire l'account del servizio con l'account utente che può accedere al proxy come "servizio Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Messaggio di errore: lo stato del nodo del runtime di integrazione self-hosted/Logical self-hosted è in stato inattivo/"in esecuzione (limitato)"

#### <a name="cause"></a>Causa 

Il nodo runtime integrato self-hosted potrebbe avere uno stato **inattivo**, come illustrato nello screenshot seguente:

![Screenshot del nodo runtime integrato self-hosted con stato inattivo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Questo comportamento si verifica quando i nodi non possono comunicare tra loro.

#### <a name="resolution"></a>Risoluzione

1. Accedere alla macchina virtuale (VM) ospitata da un nodo. In **registri applicazioni e servizi**  >  **Integration Runtime** aprire Visualizzatore eventi, quindi filtrare i log degli errori.

1. Verificare se nel log degli errori è presente l'errore seguente: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se viene visualizzato questo errore, eseguire il comando seguente in una finestra del prompt dei comandi: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se viene visualizzato l'errore della riga di comando "Impossibile aprire la connessione all'host" visualizzato nella schermata seguente, contattare il reparto IT per assistenza per risolvere il problema. Dopo aver completato Telnet, contattare supporto tecnico Microsoft se si verificano ancora problemi con lo stato del nodo di Integration Runtime.
        
   ![Screenshot dell'errore della riga di comando "Impossibile aprire la connessione all'host".](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verificare se il log degli errori contiene la voce seguente:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Per risolvere il problema, provare uno o entrambi i metodi seguenti:
    - Inserire tutti i nodi nello stesso dominio.
    - Aggiungere l'indirizzo IP al mapping dell'host in tutti i file host della macchina virtuale ospitata.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Problema di connettività tra il runtime di integrazione self-hosted e l'istanza di data factory o il runtime di integrazione self-hosted e l'origine dati o il sink

Per risolvere il problema relativo alla connettività di rete, è necessario sapere come raccogliere la traccia di rete, comprendere come utilizzarla e [analizzare la traccia Microsoft Network Monitor (Netmon)](#analyze-the-netmon-trace) prima di applicare gli strumenti NetMon in casi reali dal runtime di integrazione self-hosted.

#### <a name="symptoms"></a>Sintomi

In alcuni casi potrebbe essere necessario risolvere alcuni problemi di connettività tra il runtime di integrazione self-hosted e l'istanza di data factory, come illustrato nello screenshot seguente, oppure tra il runtime di integrazione self-hosted e l'origine dati o il sink. 

![Screenshot di un messaggio "richiesta HTTP elaborata non riuscita"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

In entrambe le istanze è possibile che si verifichino gli errori seguenti:

* "Copia non riuscita con errore: tipo = Microsoft. datatransfer. Common. Shared. HybridDeliveryException, messaggio = Impossibile connettersi a SQL Server:' indirizzo IP '"

* "Si sono verificati uno o più errori. Si è verificato un errore durante l'invio della richiesta. La connessione sottostante è stata chiusa: si è verificato un errore imprevisto in una ricezione. Impossibile leggere i dati dalla connessione di trasporto: una connessione esistente è stata chiusa forzatamente dall'host remoto. Una connessione esistente è stata chiusa forzatamente dall'ID attività host remoto. "

#### <a name="resolution"></a>Soluzione

Quando si verificano gli errori precedenti, risolverli seguendo le istruzioni riportate in questa sezione.

- Raccolta di una traccia Netmon per l'analisi: 

    1. È possibile impostare il filtro per visualizzare un ripristino dal server al lato client. Nella schermata di esempio seguente è possibile notare che il lato server è il server Data Factory.

        ![Screenshot del server di data factory.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Quando si ottiene il pacchetto di reimpostazione, è possibile trovare la conversazione seguendo Transmission Control Protocol (TCP).

        ![Screenshot della conversazione TCP.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Ottenere la conversazione tra il client e il server Data Factory riportato di seguito rimuovendo il filtro.

        ![Screenshot dei dettagli della conversazione.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Un'analisi della traccia Netmon raccolta Mostra che la durata (TTL) totale è 64. In base ai valori indicati nell'articolo [nozioni di base su durata (TTL) e limite hop dell'IP](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) , estratti nell'elenco seguente, è possibile notare che si tratta del sistema Linux che reimposta il pacchetto e causa la disconnessione.

    I valori TTL predefiniti e limite hop variano tra diversi sistemi operativi, come indicato di seguito:
    - Kernel Linux 2,4 (circa 2001): 255 per TCP, UDP (User Datagram Protocol) e Internet Control Message Protocol (ICMP)
    - Kernel Linux 4,10 (2015): 64 per TCP, UDP e ICMP
    - Windows XP (2001): 128 per TCP, UDP e ICMP
    - Windows 10 (2015): 128 per TCP, UDP e ICMP
    - Windows Server 2008:128 per TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 per TCP, UDP e ICMP
    - macOS (2001): 64 per TCP, UDP e ICMP

    ![Screenshot che mostra un valore TTL pari a 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Nell'esempio precedente, la durata (TTL) viene visualizzata come 61 anziché 64, perché quando il pacchetto di rete raggiunge la destinazione, deve passare attraverso diversi hop, ad esempio router o dispositivi di rete. Il numero di router o dispositivi di rete viene sottratto per produrre la durata (TTL) finale.
    
    In questo caso, è possibile notare che è possibile inviare una reimpostazione dal sistema Linux con TTL 64.

-  Per confermare la provenienza del dispositivo di reimpostazione, controllare il quarto hop dal runtime di integrazione self-hosted.
 
    *Pacchetto di rete dal sistema Linux A con TTL 64-> B TTL 64 meno 1 = 63-> C TTL 63 meno 1 = 62-> TTL 62 meno 1 = 61 runtime di integrazione self-hosted*

- In una situazione ideale, il numero di hop TTL è 128, il che significa che nel sistema operativo Windows è in esecuzione l'istanza di data factory. Come illustrato nell'esempio seguente, *128 meno 107 = 21 hop*, il che significa che 21 hop per il pacchetto sono stati inviati dall'istanza di data factory al runtime di integrazione self-hosted durante l'handshake TCP 3.
 
    ![Screenshot che mostra un valore TTL pari a 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Pertanto, è necessario coinvolgere il team di rete per verificare quale sia il quarto hop dal runtime di integrazione self-hosted. Se si tratta del firewall, come nel sistema Linux, controllare i log per verificare il motivo per cui il dispositivo Reimposta il pacchetto dopo l'handshake TCP 3. 
    
    Se non si è certi della posizione in cui eseguire l'analisi, provare a ottenere la traccia Netmon dal runtime di integrazione self-hosted e dal firewall durante il periodo di tempo problematico. Questo approccio consente di determinare quale dispositivo potrebbe avere reimpostato il pacchetto e ha causato la disconnessione. In questo caso, è anche necessario coinvolgere il team di rete per andare avanti.

### <a name="analyze-the-netmon-trace"></a>Analizzare la traccia Netmon

> [!NOTE] 
> Le istruzioni seguenti si applicano alla traccia Netmon. Poiché la traccia Netmon non è attualmente supportata, è possibile utilizzare Wireshark a questo scopo.

Quando si prova a usare telnet **8.8.8.8 888** con la traccia Netmon raccolta, verrà visualizzata la traccia negli screenshot seguenti:

![Screenshot che mostra il messaggio di errore "Impossibile aprire la connessione all'host sulla porta 888".](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Screenshot che mostra una descrizione della traccia Netmon.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Le immagini precedenti mostrano che non è stato possibile eseguire una connessione TCP al lato server **8.8.8.8** sulla porta **888**, quindi vengono visualizzati due pacchetti aggiuntivi **SynReTransmit** . Poiché il **HOST2** di origine non è riuscito a connettersi a **8.8.8.8** con il primo pacchetto, il tentativo di stabilire la connessione continuerà.

> [!TIP]
> Per eseguire questa connessione, provare la soluzione seguente:
> 1. Selezionare **Carica filtro**  >  **standard**  >  **indirizzi**  >  **IPv4 indirizzi**.
> 1. Per applicare il filtro, immettere **IPv4. Address = = 8.8.8.8**, quindi selezionare **applica**. Dovrebbe essere visualizzata la comunicazione dal computer locale alla destinazione **8.8.8.8**.

![Screenshot che Mostra gli indirizzi filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Screenshot che Mostra più indirizzi filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Gli scenari riusciti sono illustrati negli esempi seguenti: 

- Se è possibile usare telnet **8.8.8.8 53** senza problemi, viene eseguito un handshake TCP 3 e la sessione termina con un handshake TCP 4.

    ![Screenshot che mostra uno scenario di connessione riuscito.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Screenshot che mostra i dettagli di uno scenario di connessione riuscito.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Il precedente handshake TCP 3 produce il flusso di lavoro seguente:

    ![Diagramma di un flusso di lavoro TCP 3 handshake.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- L'handshake TCP 4 per completare la sessione è illustrato dai flussi di lavoro seguenti:

    ![Schermata dei dettagli dell'handshake TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagramma di un flusso di lavoro TCP 4 handshake.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Notifica tramite posta elettronica di Microsoft sull'aggiornamento della configurazione di rete

È possibile che venga visualizzata la seguente notifica tramite posta elettronica, che consiglia di aggiornare la configurazione di rete per consentire la comunicazione con i nuovi indirizzi IP per Azure Data Factory entro l'8 novembre 2020:

   ![Screenshot della notifica tramite posta elettronica di Microsoft che richiede l'aggiornamento della configurazione di rete.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Determinare se la notifica ha effetto sull'utente

Questa notifica si applica agli scenari seguenti:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Scenario 1: comunicazione in uscita da un runtime di integrazione self-hosted in esecuzione in locale dietro a un firewall aziendale

Come determinare se si è interessati:

- *Non* si è interessati se si definiscono regole del firewall basate su nomi di dominio completi (FQDN) che usano l'approccio descritto in [configurare una configurazione del firewall e consentire l'elenco degli indirizzi IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses).

- Si *è interessati se si Abilita* in modo esplicito l'elenco Consenti per gli IP in uscita nel firewall aziendale.

   Se si è interessati, intraprendere l'azione seguente: entro l'8 novembre 2020, inviare una notifica al team dell'infrastruttura di rete per aggiornare la configurazione di rete in modo da usare gli indirizzi IP data factory più recenti. Per scaricare gli indirizzi IP più recenti, vedere [individuare i tag del servizio usando file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Scenario 2: comunicazione in uscita da un runtime di integrazione self-hosted in esecuzione in una macchina virtuale di Azure all'interno di una rete virtuale di Azure gestita dal cliente

Come determinare se si è interessati:

- Controllare se sono presenti regole del gruppo di sicurezza di rete in uscita (NSG) in una rete privata che contiene il runtime di integrazione self-hosted. Se non sono presenti restrizioni in uscita, l'utente non è interessato.

- Se sono presenti restrizioni per le regole in uscita, verificare se si stanno usando i tag del servizio. Se si usano i tag di servizio, non si è interessati. Non è necessario modificare o aggiungere altro perché il nuovo intervallo IP è sotto i tag del servizio esistenti. 

  ![Screenshot di un controllo di destinazione che mostra DataFactory come destinazione.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Si verificano problemi *se si Abilita* in modo esplicito l'elenco Consenti per indirizzi IP in uscita nell'impostazione regole NSG nella rete virtuale di Azure.

   Se si è interessati, intraprendere l'azione seguente: entro l'8 novembre 2020, inviare una notifica al team dell'infrastruttura di rete per aggiornare le regole di NSG nella configurazione della rete virtuale di Azure in modo da usare gli indirizzi IP data factory più recenti. Per scaricare gli indirizzi IP più recenti, vedere [individuare i tag del servizio usando file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Scenario 3: comunicazione in uscita da SSIS Integration Runtime in una rete virtuale di Azure gestita dal cliente

Come determinare se si è interessati:

- Verificare se sono presenti regole NSG in uscita in una rete privata che contiene SQL Server Integration Services Integration Runtime (SSIS). Se non sono presenti restrizioni in uscita, l'utente non è interessato.

- Se sono presenti restrizioni per le regole in uscita, verificare se si stanno usando i tag del servizio. Se si usano i tag di servizio, non si è interessati. Non è necessario modificare o aggiungere altro perché il nuovo intervallo IP è sotto i tag del servizio esistenti.

- Si verificano problemi *se si Abilita* in modo esplicito l'elenco Consenti per indirizzi IP in uscita nell'impostazione regole NSG nella rete virtuale di Azure.

  Se si è interessati, intraprendere l'azione seguente: entro l'8 novembre 2020, inviare una notifica al team dell'infrastruttura di rete per aggiornare le regole di NSG nella configurazione della rete virtuale di Azure in modo da usare gli indirizzi IP data factory più recenti. Per scaricare gli indirizzi IP più recenti, vedere [individuare i tag del servizio usando file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Non è stato possibile stabilire una relazione di trust per il canale sicuro SSL/TLS 

#### <a name="symptoms"></a>Sintomi

Il runtime di integrazione self-hosted non è riuscito a connettersi al servizio Azure Data Factory.

Quando si controlla il registro eventi di runtime di integrazione self-hosted o i log delle notifiche client nella tabella CustomLogEvent, è possibile trovare il messaggio di errore seguente:

"Connessione sottostante chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS. Il certificato remoto non è stato ritenuto valido dalla procedura di convalida."

Il modo più semplice per verificare il certificato del server del servizio Data Factory consiste nell'aprire l'URL del servizio Data Factory nel browser. Ad esempio, aprire il [collegamento controlla certificato server](https://eu.frontend.clouddatahub.net/) nel computer in cui è installato il runtime di integrazione self-hosted e quindi visualizzare le informazioni sul certificato del server.

  ![Screenshot del riquadro controlla certificato server del servizio Azure Data Factory.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Screenshot della finestra per verificare il percorso di certificazione del server.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Causa

Questo problema può essere dovuto a due motivi:

- Motivo 1: la CA radice del certificato del server del servizio Data Factory non è attendibile nel computer in cui è installato il runtime di integrazione self-hosted. 
- Motivo 2: si sta usando un proxy nell'ambiente, il certificato del server del servizio Data Factory viene sostituito dal proxy e il certificato del server sostituito non è considerato attendibile dal computer in cui è installato il runtime di integrazione self-hosted.

#### <a name="resolution"></a>Soluzione

- Per il motivo 1: assicurarsi che il certificato del server Data Factory e la relativa catena di certificati siano considerati attendibili dal computer in cui è installato il runtime di integrazione self-hosted.
- Per il motivo 2: considerare attendibile la CA radice sostituita nel computer IR indipendente o configurare il proxy in modo che non sostituisca il certificato del server Data Factory.

Per ulteriori informazioni sull'attendibilità dei certificati in Windows, vedere [installazione del certificato radice attendibile](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Informazioni aggiuntive
È stato implementato un nuovo certificato SSL, firmato da DigiCert. Verificare se DigiCert Global radice G2 si trova nella CA radice attendibile.

  ![Screenshot che mostra la cartella DigiCert Global radice G2 nella directory autorità di certificazione radice attendibili.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Se non è presente nella CA radice attendibile, [scaricarlo qui](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Condivisione del runtime di integrazione self-hosted

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>La condivisione di un runtime di integrazione self-hosted da un tenant diverso non è supportata 

#### <a name="symptoms"></a>Sintomi

Si potrebbero notare altre Data Factory (in tenant diversi) perché si sta provando a condividere il runtime di integrazione self-hosted dall'interfaccia utente di Azure Data Factory, ma non è possibile condividerlo tra data factory che si trovano in tenant diversi.

#### <a name="cause"></a>Causa

Il runtime di integrazione self-hosted non può essere condiviso tra i tenant.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&una pagina](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guida alle prestazioni dei flussi di dati di mapping](concepts-data-flow-performance.md)
