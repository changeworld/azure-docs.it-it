---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327153"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Aprire la finestra del terminale o di comando e passare alla directory in cui creare l'applicazione Java. Eseguire il comando seguente per generare il progetto Java dal modello maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Si noterà che l'obiettivo 'generate' ha creato una directory con lo stesso nome di artifactId. In questa directory, `src/main/java directory` contiene il codice sorgente del progetto, la directory `src/test/java` contiene l'origine di test e il file pom.xml è il modello a oggetti del progetto o [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Aggiornare il file POM dell'applicazione per usare Java 8 o versione successiva:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Aggiungere i riferimenti al pacchetto per Chat SDK

Nel file POM fare riferimento al pacchetto `azure-communication-chat` con le API di chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Per l'autenticazione, il client deve fare riferimento al pacchetto `azure-communication-common`:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Servizi di comunicazione di Azure Chat SDK per Java.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatAsyncClient | Questa classe è necessaria per la funzionalità di chat asincrona. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |
| ChatThreadAsyncClient | Questa classe è necessaria per la funzionalità di thread di chat asincrona. È possibile ottenere un'istanza tramite ChatAsyncClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat
Per creare un client di chat, si useranno l''endpoint di Servizi di comunicazione e il token di accesso generato nell'ambito dei passaggi preliminari. I token di accesso utente consentono di creare applicazioni client che eseguono l'autenticazione direttamente in Servizi di comunicazione di Azure. Dopo aver generato questi token nel server, passarli a un dispositivo client. È necessario usare la classe CommunicationTokenCredential di Common SDK per passare il token al client di chat.

Altre informazioni [sull'architettura di chat](../../../concepts/chat/concepts.md)

Quando si aggiungono le istruzioni di importazione, assicurarsi di aggiungere solo le importazioni dagli spazi dei nomi com.azure.communication.chat e com.azure.communication.chat.models e non dallo spazio dei nomi com.azure.communication.chat.implementation. Nel file App.java generato tramite Maven, è possibile usare il codice seguente per iniziare:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `createChatThread` per creare un thread di chat.
`createChatThreadOptions` viene usato per descrivere la richiesta di thread.

- Usare il `topic` parametro del costruttore per fornire un argomento a questa chat. L'argomento può essere aggiornato dopo aver creato il thread di chat usando la `UpdateThread` funzione .
- Usare `participants` per elencare i partecipanti del thread da aggiungere al thread. `ChatParticipant` accetta l'utente creato nella guida di avvio rapido [Token di accesso utente](../../access-tokens.md).

`CreateChatThreadResult` è la risposta restituita dalla creazione di un thread di chat.
Contiene un metodo che restituisce l'oggetto che può essere usato per ottenere il client del thread da cui è possibile ottenere l'oggetto per l'esecuzione di operazioni sul thread creato: aggiunta di partecipanti, invio di messaggi e `getChatThread()` `ChatThread` così `ChatThreadClient` via. `ChatThread` L'oggetto contiene anche il metodo che recupera `getId()` l'ID univoco del thread.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Elencare i thread di chat

Usare il `listChatThreads` metodo per recuperare un elenco di thread di chat esistenti.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat

Il metodo `getChatThreadClient` restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiunta di partecipanti, invio di messaggi e così via. `chatThreadId` è l'ID univoco del thread di chat esistente.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il metodo `sendMessage` per inviare un messaggio al thread appena creato, identificato da chatThreadId.
`sendChatMessageOptions` consente di descrivere la richiesta del messaggio di chat.

- Usare `content` per fornire il contenuto del messaggio di chat.
- Usare `type` per specificare il tipo di contenuto del messaggio di chat, TEXT o HTML.
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente.

Il `sendChatMessageResult` di risposta contiene un `id`, ovvero l'ID univoco del messaggio.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `listMessages` sul client del thread di chat a intervalli specificati.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando .editMessage() e .deleteMessage(). Per i messaggi eliminati, `chatMessage.getDeletedOn()` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `chatMessage.getEditedOn()` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `chatMessage.getCreatedOn()` e tale ora può essere usata per ordinare i messaggi.

Altre informazioni sui tipi di messaggio sono disponibili qui: [Tipi di messaggio.](../../../concepts/chat/concepts.md#message-types)

## <a name="send-read-receipt"></a>Inviare la conferma di lettura

Usare il `sendReadReceipt` metodo per pubblicare un evento di conferma di lettura in un thread di chat, per conto di un utente.
`chatMessageId` è l'ID univoco del messaggio di chat letto.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Elencare i partecipanti alla chat

Usare `listParticipants` per recuperare una raccolta di pagine contenente i partecipanti del thread di chat identificato da chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Aggiungere un utente come partecipante al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo gli utenti, è possibile concedere loro l'accesso per inviare messaggi al thread di chat e aggiungere/rimuovere altri partecipanti. Per iniziare, sarà necessario ottenere un nuovo token di accesso e un'identità per tale utente. Prima di chiamare il metodo addParticipants, assicurarsi di aver acquisito un nuovo token di accesso e una nuova identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare il `addParticipants` metodo per aggiungere partecipanti al thread.

- `communicationIdentifier`, obbligatorio, è l'elemento CommunicationIdentifier creato da CommunicationIdentityClient nella guida introduttiva [al token di accesso](../../access-tokens.md) utente.
- `displayName`, facoltativo, è il nome visualizzato per il partecipante del thread.
- `shareHistoryTime`, facoltativo, è l'ora a partire dalla quale la cronologia della chat viene condivisa con il partecipante. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente all'aggiunta del partecipante, impostarla sulla data corrente. Per condividere una cronologia parziale, impostarla sulla data richiesta.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Eseguire il codice

Passare alla directory contenente il file *pom.xml* e compilare il progetto tramite il comando `mvn` seguente.

```console
mvn compile
```

Compilare il pacchetto.

```console
mvn package
```

Eseguire il comando `mvn` seguente per eseguire l'app.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
