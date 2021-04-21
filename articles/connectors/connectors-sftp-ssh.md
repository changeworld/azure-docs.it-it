---
title: Connettersi al server SFTP con SSH
description: Automatizzare le attività che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP usando SSH e App per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781560"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>Creare e gestire file SFTP usando SSH e App per la logica di Azure

Per automatizzare le attività che creano e gestiscono file in un server [SfTP (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) usando il [protocollo Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) è possibile creare flussi di lavoro di integrazione automatizzati usando App per la logica di Azure e il connettore SFTP-SSH. SFTP è un protocollo di rete che fornisce l'accesso ai file, il trasferimento di file e la gestione di file su qualsiasi flusso di dati affidabile.

Ecco alcuni esempi di attività che è possibile automatizzare:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, rinominare, aggiornare, creare elenchi ed eliminare file.
* Creare cartelle.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

Nel flusso di lavoro è possibile usare un trigger che monitora gli eventi nel server SFTP e rende disponibile l'output per altre azioni. È quindi possibile usare le azioni per eseguire varie attività nel server SFTP. È anche possibile includere altre azioni che usano l'output delle azioni SFTP-SSH. Ad esempio, se si recuperano regolarmente file dal server SFTP, è possibile inviare avvisi di posta elettronica su tali file e il relativo contenuto usando il connettore Office 365 Outlook o Outlook.com connettore. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

Per le differenze tra il connettore SFTP-SSH e il connettore SFTP, vedere la sezione Confrontare [SFTP-SSH](#comparison) e SFTP più avanti in questo argomento.

## <a name="limits"></a>Limiti

* Il connettore SFTP-SSH attualmente non supporta questi server SFTP:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* Le azioni SFTP-SSH che supportano la suddivisione in blocchi possono gestire file fino a 1 GB, mentre le azioni SFTP-SSH che non supportano la suddivisione in blocchi possono gestire file fino a 50 MB. [](../logic-apps/logic-apps-handle-large-messages.md) La dimensione predefinita del blocco è 15 MB. Tuttavia, queste dimensioni possono cambiare dinamicamente, a partire da 5 MB e aumentando gradualmente fino al valore massimo di 50 MB. Il ridimensionamento dinamico si basa su fattori come la latenza di rete, il tempo di risposta del server e così via.

  > [!NOTE]
  > Per le app per la logica in un ambiente del servizio di integrazione [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore richiede invece la suddivisione in blocchi per usare i limiti dei messaggi [ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  È possibile eseguire l'override di questo comportamento adattivo quando [si specifica una dimensione costante del blocco](#change-chunk-size) da usare. Queste dimensioni possono variare da 5 MB a 50 MB. Si supponga, ad esempio, di avere un file di 45 MB e una rete in grado di supportare le dimensioni del file senza latenza. La suddivisione in blocchi adattiva comporta diverse chiamate, anziché una sola chiamata. Per ridurre il numero di chiamate, è possibile provare a impostare una dimensione del blocco di 50 MB. In uno scenario diverso, se si verifica il timeout dell'app per la logica, ad esempio quando si usano blocchi da 15 MB, è possibile provare a ridurre le dimensioni a 5 MB.

  La dimensione del blocco è associata a una connessione. Questo attributo significa che è possibile usare la stessa connessione sia per le azioni che supportano la suddivisione in blocchi che per le azioni che non supportano la suddivisione in blocchi. In questo caso, le dimensioni del blocco per le azioni che non supportano la suddivisione in blocchi sono da 5 MB a 50 MB. Questa tabella mostra le azioni SFTP-SSH che supportano la suddivisione in blocchi:

  | Azione | Supporto della suddivisione in blocchi | Eseguire l'override del supporto delle dimensioni dei blocchi |
  |--------|------------------|-----------------------------|
  | **Copia file** | No | Non applicabile |
  | **Crea file** | Sì | Sì |
  | **Crea cartella** | Non applicabile | Non applicabile |
  | **Elimina file** | Non applicabile | Non applicabile |
  | **Estrai archivio nella cartella** | Non applicabile | Non applicabile |
  | **Recupera contenuto di file** | Sì | Sì |
  | **Recupera contenuto di file tramite percorso** | Sì | Sì |
  | **Ottenere i metadati dei file** | Non applicabile | Non applicabile |
  | **Recupera metadati di file tramite percorso** | Non applicabile | Non applicabile |
  | **Elenca i file nella cartella** | Non applicabile | Non applicabile |
  | **Rinomina file** | Non applicabile | Non applicabile |
  | **Aggiorna file** | No | Non applicabile |
  ||||

* I trigger SFTP-SSH non supportano la suddivisione in blocchi dei messaggi. Quando si richiede il contenuto del file, i trigger selezionano solo file di dimensioni pari o inferiori a 15 MB. Per ottenere file di dimensioni superiori a 15 MB, seguire invece questo modello:

  1. Usare un trigger SFTP-SSH che restituisce solo le proprietà del file. Questi trigger hanno nomi che includono la descrizione **(solo proprietà).**

  1. Seguire il trigger con l'azione Get **file content di** SFTP-SSH. Questa azione legge il file completo e usa in modo implicito la suddivisione in blocchi dei messaggi.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH e SFTP a confronto

L'elenco seguente descrive le funzionalità principali di SFTP-SSH che differiscono dal connettore SFTP:

* Usa la [SSH.NET ,](https://github.com/sshnet/SSH.NET)che è una libreria open source Secure Shell (SSH) che supporta .NET.

* Fornisce l'azione **Crea cartella** che crea una cartella nel percorso specificato nel server SFTP.

* Fornisce l'azione **Rinomina file** che rinomina un file nel server SFTP.

* Memorizza nella cache la connessione al server SFTP *per un massimo di 1 ora.* Questa funzionalità migliora le prestazioni e riduce la frequenza con cui il connettore tenta di connettersi al server. Per impostare la durata di questo comportamento di memorizzazione nella cache, modificare la [ **proprietà ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) nella configurazione SSH nel server SFTP.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'indirizzo del server SFTP e le credenziali dell'account, in modo che il flusso di lavoro possa accedere all'account SFTP. È anche necessario accedere a una chiave privata SSH e alla password della chiave privata SSH. Per caricare file di grandi dimensioni usando la suddivisione in blocchi, è necessario l'accesso in lettura e scrittura per la cartella radice nel server SFTP. In caso contrario, viene visualizzato un errore "401 Unauthorized".

  Il connettore SFTP-SSH supporta sia l'autenticazione con chiave privata che l'autenticazione con password. Tuttavia, il connettore SFTP-SSH *supporta* solo questi formati di chiave privata, algoritmi e impronte digitali:

  * **Formati di chiave** privata: chiavi RSA (Rivest Shamir Adleman) e DSA (Digital Signature Algorithm) nei formati OpenSSH e ssh.com. Se la chiave privata è in formato di file PuTTY (ppk), convertire prima di tutto la chiave nel formato [di file OpenSSH (con estensione pem).](#convert-to-openssh)
  * **Algoritmi di crittografia**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  * **Impronta digitale**: MD5

  Dopo aver aggiunto un trigger o un'azione SFTP-SSH al flusso di lavoro, è necessario specificare le informazioni di connessione per il server SFTP. Quando si specifica la chiave privata SSH per questa connessione, * non immettere o modificare manualmente la chiave _, il che potrebbe causare **l'esito** negativo della connessione. Assicurarsi invece di _*_copiare_*_ la chiave dal file di chiave privata SSH e *_di_* incollare * la chiave nei dettagli della connessione. Per altre informazioni, vedere la sezione [Connettersi a SFTP con SSH](#connect) più avanti in questo articolo.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Flusso di lavoro dell'app per la logica in cui si vuole accedere all'account SFTP. Per iniziare con un trigger SFTP-SSH, [creare un flusso di lavoro dell'app per la logica vuoto.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per usare un'azione SFTP-SSH, avviare il flusso di lavoro con un altro trigger, ad esempio il trigger **Ricorrenza.**

## <a name="how-sftp-ssh-triggers-work"></a>Funzionamento dei trigger SFTP-SSH

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Comportamento del polling

I trigger SFTP-SSH eseguano il polling del file system SFTP e ricercano eventuali file modificati dopo l'ultimo polling. Alcuni strumenti consentono di mantenere il timestamp quando i file vengono modificati. In questi casi è necessario disabilitare questa funzionalità per consentire il funzionamento del trigger. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione |
|-------------|--------|
| Winscp | Vai a Opzioni **Preferenze**  >  **Trasferimento**  >  **Modifica**  >  **Mantieni**  >  **timestamp**  >  **Disabilita** |
| FileZilla | Passare a **Transfer**  >  **Preserve timestamps of transferred files**(Mantieni timestamp dei file  >  **trasferiti) Disable (Disabilita)** |
|||

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Attivare lo spostamento e la deriva della ricorrenza

I trigger basati sulla connessione in cui è necessario creare prima una connessione, ad esempio il trigger SFTP-SSH, differiscono dai trigger predefiniti eseguiti in modo nativo in App per la logica di Azure, ad esempio il [trigger Ricorrenza](../connectors/connectors-native-recurrence.md). Nei trigger ricorrenti basati su connessione, la pianificazione della ricorrenza non è l'unico driver che controlla l'esecuzione e il fuso orario determina solo l'ora di inizio iniziale. Le esecuzioni successive dipendono dalla pianificazione della ricorrenza, dall'ultima esecuzione del trigger e da altri fattori che potrebbero causare la deriva dei tempi di esecuzione o produrre un comportamento imprevisto.  Ad esempio, un comportamento imprevisto può includere la mancata gestione della pianificazione specificata all'inizio e alla fine dell'ora legale. Per assicurarsi che il tempo di ricorrenza non si svii quando l'ora legale ha effetto, modificare manualmente la ricorrenza. In questo modo, il flusso di lavoro continua a essere eseguito all'ora prevista. In caso contrario, l'ora di inizio viene spostata avanti di un'ora all'inizio dell'ora legale e di un'ora indietro al termine dell'ora legale. Per altre informazioni, [vedere Ricorrenza per i trigger basati sulla connessione.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Convertire una chiave basata su PuTTY in OpenSSH

Il formato PuTTY e il formato OpenSSH usano estensioni di file diverse. Il formato PuTTY usa l'estensione di file ppk, o chiave privata PuTTY. Il formato OpenSSH usa l'estensione pem, o Privacy Enhanced Mail, del nome file. Se la chiave privata è in formato PuTTY ed è necessario usare il formato OpenSSH, convertire prima di tutto la chiave nel formato OpenSSH seguendo questa procedura:

### <a name="unix-based-os"></a>Sistema operativo basato su Unix

1. Se gli strumenti PuTTY non sono installati nel sistema, eseguire questa operazione ora, ad esempio:

   `sudo apt-get install -y putty`

1. Eseguire questo comando, che crea un file che è possibile usare con il connettore SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Ad esempio:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Sistema operativo Windows

1. Se non è già stato fatto, scaricare lo strumento [PuTTY Generator (puttygen.exe) più](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)recente e quindi avviare lo strumento.

1. In questa schermata selezionare **Carica**.

   ![Selezionare "Carica"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Passare al file di chiave privata in formato PuTTY e selezionare **Apri**.

1. Scegliere Esporta chiave **OpenSSH dal** menu **Conversioni** .

   ![Selezionare "Esporta chiave OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salvare il file di chiave privata con `.pem` l'estensione file.

## <a name="considerations"></a>Considerazioni

Questa sezione descrive le considerazioni da esaminare quando si usano i trigger e le azioni di questo connettore.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Usare cartelle SFTP diverse per il caricamento e l'elaborazione di file

Nel server SFTP usare cartelle separate per archiviare i file caricati e per il trigger per monitorare tali file per l'elaborazione. In caso contrario, il trigger non verrà attivato e si comporta in modo imprevedibile, ad esempio ignorando un numero casuale di file che il trigger elabora. Tuttavia, questo requisito significa che è necessario un modo per spostare i file tra tali cartelle. 

Se si verifica questo problema di trigger, rimuovere i file dalla cartella monitorata dal trigger e usare una cartella diversa per archiviare i file caricati.

<a name="create-file"></a>

### <a name="create-file"></a>Crea file

Per creare un file nel server SFTP, è possibile usare l'azione Crea **file** SFTP-SSH. Quando questa azione crea il file, anche il servizio App per la logica chiama automaticamente il server SFTP per ottenere i metadati del file. Tuttavia, se si sposta il file appena creato prima che il servizio App per la logica possa effettuare la chiamata per ottenere i metadati, viene visualizzato un messaggio `404` di errore, `'A reference was made to a file or folder which does not exist'` . Per ignorare la lettura dei metadati del file dopo la creazione del file, seguire la procedura per aggiungere e impostare la proprietà Ottieni tutti i metadati [del **file** su **No**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Connettersi a SFTP con SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere `sftp ssh` come filtro. Nell'elenco dei trigger selezionare il trigger desiderato.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione selezionare **Nuovo passaggio.** Nella casella di ricerca immettere `sftp ssh` come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. Specificare le informazioni necessarie per la connessione.

   > [!IMPORTANT]
   >
   > Quando si immette la chiave privata SSH nella proprietà **Chiave privata SSH**, seguire questi passaggi aggiuntivi che consentono di assicurarsi di specificare il valore completo e corretto per questa proprietà. Una chiave non valida provoca un errore di connessione.

   Anche se è possibile usare qualsiasi editor di testo, ecco i passaggi esemplificativi che mostrano come copiare e incollare la chiave usando Notepad.exe.

   1. Aprire il file della chiave privata SSH in un editor di testo. In questi passaggi viene usato il Blocco note come esempio.

   1. Scegliere Seleziona tutto dal menu **Modifica** blocco **note**.

   1. Selezionare **Modifica**  >  **Copia.**

   1. Nel trigger o nell'azione SFTP-SSH incollare la chiave copiata completa nella proprietà Chiave **privata SSH,** che supporta più righe.  **_Non immettere o modificare manualmente il tasto_**.

1. Dopo aver immesso i dettagli della connessione, selezionare **Crea.**

1. Specificare ora i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Eseguire l'override delle dimensioni del blocco

Per eseguire l'override del comportamento adattivo predefinito utilizzato dalla suddivisione in blocchi, è possibile specificare una dimensione costante del blocco da 5 MB a 50 MB.

1. Nell'angolo superiore destro dell'azione selezionare il pulsante con i puntini di sospensione (**...**) e quindi selezionare **Impostazioni**.

   ![Aprire le impostazioni SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. In **Trasferimento contenuto** nella proprietà Dimensioni **blocco** immettere un valore intero compreso tra e , `5` ad `50` esempio: 

   ![Specificare le dimensioni del blocco da usare](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Al termine, selezionare **Fine.**

## <a name="examples"></a>Esempio

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>Trigger SFTP-SSH: Quando viene aggiunto o modificato un file

Questo trigger avvia un flusso di lavoro quando un file viene aggiunto o modificato in un server SFTP. Ad esempio, le azioni di follow-up, il flusso di lavoro può usare una condizione per verificare se il contenuto del file soddisfa i criteri specificati. Se il contenuto soddisfa la condizione, l'azione Ottieni contenuto file SFTP-SSH può ottenere il contenuto e quindi un'altra azione SFTP-SSH può inserire il **file** in una cartella diversa nel server SFTP.

**Esempio riguardante un'organizzazione**: si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. È quindi possibile usare un'azione SFTP-SSH, ad esempio Ottenere il contenuto del **file,** in modo da ottenere il contenuto dell'ordine per un'ulteriore elaborazione e archiviare tale ordine in un database degli ordini.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>Azione SFTP - SSH: Ottenere il contenuto del file usando il percorso

Questa azione ottiene il contenuto da un file in un server SFTP specificando il percorso del file. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e una condizione che il contenuto del file deve soddisfare. Se la condizione è true, è possibile eseguire l'azione che recupera il contenuto.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Risolvere i problemi

In questa sezione vengono descritte le possibili soluzioni a errori o problemi comuni.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>Errore 504: "Tentativo di connessione non riuscito perché la parte connessa non ha risposto correttamente dopo un periodo di tempo oppure la connessione stabilita non è riuscita perché l'host connesso non è riuscito a rispondere" o "La richiesta al server SFTP ha richiesto più di '00:00:30' secondi"

Questo errore può verificarsi quando l'app per la logica non è in grado di stabilire correttamente una connessione con il server SFTP. Potrebbero esserci diversi motivi per questo problema, quindi provare queste opzioni di risoluzione dei problemi:

* Il timeout della connessione è di 20 secondi. Verificare che il server SFTP abbia prestazioni ottimali e che i dispositivi intermedi, ad esempio i firewall, non aggiungono sovraccarico. 

* Se è configurato un firewall, assicurarsi di aggiungere gli indirizzi **IP** del connettore gestito all'elenco approvato. Per trovare gli indirizzi IP per l'area dell'app per la logica, vedere Limiti e [configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Se questo errore si verifica in modo intermittente, modificare l'impostazione dei criteri **Riprova** nell'azione SFTP-SSH impostando un numero di tentativi superiore a quello predefinito di quattro tentativi.

* Controllare se il server SFTP inserisce un limite al numero di connessioni da ogni indirizzo IP. Se esiste un limite, potrebbe essere necessario limitare il numero di istanze simultanee dell'app per la logica.

* Per ridurre i costi di creazione della connessione, nella configurazione SSH per il server SFTP aumentare la [**proprietà ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) a circa un'ora.

* Esaminare il log del server SFTP per verificare se la richiesta dall'app per la logica ha raggiunto il server SFTP. Per ottenere altre informazioni sul problema di connettività, è anche possibile eseguire una traccia di rete nel firewall e nel server SFTP.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>Errore 404: "È stato fatto un riferimento a un file o a una cartella che non esiste"

Questo errore può verificarsi quando il flusso di lavoro crea un file nel server SFTP con l'azione Crea **file** SFTP-SSH, ma lo sposta immediatamente prima che il servizio App per la logica possa ottenere i metadati del file. Quando il flusso di lavoro esegue l'azione Crea **file,** il servizio App per la logica chiama automaticamente il server SFTP per ottenere i metadati del file. Tuttavia, se l'app per la logica sposta il file, il servizio App per la logica non è più in grado di trovare il file, quindi viene visualizzato il `404` messaggio di errore.

Se non è possibile evitare o ritardare lo spostamento del file, è possibile ignorare la lettura dei metadati del file dopo la creazione del file seguendo questa procedura:

1. **Nell'azione Crea file** aprire l'elenco **Aggiungi** nuovo parametro, selezionare la proprietà Ottieni tutti i metadati **del file** e impostare il valore su **No.**

1. Se questi metadati del file sono necessari in un secondo momento, è possibile usare **l'azione Ottieni metadati file.**

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni più tecniche su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la pagina di riferimento [del connettore](/connectors/sftpwithssh/).

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di integrazione [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore richiede invece la suddivisione in blocchi per usare i limiti dei messaggi [ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
