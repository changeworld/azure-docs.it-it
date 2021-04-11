---
title: Connettersi al server IBM MQ
description: Inviare e recuperare messaggi con un server IBM MQ di Azure o locale e App per la logica di Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 03/10/2021
tags: connectors
ms.openlocfilehash: a07eb6e592c68794f0e4038a7cf9a42bd396b47a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495233"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Connettersi a un server IBM MQ da App per la logica di Azure

Il connettore MQ Invia e recupera i messaggi archiviati in un server MQ locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. Questo articolo fornisce una guida introduttiva sull'uso del connettore MQ. Per iniziare, visualizzare un singolo messaggio in una coda e quindi provare altre azioni.

Il connettore MQ include queste azioni, ma non fornisce trigger:

- Sfogliare un singolo messaggio senza eliminare il messaggio dal server MQ.
- Sfogliare un batch di messaggi senza eliminare i messaggi dal server MQ.
- Ricevere un singolo messaggio ed eliminare il messaggio dal server MQ.
- Ricevere un batch di messaggi ed eliminare i messaggi dal server MQ.
- Inviare un singolo messaggio al server MQ.

Ecco le versioni di IBM WebSphere MQ ufficialmente supportate:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, è necessario [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un server all'interno della rete.

  > [!NOTE]
  > Se il server MQ è disponibile pubblicamente o all'interno di Azure, non è necessario usare il gateway dati.

  * Per il funzionamento del connettore MQ, è necessario che nel server in cui si installa il gateway dati locale sia installato anche .NET Framework 4,6.
  
  * Dopo aver installato il gateway dati locale, è anche necessario [creare una risorsa del gateway di Azure per il gateway dati locale](../logic-apps/logic-apps-gateway-connection.md) usato dal connettore mq per accedere al server mq locale.

* App per la logica in cui si vuole usare il connettore MQ. Il connettore MQ non contiene trigger, quindi è necessario aggiungerne prima uno all'app per la logica. Ad esempio, è possibile usare il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md). Se non si ha familiarità con le app per la logica, vedere [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limitations"></a>Limitazioni

Il connettore MQ non supporta o usa il campo **Format** del messaggio e non esegue alcuna conversione di set di caratteri. Il connettore inserisce solo i dati che vengono visualizzati nel campo del messaggio in un messaggio JSON e invia il messaggio lungo.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Creare la connessione MQ

Se la connessione MQ non è già disponibile quando si aggiunge un'azione MQ, viene richiesto di crearla, ad esempio:

![Specificare le informazioni di connessione](media/connectors-create-api-mq/connection-properties.png)

1. Se ci si connette a un server MQ locale, selezionare **Connetti tramite gateway dati locale**.

1. Specificare le informazioni di connessione per il server MQ.

   * In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta.

   * Per usare Transport Layer Security (TLS) o Secure Sockets Layer (SSL), selezionare **Abilita SSL?**.

     Il connettore MQ attualmente supporta solo l'autenticazione server e non l'autenticazione client. Per altre informazioni, vedere [Problemi di connessione e autenticazione](#connection-problems).

1. Nella sezione **gateway** seguire questa procedura:

   1. Dall'elenco **sottoscrizione** selezionare la sottoscrizione di Azure associata alla risorsa del gateway di Azure.

   1. Nell'elenco **Gateway connessione** selezionare la risorsa gateway di Azure da usare.

1. Al termine, selezionare **Crea**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemi di connessione e autenticazione

Quando l'app per la logica tenta la connessione al server MQ locale, è possibile che si riceva questo errore:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se si usa il connettore MQ direttamente in Azure, il server MQ deve usare un certificato emesso da una [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/) attendibile.

* Se si usa il gateway dati locale, provare a usare un certificato emesso da una [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/) attendibile laddove possibile. Tuttavia, se questa opzione non è fattibile, è possibile usare un certificato autofirmato, che non viene emesso da una [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/) attendibile ed è considerato meno sicuro.

  Per installare il certificato autofirmato del server, è possibile usare lo strumento di **gestione certificati Windows** (certmgr.msc). Per questo scenario, nel computer locale in cui è in esecuzione il servizio gateway dati locale, è necessario installare il certificato nell'archivio certificati del **computer locale** al livello di **Autorità di certificazione radice attendibili**.

  1. Nel computer in cui è in esecuzione il servizio gateway dati locale, aprire il menu Start, quindi trovare e selezionare **Gestire i certificati utente**.

  1. Dopo aver aperto lo strumento di gestione certificati Windows, passare alla cartella **Certificati - Computer locale** >  **Autorità di certificazione radice attendibili** e installare il certificato.

     > [!IMPORTANT]
     > Assicurarsi di installare il certificato nell'archivio **Certificati - Computer locale** > **Autorità di certificazione radice attendibili**.

* Il server MQ richiede la definizione della specifica di crittografia che si desidera utilizzare per le connessioni TLS/SSL. Tuttavia, SslStream in .NET non consente di specificare l'ordine per le specifiche di crittografia. Per ovviare a questa limitazione, è possibile modificare la configurazione del server MQ in modo che corrisponda alla prima specifica di crittografia nel gruppo inviato dal connettore nella negoziazione TLS/SSL.

  Quando si prova a stabilire la connessione, il server MQ registra un messaggio di evento che indica che la connessione non è riuscita perché l'altra estremità usa una specifica di crittografia non corretta. Il messaggio di evento contiene la specifica di crittografia visualizzata per prima nell'elenco. Aggiornare la specifica di crittografia nella configurazione del canale in modo che corrisponda a quella indicata nel messaggio di evento.

## <a name="browse-single-message"></a>Visualizzare un singolo messaggio

1. Nell'app per la logica, sotto il trigger o un'altra azione, selezionare **Nuovo passaggio**.

1. Nella casella di ricerca immettere `mq` e selezionare l'azione **Esplora messaggio**.

   ![Selezionare l'azione "Esplora messaggio"](media/connectors-create-api-mq/browse-message.png)

1. Se non è già stato fatto, viene richiesto di [creare la connessione MQ](#create-connection).

1. Dopo aver creato la connessione, configurare le proprietà dell'azione **Esplora messaggio**:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Coda** | Specificare la coda, se è diversa da quella indicata nella connessione. |
   | **MessageId**, **CorrelationId**, **GroupId** e altre proprietà | Ricerca di un messaggio basato sulle diverse proprietà dei messaggi MQ |
   | **IncludeInfo** | Per includere informazioni aggiuntive sui messaggi nell'output, selezionare **true**. Per omettere informazioni aggiuntive sui messaggi nell'output, selezionare **false**. |
   | **Timeout** | Immettere un valore per impostare la durata dell'attesa dell'arrivo di un messaggio in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio. |
   |||

   Ad esempio:

   ![Proprietà per l'azione "Esplora messaggio"](media/connectors-create-api-mq/browse-message-properties.png)

1. Al termine, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. Per testare l'app, selezionare **Esegui**.

   Al termine dell'esecuzione, nella finestra di progettazione vengono visualizzati i passaggi del flusso di lavoro e il relativo stato ed è quindi possibile esaminare l'output.

1. Per visualizzare i dettagli di ogni passaggio, fare clic sulla relativa barra del titolo. Per esaminare altre informazioni sull'output di un passaggio, selezionare **Mostra output non elaborati**.

   ![Visualizzare l'output del messaggio](media/connectors-create-api-mq/browse-message-output.png)

   Ecco un output non elaborato di esempio:

   ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se si imposta **IncludeInfo** su **true**, viene visualizzato un output aggiuntivo:

   ![IncludeInfo nella visualizzazione di un messaggio](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi

L'azione **Esplora messaggi** include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda. Se **BatchSize** non include valori, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Seguire la procedura precedente, ma aggiungere invece l'azione **Esplora messaggi**.

1. Se non è già stato fatto, viene richiesto di [creare la connessione MQ](#create-connection). In caso contrario, per impostazione predefinita viene usata la prima connessione configurata in precedenza. Per creare una nuova connessione, selezionare **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Specificare le informazioni per l'azione.

1. Salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, ecco un output di esempio dell'azione **Esplora messaggi**:

   ![Esempio di output di "Esplora messaggi"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Ricevere un singolo messaggio

L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si usa l'azione **Ricevi messaggio**, il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi

L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si usa **Ricevi messaggi**, i messaggi vengono eliminati dalla coda.

> [!NOTE]
> Se si esegue un'azione di esplorazione o ricezione in una coda che non include messaggi, l'operazione non riesce con questo output:
>
> ![Errore "nessun messaggio" di MQ](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Send message

1. Seguire la procedura precedente, ma aggiungere invece l'azione **Invia messaggio**.

1. Se non è già stato fatto, viene richiesto di [creare la connessione MQ](#create-connection). In caso contrario, per impostazione predefinita viene usata la prima connessione configurata in precedenza. Per creare una nuova connessione, selezionare **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Specificare le informazioni per l'azione. Per **MessageType** selezionare un tipo di messaggio valido: **Datagram**, **Reply** o **Request**

   ![Proprietà per l'azione "Invia messaggio"](media/connectors-create-api-mq/send-message-properties.png)

1. Salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, ecco un output di esempio dell'azione **Invia messaggio**:

   ![Esempio di output di "Invia messaggio"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio azioni e limiti, descritti nel file di spavalderia del connettore, vedere la [pagina di riferimento del connettore](/connectors/mq/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
