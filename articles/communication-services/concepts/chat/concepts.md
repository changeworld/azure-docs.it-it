---
title: Concetti sulle chat in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui concetti relativi alla chat di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 389d2282812406c50cddf255be2219fa203b0895
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729661"
---
# <a name="chat-concepts"></a>Concetti sulle chat 

Azure Communication Services Chat SDK può essere usato per aggiungere chat di testo in tempo reale alle applicazioni. Questa pagina riepiloga i principali concetti e funzionalità della libreria Chat.    

Per ulteriori informazioni su linguaggi e funzionalità SDK specifici, vedere la [Panoramica di Communication Services Chat SDK](./sdk-features.md) .  

## <a name="chat-overview"></a>Panoramica della chat    

Le conversazioni di chat avvengono all'interno di **thread di chat**. I thread di chat hanno le proprietà seguenti:

- Un thread di chat viene identificato in modo univoco da `ChatThreadId` . 
- I thread di chat possono avere uno o più utenti come partecipanti che possono inviare messaggi. 
- Un utente può far parte di uno o più thread di chat. 
- Solo i partecipanti al thread possono accedere a un thread di chat specificato e solo possono eseguire operazioni di thread di chat. Queste operazioni includono l'invio e la ricezione di messaggi, l'aggiunta di partecipanti e la rimozione di partecipanti. 
- Gli utenti vengono aggiunti automaticamente come partecipanti a tutti i thread di chat creati.

### <a name="user-access"></a>Accesso utente
In genere, il creatore di thread e i partecipanti hanno lo stesso livello di accesso al thread e possono eseguire tutte le operazioni correlate disponibili nell'SDK, inclusa l'eliminazione. I partecipanti non dispongono dell'accesso in scrittura ai messaggi inviati da altri partecipanti, il che significa che solo il mittente del messaggio può aggiornare o eliminare i messaggi inviati. Se un altro partecipante tenta di eseguire questa operazione, riceverà un errore. 

Se si vuole limitare l'accesso alle funzionalità di chat per un set di utenti, è possibile configurare l'accesso come parte del servizio attendibile. Il servizio attendibile è il servizio che orchestra l'autenticazione e l'autorizzazione dei partecipanti alla chat. Questo aspetto verrà esaminato in dettaglio di seguito.  

### <a name="chat-data"></a>Dati della chat 
Servizi di comunicazione archivia la cronologia delle chat fino all'eliminazione esplicita I partecipanti al thread di chat possono usare `ListMessages` per visualizzare la cronologia dei messaggi per un thread specifico. Gli utenti rimossi da un thread di chat saranno in grado di visualizzare la cronologia dei messaggi precedente, ma non saranno in grado di inviare o ricevere nuovi messaggi come parte del thread di chat. Un thread completamente inattivo senza partecipanti verrà eliminato automaticamente dopo 30 giorni. Per ulteriori informazioni sui dati archiviati da servizi di comunicazione, consultare la documentazione sulla [privacy](../privacy.md).  

### <a name="service-limits"></a>Limiti del servizio  
- Il numero massimo di partecipanti consentiti in un thread di chat è 250.   
- La dimensione massima consentita per i messaggi è approssimativamente di 28 KB.  
- Per i thread di chat con più di 20 partecipanti, le conferme di lettura e le funzionalità dell'indicatore digitante non sono supportate.    

## <a name="chat-architecture"></a>Architettura della chat    

L'architettura della chat è costituita da due parti principali: 1) servizio attendibile e 2) applicazione client.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramma che illustra l'architettura della chat di Servizi di comunicazione."::: 

 - **Servizio attendibile:** Per gestire correttamente una sessione di chat, è necessario un servizio che consenta di connettersi ai servizi di comunicazione usando la stringa di connessione della risorsa. Questo servizio è responsabile della creazione di thread di chat, dell'aggiunta e della rimozione dei partecipanti e dell'emissione di token di accesso agli utenti. Per altre informazioni sui token di accesso, vedere l'argomento di avvio rapido relativo [ai token di accesso](../../quickstarts/access-tokens.md).  
 - **App client:**  L'applicazione client si connette al servizio attendibile e riceve i token di accesso usati dagli utenti per connettersi direttamente ai servizi di comunicazione. Una volta che il servizio trusted ha creato il thread di chat e aggiunto utenti come partecipanti, può usare l'app client per connettersi al thread di chat e inviare messaggi. Usare la funzionalità di notifiche in tempo reale, illustrata di seguito, nell'app client per sottoscrivere gli aggiornamenti del messaggio & thread da altri partecipanti.
    
        
## <a name="message-types"></a>Tipi di messaggio    

Come parte della cronologia dei messaggi, la chat condivide i messaggi generati dall'utente e i messaggi generati dal sistema. I messaggi di sistema vengono generati quando un thread di chat viene aggiornato e possono aiutare a identificare il momento in cui un partecipante è stato aggiunto o rimosso o quando l'argomento del thread di chat è stato aggiornato. Quando si chiama `List Messages` o `Get Messages` su un thread di chat, il risultato conterrà entrambi i tipi di messaggi in ordine cronologico.

Per i messaggi generati dall'utente, il tipo di messaggio può essere impostato in `SendMessageOptions` quando si invia un messaggio al thread di chat. Se non viene specificato alcun valore, i servizi di comunicazione utilizzeranno per impostazione predefinita il `text` tipo. L'impostazione di questo valore è importante quando si invia codice HTML. Quando `html` si specifica, servizi di comunicazione purifica il contenuto per garantirne il rendering sicuro nei dispositivi client.
 - `text`: Un messaggio di testo normale composto e inviato da un utente come parte di un thread di chat. 
 - `html`: Messaggio formattato che usa HTML, composto e inviato da un utente come parte del thread di chat. 

Tipi di messaggi di sistema: 
 - `participantAdded`: Messaggio di sistema che indica che uno o più partecipanti sono stati aggiunti al thread di chat.
 - `participantRemoved`: Messaggio di sistema che indica che un partecipante è stato rimosso dal thread di chat.
 - `topicUpdated`: Messaggio di sistema che indica che l'argomento thread è stato aggiornato.

## <a name="real-time-notifications"></a>Notifiche in tempo reale  

Alcuni SDK (ad esempio JavaScript Chat SDK) supportano le notifiche in tempo reale. Questa funzionalità consente ai client di ascoltare i servizi di comunicazione per gli aggiornamenti in tempo reale e i messaggi in arrivo in un thread di chat senza dover eseguire il polling delle API. L'app client può sottoscrivere gli eventi seguenti:
 - `chatMessageReceived` -Quando un nuovo messaggio viene inviato a un thread di chat da un partecipante.
 - `chatMessageEdited` -Quando un messaggio viene modificato in un thread di chat. 
 - `chatMessageDeleted` -Quando un messaggio viene eliminato in un thread di chat.   
 - `typingIndicatorReceived` -Quando un altro partecipante invia un indicatore di digitazione al thread di chat.    
 - `readReceiptReceived` -Quando un altro partecipante invia una ricevuta di lettura per un messaggio che ha letto.  
 - `chatThreadCreated` -Quando un thread di chat viene creato da un utente di servizi di comunicazione.    
 - `chatThreadDeleted` -Quando un thread di chat viene eliminato da un utente di servizi di comunicazione.    
 - `chatThreadPropertiesUpdated` -Quando vengono aggiornate le proprietà del thread di chat; Attualmente, è supportato solo l'aggiornamento dell'argomento per il thread. 
 - `participantsAdded` -Quando un utente viene aggiunto come partecipante a un thread di chat.     
 - `participantsRemoved` -Quando un partecipante esistente viene rimosso dal thread di chat.

È possibile usare le notifiche in tempo reale per offrire un'esperienza di chat in tempo reale agli utenti. Per inviare notifiche push per i messaggi persi dagli utenti mentre erano lontani, i servizi di comunicazione si integrano con griglia di eventi di Azure per pubblicare eventi correlati alla chat (operazione post) che possono essere inseriti nel servizio di notifica dell'app personalizzato. Per ulteriori informazioni, vedere [eventi del server](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Crea esperienze di chat intelligenti e basate su intelligenza artificiale   

È possibile usare le [API cognitive di Azure](../../../cognitive-services/index.yml) con la chat SDK per compilare casi d'uso, ad esempio:

- Consentire agli utenti di chattare in lingue diverse.  
- Consentire a un agente di supporto di assegnare la priorità ai ticket rilevando un sentimento negativo di un messaggio in arrivo da un cliente. 
- Analizzare i messaggi in arrivo per il rilevamento delle chiavi e il riconoscimento delle entità e richiedere all'utente informazioni pertinenti nell'app in base al contenuto del messaggio.

Un modo per ottenere questo risultato consiste nel fare in modo che il servizio attendibile funga da partecipante a un thread di chat. Si supponga di voler abilitare il servizio di traduzione. Questo servizio sarà responsabile dell'ascolto dei messaggi scambiati da altri partecipanti [1], chiamando le API cognitive per tradurre il contenuto nella lingua desiderata [2, 3] e inviando il risultato tradotto come messaggio nel thread di chat [4].

In questo modo la cronologia dei messaggi conterrà sia i messaggi originali che quelli tradotti. Nell'applicazione client è possibile aggiungere la logica per visualizzare il messaggio originale o quello tradotto. Per informazioni su come usare le API Servizi cognitivi per tradurre testo in lingue diverse, vedere [questa guida di avvio rapido](../../../cognitive-services/translator/quickstart-translator.md). 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramma che mostra l'interazione tra Servizi cognitivi e Servizi di comunicazione."::: 

## <a name="next-steps"></a>Passaggi successivi   

> [!div class="nextstepaction"] 
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)    

I documenti seguenti possono essere interessanti:  
- Acquisire familiarità con l' [SDK della chat](sdk-features.md)
