---
title: 'Avvio rapido: Partecipare a una riunione di Teams'
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578044"
---
## <a name="join-the-meeting-chat"></a>Partecipare alla chat della riunione 

Quando è abilitata l'interoperabilità di Teams, un utente di Servizi di comunicazione può partecipare alla chiamata di Teams come utente guest usando la libreria client per le chiamate. Partecipando alla chiamata, l'utente viene aggiunto anche come partecipante alla chat, dove può inviare e ricevere messaggi con altri utenti che partecipano alla chiamata. L'utente non ha però accesso ai messaggi della chat inviati prima che venisse aggiunto alla chiamata. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Ottenere un thread di chat della riunione di Teams per un utente di Servizi di comunicazione

Creare prima di tutto un'istanza di `ChatThreadClient` per il thread di chat della riunione. Analizzare il collegamento alla riunione oppure usare le API Graph con l'ID riunione per ottenere l'ID del thread. 

- Un collegamento a una riunione di Teams ha un aspetto simile al seguente: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. L'ID del thread si trova nella posizione in cui si trova `meeting_chat_thread_id` in questo collegamento. 
- Se si dispone dell'ID della riunione, è possibile usare l'[API Graph](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) per ottenere l'ID del thread. La risposta [GET API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) includerà un oggetto `chatInfo` che contiene il `threadID`. 

Una volta ottenuto l'ID del thread della chat, è possibile ottenere il client di thread delle chat usando la libreria client di chat JavaScript: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient` può essere usato per visualizzare l'elenco dei membri nel thread della chat, ottenere la cronologia della chat e inviare messaggi.  

## <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi  

Usare `SendMessage` per inviare un messaggio alla chat della riunione. Per la ricezione dei messaggi in arrivo, la sottoscrizione di eventi come `chatMessageReceived` non è supportata in quanto la segnalazione in tempo reale non è ancora abilitata per questo scenario. Per ottenere i messaggi più recenti, è possibile eseguire il polling dell'API `ListMessages`. Per lo scenario di interoperabilità, l'API `ListMessages` supporta ora la restituzione di tre nuovi tipi di messaggi:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Per altre informazioni sui tipi di messaggio, vedere [questo articolo](../../../concepts/chat/concepts.md). 

**Nota**: attualmente sono supportati solo l'invio e la ricezione di messaggi per gli scenari di interoperabilità con Teams. Altre funzionalità, come gli indicatori di digitazione e la possibilità per gli utenti di Servizi di comunicazione di aggiungere o rimuovere altri utenti dalla riunione di Teams, non sono ancora supportate.  

 
