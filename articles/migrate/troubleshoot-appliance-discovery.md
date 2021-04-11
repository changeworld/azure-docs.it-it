---
title: Risolvere i problemi di distribuzione e individuazione di Azure Migrate Appliance
description: Ottenere supporto per la distribuzione dell'appliance e l'individuazione del server.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 995914fab0e7112327ebf6ab8e32fb67181f481e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608919"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Risolvere i problemi relativi a Azure Migrate Appliance e all'individuazione

Questo articolo illustra come risolvere i problemi durante la distribuzione del dispositivo [Azure migrate](migrate-services-overview.md) e l'uso dell'appliance per individuare i server locali.

## <a name="whats-supported"></a>Elementi supportati

[Esaminare](migrate-appliance.md) i requisiti di supporto dell'appliance.

## <a name="invalid-ovf-manifest-entry"></a>"Voce del manifesto OVF non valida"

Se viene visualizzato l'errore "il file manifesto specificato non è valido: voce del manifesto OVF non valida", eseguire le operazioni seguenti:

1. Verificare che il file OVA del dispositivo Azure Migrate venga scaricato correttamente controllando il relativo valore hash. [Altre informazioni](./tutorial-discover-vmware.md) Se il valore hash non corrisponde, scaricare di nuovo il file OVA e riprovare la distribuzione.
2. Se la distribuzione ha ancora esito negativo e si usa il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client Web di vSphere. Se la distribuzione non riesce ancora, provare a usare un altro Web browser.
3. Se si usa il client Web vSphere e si prova a distribuirlo in server vCenter 6,5 o 6,7, provare a distribuire gli OVA direttamente nell'host ESXi:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) con il client Web (https://<*indirizzo IP host*>/UI).
   - In **Home**  >  **Inventory** selezionare **file**  >  **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Non è possibile connettersi a Internet

Questo problema può verificarsi se il server appliance si trova dietro un proxy.

- Assicurarsi di specificare le credenziali di autorizzazione, se richieste dal proxy.
- Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, aggiungere [questi URL](migrate-appliance.md#url-access) a un oggetto allow.
- Se si usa un proxy di intercettazione per connettersi a Internet, importare il certificato proxy nell'appliance attenendosi alla [procedura seguente](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Non è possibile accedere ad Azure dall'app Web Appliance

Se si usa l'account Azure errato per accedere ad Azure, viene visualizzato l'errore "dispiace, ma si è verificato un problema durante l'accesso". Questo errore si verifica per due motivi:

- Se si accede all'applicazione Web Appliance per il cloud pubblico, usando le credenziali dell'account utente per il portale cloud per enti pubblici.
- Se si accede all'applicazione Web Appliance per il cloud per enti pubblici usando le credenziali dell'account utente per il portale del cloud privato.

Assicurarsi di usare le credenziali corrette.

## <a name="datetime-synchronization-error"></a>Errore di sincronizzazione di data/ora

Un errore relativo alla sincronizzazione di data e ora (802) indica che l'orologio del server potrebbe non essere sincronizzato con l'ora corrente da più di cinque minuti. Modificare l'ora di clock del server di raccolta in modo che corrisponda all'ora corrente:

1. Aprire un prompt dei comandi di amministratore sul server.
2. Per controllare il fuso orario, eseguire **w32tm/TZ**.
3. Per sincronizzare l'ora, eseguire **w32tm sull'/Resync**.

## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

Se viene ricevuto questo errore di connessione, potrebbe non essere possibile connettersi a server vCenter *nomeserver*. com: 9443. I dettagli dell'errore indicano che non esiste alcun endpoint in ascolto in `https://\*servername*.com:9443/sdk` grado di accettare il messaggio.

- Controllare se si sta eseguendo la versione più recente dell'appliance. In caso contrario, aggiornare l'appliance alla [versione più recente](./migrate-appliance.md).
- Se il problema si verifica ancora nella versione più recente, l'appliance potrebbe non essere in grado di risolvere il nome del server vCenter specificato oppure la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.

    1. Eseguire il ping *ServerName*. com dall'appliance.
    2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter usando l'indirizzo IP.
    3. Identificare il numero di porta corretto per la connessione server vCenter.
    4. Verificare che server vCenter sia attivo e in esecuzione.

## <a name="error-6005260039-appliance-might-not-be-registered"></a>Errore 60052/60039: l'appliance potrebbe non essere registrata

- Errore 60052, "l'appliance potrebbe non essere stata registrata correttamente nel progetto" si verifica se l'account Azure usato per registrare l'appliance non dispone di autorizzazioni sufficienti.
    - Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
    - [Altre](./migrate-appliance.md#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.
- Errore 60039, "l'appliance potrebbe non essere registrata correttamente nel progetto" può verificarsi se la registrazione non riesce perché il progetto usato per registrare l'appliance non è stato trovato.
    - Nel portale di Azure e verificare se il progetto esiste nel gruppo di risorse.
    - Se il progetto non esiste, creare un nuovo progetto nel gruppo di risorse e registrare di nuovo l'appliance. [Informazioni su come](./create-manage-projects.md#create-a-project-for-the-first-time) creare un nuovo progetto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Errore 60030/60031: operazione di gestione Key Vault non riuscita

Se viene visualizzato l'errore 60030 o 60031, "un'operazione di gestione Azure Key Vault non riuscita", eseguire le operazioni seguenti:

- Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
- Verificare che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione.
- Se il problema persiste, contattare il supporto tecnico Microsoft.
- [Altre](./migrate-appliance.md#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Errore 60028: non è stato possibile avviare l'individuazione

Errore 60028: "Impossibile avviare l'individuazione a causa di un errore. L'operazione non è riuscita per l'elenco specificato di host o cluster "indica che non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema durante l'accesso o il recupero delle informazioni sul server. Il resto degli host è stato aggiunto correttamente.

- Aggiungere di nuovo gli host elencati nell'errore utilizzando l'opzione **Aggiungi host** .
- Se si verifica un errore di convalida, rivedere le indicazioni per la correzione per correggere gli errori, quindi provare a eseguire di nuovo l'opzione **Salva e avvia individuazione** .

## <a name="error-60025-azure-ad-operation-failed"></a>Errore 60025: operazione Azure AD non riuscita

Errore 60025: "un'operazione Azure AD non è riuscita. L'errore si è verificato durante la creazione o l'aggiornamento dell'applicazione Azure AD "si verifica quando l'account utente di Azure usato per avviare l'individuazione è diverso dall'account usato per registrare l'appliance. Eseguire una delle operazioni seguenti:

- Assicurarsi che l'account utente che avvia l'individuazione sia uguale a quello usato per registrare l'appliance.
- Fornire Azure Active Directory le autorizzazioni di accesso alle applicazioni per l'account utente per il quale l'operazione di individuazione ha esito negativo.
- Eliminare il gruppo di risorse creato in precedenza per il progetto. Creare un altro gruppo di risorse per riavviarlo.
- [Altre](./migrate-appliance.md#appliance---vmware) informazioni sulle autorizzazioni per l'applicazione Azure Active Directory.

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Errore 50004: non è possibile connettersi all'host o al cluster

Errore 50004: "Impossibile connettersi a un host o a un cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 "potrebbe verificarsi se il servizio DNS di Azure per l'appliance non riesce a risolvere il cluster o il nome host specificato.

- Se viene visualizzato questo errore nel cluster, FQDN del cluster.
- Questo errore può essere visualizzato anche per gli host in un cluster. Ciò indica che l'appliance è in grado di connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN. Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
    1. Aprire Blocco note come amministratore.
    2. Aprire il file C:\Windows\System32\Drivers\etc\hosts.
    3. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
    4. Salvare e chiudere il file degli host.
    5. Controllare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, nel portale di Azure verranno visualizzate le informazioni più recenti per questi host.

## <a name="error-60001-unable-to-connect-to-server"></a>Errore 60001: Impossibile connettersi al server

- Verificare che sia disponibile la connettività dal dispositivo al server
- Se si tratta di un server Linux, assicurarsi che l'autenticazione basata su password sia abilitata attenendosi alla procedura seguente:
    1. Accedere al server Linux e aprire il file di configurazione SSH usando il comando ' vi nella/etc/ssh/sshd_config '
    2. Impostare l'opzione "PasswordAuthentication" su Sì. Salvare il file.
    3. Riavviare il servizio SSH eseguendo "service sshd restart"
- Se si tratta di un server Windows, assicurarsi che la porta 5985 sia aperta per consentire le chiamate WMI remote.
- Se si sta scoprendo un server GCP Linux e si usa un utente root, usare i comandi seguenti per modificare l'impostazione predefinita per l'account di accesso radice
    1. Accedere al server Linux e aprire il file di configurazione SSH usando il comando ' vi nella/etc/ssh/sshd_config '
    2. Impostare l'opzione "PermitRootLogin" su Sì.
    3. Riavviare il servizio SSH eseguendo "service sshd restart"

## <a name="error-no-suitable-authentication-method-found"></a>Errore: non è stato trovato alcun metodo di autenticazione appropriato

Verificare che l'autenticazione basata su password sia abilitata nel server Linux attenendosi alla procedura seguente:
    1. Accedere al server Linux e aprire il file di configurazione SSH usando il comando ' vi nella/etc/ssh/sshd_config '
    2. Impostare l'opzione "PasswordAuthentication" su Sì. Salvare il file.
    3. Riavviare il servizio SSH eseguendo "service sshd restart"

## <a name="discovered-servers-not-in-portal"></a>Server individuati non nel portale

Se lo stato di individuazione è "individuazione in corso", ma non sono ancora visibili i server nel portale, attendere alcuni minuti:

- Sono necessari circa 15 minuti per un server in VMware.
- Sono necessari circa due minuti per ogni host aggiunto per i server nell'individuazione di Hyper-V.

Se si attende che lo stato non cambia, selezionare **Aggiorna** nella scheda **Server** . Verrà visualizzato il numero dei server individuati in Azure Migrate: individuazione e valutazione e Azure Migrate: migrazione del server.

Se questa operazione non funziona e si stanno scoprendo i server VMware:

- Verificare che l'account vCenter specificato disponga delle autorizzazioni impostate correttamente, con accesso ad almeno un server.
- Azure Migrate non è in grado di individuare i server in VMware se l'account vCenter ha accesso concesso a livello di cartella VM vCenter. [Altre](set-discovery-scope.md) informazioni sull'individuazione dell'ambito.

## <a name="server-data-not-in-portal"></a>Dati del server non nel portale

Se i server individuati non vengono visualizzati nel portale o se i dati del server sono obsoleti, attendere alcuni minuti. Sono necessari fino a 30 minuti per la visualizzazione delle modifiche nei dati di configurazione del server individuati nel portale. Potrebbero essere necessarie alcune ore per visualizzare le modifiche apportate ai dati di inventario software. Se non sono presenti dati dopo questa volta, provare ad aggiornare, come indicato di seguito.

1. In **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, selezionare **Panoramica**.
2. In **Gestisci** selezionare **integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="deleted-servers-appear-in-portal"></a>I server eliminati vengono visualizzati nel portale

Se si eliminano i server che ancora vengono visualizzati nel portale, attendere 30 minuti. Se vengono ancora visualizzati, aggiornare come descritto in precedenza.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>Inventario software e istanze di SQL Server e database individuati non nel portale

Dopo aver avviato l'individuazione nell'appliance, potrebbero essere necessarie fino a 24 ore per iniziare a visualizzare i dati di inventario nel portale.

Se non è stata specificata l'autenticazione di Windows o SQL Server credenziali di autenticazione in Gestione configurazione Appliance, aggiungere le credenziali in modo che l'appliance possa usarle per connettersi alle rispettive istanze di SQL Server.

Una volta stabilita la connessione, appliance raccoglie i dati di configurazione e delle prestazioni delle istanze di SQL Server e dei database. I dati di configurazione SQL Server vengono aggiornati ogni 24 ore e i dati sulle prestazioni vengono acquisiti ogni 30 secondi. Di conseguenza, qualsiasi modifica apportata alle proprietà dell'istanza SQL Server e dei database, ad esempio lo stato del database, il livello di compatibilità e così via, può richiedere fino a 24 ore per l'aggiornamento nel portale.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server istanza viene visualizzata nello stato "non connesso" nel portale

Per visualizzare i problemi riscontrati durante l'individuazione di SQL Server istanze e dei database, fare clic su stato "non connesso" nella colonna stato connessione nella pagina "server individuati" del progetto.

La creazione di una valutazione su server contenenti istanze SQL che non sono state individuate completamente o che non si trovano nello stato connesso, può comportare la conformità del contrassegno come "sconosciuto".

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Non vengono visualizzati i dati sulle prestazioni per alcune schede di rete nei server fisici

Questo problema può verificarsi se nel server fisico è abilitata la virtualizzazione Hyper-V. A causa del Gap del prodotto, la velocità effettiva della rete viene acquisita sulle schede di rete virtuali individuate.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Errore: il formato del file caricato non è corretto

Alcuni strumenti hanno impostazioni internazionali che creano il file CSV con il punto e virgola come delimitatore. Modificare le impostazioni in modo che il delimitatore sia una virgola.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>È stato importato un CSV ma viene visualizzato il messaggio "Individuazione in corso"

Questo stato viene visualizzato se il caricamento del file CSV non è riuscito a causa di un errore di convalida. Provare di nuovo a importare il CSV. Per correggere gli errori, è possibile scaricare il report dell’errore relativo al caricamento precedente e seguire le istruzioni per la correzione nel file. Il report degli errori può essere scaricato dalla sezione ' importa dettagli ' nella pagina ' individua server '.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Non visualizzare i dettagli dell'inventario software anche dopo l'aggiornamento delle credenziali Guest

L'individuazione dell'inventario software viene eseguita ogni 24 ore. Se si desidera visualizzare immediatamente i dettagli, aggiornare come indicato di seguito. L'operazione potrebbe richiedere alcuni minuti a seconda del numero di minuti. dei server individuati.

1. In **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, selezionare **Panoramica**.
2. In **Gestisci** selezionare **integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="unable-to-export-software-inventory"></a>Non è possibile esportare l'inventario software

Assicurarsi che l'utente che Scarica l'inventario dal portale disponga dei privilegi di collaboratore per la sottoscrizione.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Non è stato trovato alcun metodo di autenticazione appropriato per completare l'autenticazione (PublicKey)

L'autenticazione basata su chiave non funziona, usare l'autenticazione della password.

## <a name="common-app-discovery-errors"></a>Errori comuni di individuazione delle app

Azure Migrate supporta l'individuazione dell'inventario software, usando Azure Migrate: individuazione e valutazione. L'individuazione delle app è attualmente supportata solo per VMware. [Altre](how-to-discover-applications.md) informazioni sui requisiti e i passaggi per la configurazione dell'individuazione delle app.

I tipici errori di individuazione delle app sono riepilogati nella tabella.

| **Error (Errore) (Error (Errore)e)** | **Causa** | **Azione** |
|--|--|--|
| 9000: Impossibile rilevare lo stato dello strumento VMware. | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Verificare che gli strumenti VMware siano installati e in esecuzione nel server. |
| 9001: gli strumenti VMware non sono installati. | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Verificare che gli strumenti VMware siano installati e in esecuzione nel server. |
| 9002: gli strumenti VMware non sono in esecuzione. | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Verificare che gli strumenti VMware siano installati e in esecuzione nel server. |
| 9003: il tipo di sistema operativo non è supportato per l'individuazione del server Guest. | Il sistema operativo in esecuzione nel server non è né Windows né Linux. | I tipi di sistema operativo supportati sono solo Windows e Linux. Se il server è effettivamente Windows o Linux, controllare il tipo di sistema operativo specificato in server vCenter. |
| 9004: il server non è in esecuzione. | Il server è spento. | Verificare che il server sia acceso. |
| 9005: il tipo di sistema operativo non è supportato per l'individuazione del server Guest. | Il tipo di sistema operativo non è supportato per l'individuazione del server Guest. | I tipi di sistema operativo supportati sono solo Windows e Linux. |
| 9006: l'URL per scaricare il file di metadati dal Guest è vuoto. | Questo problema può verificarsi se l'agente di individuazione non funziona come previsto. | Il problema dovrebbe risolvere automaticamente IN24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 9007: Impossibile trovare il processo che esegue l'attività di individuazione nel server Guest. | Questo problema può verificarsi se l'agente di individuazione non funziona correttamente. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 9008: Impossibile recuperare lo stato del processo del server Guest. | Il problema può verificarsi a causa di un errore interno. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 9009: l'account utente di Windows ha impedito l'esecuzione dell'attività di individuazione nel server. | Le impostazioni di controllo dell'account utente (UAC) di Windows nel server sono restrittive e impediscono l'individuazione dell'inventario software installato. | In impostazioni di controllo dell'account utente nel server configurare l'impostazione UAC su uno dei due livelli inferiori. |
| 9010: il server è spento. | Il server è spento. | Verificare che il server sia acceso. |
| 9011: il file di metadati individuato non è stato trovato nel server Guest file system. | Il problema può verificarsi a causa di un errore interno. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 9012: il file di metadati individuati è vuoto. | Il problema può verificarsi a causa di un errore interno. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 9013: viene creato un nuovo profilo temporaneo per ogni account di accesso. | Viene creato un nuovo profilo temporaneo per ogni accesso al server in VMware. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9014: Impossibile recuperare i metadati dal server Guest file system. | Nessuna connettività all'host ESXi | Verificare che l'appliance sia in grado di connettersi alla porta 443 nell'host ESXi che esegue il server |
| 9015: il ruolo operazioni Guest non è abilitato nell'account utente di vCenter | Il ruolo operazioni Guest non è abilitato nell'account utente di vCenter. | Verificare che il ruolo operazioni Guest sia abilitato nell'account utente di vCenter. |
| 9016: Impossibile individuare perché l'agente operativo guest non è aggiornato. | Gli strumenti VMware non sono installati correttamente o non sono aggiornati. | Verificare che gli strumenti VMware siano installati correttamente e aggiornati. |
| 9017: il file con metadati individuati non è stato trovato nel server. | Il problema può verificarsi a causa di un errore interno. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9018: PowerShell non è installato nei server Guest. | PowerShell non è disponibile nel server Guest. | Installare PowerShell nel server Guest. |
| 9019: Impossibile individuare a causa di errori dell'operazione del server Guest. | Operazione guest VMware non riuscita nel server. | Verificare che le credenziali del server siano valide e che il nome utente specificato nelle credenziali del server Guest sia in formato UPN. |
| 9020: autorizzazione per la creazione di file negata. | Il ruolo associato all'utente o i criteri di gruppo limitano l'utente alla creazione del file nella cartella | Controllare se l'utente Guest specificato dispone dell'autorizzazione Crea per il file nella cartella. Vedere **notifiche** in Azure migrate: individuazione e valutazione per il nome della cartella. |
| 9021: Impossibile creare il file nel percorso temporaneo di sistema. | VMware Tool segnala il percorso temporaneo del sistema anziché il percorso temporaneo degli utenti. | Aggiornare la versione dello strumento VMware sopra 10287 (formato client NGC/VI). |
| 9022: accesso all'oggetto WMI negato. | Il ruolo associato all'utente o i criteri di gruppo limitano l'accesso dell'utente all'oggetto WMI. | Contattare il supporto tecnico Microsoft. |
| 9023: Impossibile eseguire PowerShell perché il valore della variabile di ambiente SystemRoot è vuoto. | Il valore della variabile di ambiente SystemRoot è vuoto per il server Guest. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9024: Impossibile individuare poiché il valore della variabile di ambiente TEMP è vuoto. | Il valore della variabile di ambiente TEMP è vuoto per il server Guest. | Contattare il supporto tecnico Microsoft. |
| 9025: PowerShell è danneggiato nei server Guest. | PowerShell è danneggiato nel server Guest. | Reinstallare PowerShell nel server Guest e verificare che PowerShell possa essere eseguito nel server Guest. |
| 9026: Impossibile eseguire le operazioni Guest sul server. | Lo stato del server non consente l'esecuzione di operazioni Guest sul server. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9027: l'agente operativo guest non è in esecuzione nel server. | Impossibile contattare l'agente operativo guest in esecuzione all'interno del server virtuale. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9028: non è possibile creare il file a causa di spazio di archiviazione su disco insufficiente nel server. | Spazio su disco insufficiente. | Verificare che lo spazio disponibile nell'archiviazione su disco del server sia sufficiente. |
| 9029: nessun accesso a PowerShell sulle credenziali del server Guest fornito. | L'accesso a PowerShell non è disponibile per l'utente. | Assicurarsi che l'utente aggiunto all'appliance possa accedere a PowerShell nel server Guest. |
| 9030: non è possibile raccogliere i metadati individuati perché l'host ESXi è disconnesso. | L'host ESXi si trova in uno stato disconnesso. | Verificare che l'host ESXi che esegue il server sia connesso. |
| 9031: non è possibile raccogliere i metadati individuati perché l'host ESXi non risponde. | Lo stato dell'host remoto non è valido. | Verificare che l'host ESXi che esegue il server sia in esecuzione e connesso. |
| 9032: Impossibile individuare a causa di un errore interno. | Il problema può verificarsi a causa di un errore interno. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 9033: Impossibile individuare perché il nome utente del server contiene caratteri non validi. | Sono stati rilevati caratteri non validi nel nome utente. | Specificare nuovamente le credenziali del server per verificare che non siano presenti caratteri non validi. |
| 9034: il nome utente specificato non è in formato UPN. | Il nome utente non è in formato UPN. | Verificare che il nome utente sia in formato UPN (User Principal Name). |
| 9035: Impossibile individuare perché la modalità linguaggio di PowerShell non è impostata su' lingua completa '. | La modalità linguaggio per PowerShell nel server Guest non è impostata su Full Language. | Verificare che la modalità linguaggio di PowerShell sia impostata su "lingua completa". |
| 9037: la raccolta dati è stata sospesa temporaneamente perché il tempo di risposta del server è troppo elevato. | Il server individuato impiega troppo tempo per rispondere | Non è necessaria alcuna azione. Verrà eseguito un nuovo tentativo tra 24 ore per l'individuazione dell'inventario software e 3 ore per l'analisi delle dipendenze (senza agenti). |
| 10000: il tipo di sistema operativo non è supportato. | Il sistema operativo in esecuzione nel server non è né Windows né Linux. | I tipi di sistema operativo supportati sono solo Windows e Linux. |
| 10001: lo script per l'individuazione del server non è stato trovato nel dispositivo. | L'individuazione non funziona come previsto. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 10002: l'attività di individuazione non è stata completata nel tempo. | L'agente di individuazione non funziona come previsto. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| 10003: il processo che esegue l'attività di individuazione è stato terminato con un errore. | Il processo che esegue l'attività di individuazione è stato terminato con un errore. | Il problema dovrebbe risolversi automaticamente tra 24 ore. Se il problema persiste, contattare supporto tecnico Microsoft. |
| 10004: le credenziali non sono state specificate per il tipo di sistema operativo guest. | Le credenziali per accedere ai server di questo tipo di sistema operativo non sono state fornite nell'appliance Azure Migrate. | Aggiungere le credenziali per i server nell'appliance |
| 10005: le credenziali specificate non sono valide. | Le credenziali fornite per l'appliance per accedere al server non sono corrette. | Aggiornare le credenziali fornite nell'appliance e assicurarsi che il server sia accessibile tramite le credenziali. |
| 10006: il tipo di sistema operativo guest non è supportato dall'archivio delle credenziali. | Il sistema operativo in esecuzione nel server non è né Windows né Linux. | I tipi di sistema operativo supportati sono solo Windows e Linux. |
| 10007: Impossibile elaborare i metadati individuati. | Si è verificato un errore durante il tentativo di deserializzare JSON. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 10008: non è possibile creare un file nel server. | Il problema può verificarsi a causa di un errore interno. | Contattare supporto tecnico Microsoft per una risoluzione. |
| 10009: Impossibile scrivere i metadati individuati in un file nel server. | Il problema può verificarsi a causa di un errore interno. | Contattare supporto tecnico Microsoft per una risoluzione. |

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Istanze di SQL Server comuni e errori di individuazione del database

Azure Migrate supporta l'individuazione di istanze di SQL Server e database in esecuzione in computer locali, usando Azure Migrate: individuazione e valutazione. L'individuazione SQL è attualmente supportata solo per VMware. Per iniziare, vedere l'esercitazione sull' [individuazione](tutorial-discover-vmware.md) .

I tipici errori di individuazione SQL sono riepilogati nella tabella.

| **Error (Errore) (Error (Errore)e)** | **Causa** | **Azione** |
|--|--|--|
|30000: le credenziali associate a questo SQL Server non funzionano.|Le credenziali associate manualmente non sono valide o le credenziali associate automaticamente non possono più accedere all'SQL Server.|Aggiungere le credenziali per SQL Server nel dispositivo e attendere il successivo ciclo di individuazione SQL o forzare l'aggiornamento.|
|30001: non è possibile connettersi al SQL Server dal dispositivo.|1. il dispositivo non ha la linea di rete per SQL Server.<br/>2. connessione di blocco del firewall tra SQL Server e appliance.|1. rendere SQL Server raggiungibile dal dispositivo.<br/>2. consentire le connessioni in ingresso dal dispositivo alla SQL Server.|
|30003: il certificato non è attendibile.|Nel computer in cui è in esecuzione SQL Server non è installato un certificato attendibile.|Configurare un certificato attendibile nel server. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153616)|
|30004: autorizzazioni insufficienti.|Questo errore può verificarsi a causa della mancanza di autorizzazioni necessarie per l'analisi di SQL Server istanze. |Concedere il ruolo sysadmin all'account o alle credenziali fornite nell'appliance per individuare SQL Server istanze e i database. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153511)|
|30005: l'accesso SQL Server non è riuscito a connettersi a causa di un problema con il database master predefinito.|Il database non è valido oppure l'account di accesso non dispone dell'autorizzazione CONNECT per il database.|Utilizzare ALTER LOGIN per impostare il database predefinito su database master.<br/>Concedere il ruolo sysadmin all'account o alle credenziali fornite nell'appliance per individuare SQL Server istanze e i database. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153615)|
|30006: non è possibile usare SQL Server account di accesso con l'autenticazione di Windows.|1. l'account di accesso può essere un account di accesso SQL Server ma il server accetta solo l'autenticazione di Windows.<br/>2. si sta tentando di connettersi utilizzando l'autenticazione SQL Server, ma l'account di accesso utilizzato non esiste nel SQL Server.<br/>3. l'account di accesso può utilizzare l'autenticazione di Windows, ma l'account di accesso è un'entità di Windows non riconosciuta. Un'entità di Windows non riconosciuta indica che l'account di accesso non può essere verificato da Windows. La causa potrebbe essere la provenienza dell'account di accesso di Windows da un dominio non attendibile.|Se si sta tentando di eseguire la connessione utilizzando l'autenticazione di SQL Server, verificare che SQL Server sia configurato in modalità di autenticazione mista e che SQL Server account di accesso esista.<br/>Se si sta tentando di effettuare la connessione mediante l'autenticazione di Windows, verificare di essere connessi al dominio corretto. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153421)|
|30007: la password è scaduta.|la password dell'account è scaduta.|È possibile che la password di accesso SQL Server sia scaduta, impostare nuovamente la password e/o estendere la data di scadenza della password. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153419)|
|30008: è necessario modificare la password.|è necessario modificare la password dell'account.|Modificare la password delle credenziali fornite per l'individuazione SQL Server. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153318)|
|30009: si è verificato un errore interno.|Si è verificato un errore interno durante l'individuazione di SQL Server istanze e database. |Se il problema persiste, contattare il supporto tecnico Microsoft.|
|30010: non sono stati trovati database.|Impossibile trovare database dall'istanza del server selezionata.|Concedere il ruolo sysadmin all'account o alle credenziali fornite nell'appliance per l'individuazione dei database SQL.|
|30011: si è verificato un errore interno durante la valutazione di un'istanza o di un database SQL.|Si è verificato un errore interno durante l'esecuzione della valutazione.|Se il problema persiste, contattare il supporto tecnico Microsoft.|
|30012: connessione SQL non riuscita.|1. la connessione è stata rifiutata dal firewall sul server.<br/>2. il servizio di SQL Server Browser (sqlbrowser) non è stato avviato.<br/>3. SQL Server non ha risposto alla richiesta del client perché il server probabilmente non è stato avviato.<br/>4. il client di SQL Server non è in grado di connettersi al server. Questo errore potrebbe verificarsi poiché il server non è configurato per l'accettazione di connessioni remote.<br/>5. il client di SQL Server non è in grado di connettersi al server. L'errore potrebbe verificarsi poiché il client non è in grado di risolvere il nome del server oppure il nome del server non è corretto.<br/>6. i protocolli TCP o named pipe non sono abilitati.<br/>7. il nome dell'istanza di SQL Server specificato non è valido.|Per risolvere il problema di connettività, usare [questa](https://go.microsoft.com/fwlink/?linkid=2153317) guida per l'utente interattivo. Attendere 24 ore dopo aver seguito la guida per i dati da aggiornare nel servizio. Se il problema persiste, contattare il supporto tecnico Microsoft.|
|30013: si è verificato un errore durante il tentativo di stabilire una connessione all'istanza di SQL Server.|1. non è possibile risolvere il nome del SQL Server dal dispositivo.<br/>2. SQL Server non consente le connessioni remote.|Se è possibile eseguire il ping di SQL Server dal dispositivo, attendere 24 ore per verificare se il problema si risolve automaticamente. In caso contrario, contattare il supporto tecnico Microsoft. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153316)|
|30014: il nome utente o la password non è valida.| Questo errore può verificarsi a causa di un errore di autenticazione che comporta una password o un nome utente non valido.|Specificare una credenziale con un nome utente e una password validi. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153315)|
|30015: si è verificato un errore interno durante l'individuazione dell'istanza di SQL.|Si è verificato un errore interno durante l'individuazione dell'istanza di SQL.|Se il problema persiste, contattare il supporto tecnico Microsoft.|
|30016: la connessione all'istanza '% instance;' non è riuscita a causa di un timeout.| Questo problema può verificarsi se il firewall del server rifiuta la connessione.|Verificare che il firewall nel SQL Server sia configurato per accettare le connessioni. Se l'errore si mantiene, contattare il supporto tecnico Microsoft. [Scopri di più](https://go.microsoft.com/fwlink/?linkid=2153611)|
|30017: si è verificato un errore interno.|Eccezione non gestita.|Se il problema persiste, contattare il supporto tecnico Microsoft.|
|30018: si è verificato un errore interno.|Si è verificato un errore interno durante la raccolta dei dati, ad esempio le dimensioni del database temporaneo, le dimensioni del file e così via dell'istanza SQL.|Attendere 24 ore e contattare il supporto tecnico Microsoft se il problema persiste.|
|30019: si è verificato un errore interno.|Si è verificato un errore interno durante la raccolta delle metriche delle prestazioni, ad esempio l'utilizzo della memoria e così via, di un database o di un'istanza.|Attendere 24 ore e contattare il supporto tecnico Microsoft se il problema persiste.|

## <a name="next-steps"></a>Passaggi successivi

Configurare un'appliance per [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)o [server fisici](how-to-set-up-appliance-physical.md).
