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
ms.openlocfilehash: e05bf1df503a13efc8e4ca30b3341216e01e678e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110832"
---
# <a name="chat-concepts"></a>Concetti sulle chat 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Azure Communication Services Chat SDK può essere usato per aggiungere chat di testo in tempo reale alle applicazioni. Questa pagina riepiloga i principali concetti e funzionalità della libreria Chat.    

Per ulteriori informazioni su linguaggi e funzionalità SDK specifici, vedere la [Panoramica di Communication Services Chat SDK](./sdk-features.md) .  

## <a name="chat-overview"></a>Panoramica della chat    

Le conversazioni di chat avvengono all'interno di thread. Un thread di chat può contenere numerosi messaggi e molti utenti. Ogni messaggio appartiene a un solo thread e un utente può fare parte di uno o più thread. Ogni utente nel thread di chat viene chiamato partecipante. Solo i partecipanti ai thread possono inviare e ricevere messaggi e aggiungere o rimuovere altri utenti in un thread di chat. Servizi di comunicazione archivia la cronologia delle chat fino a quando non si esegue un'operazione di eliminazione sul thread o sul messaggio di chat oppure finché non ci sono partecipanti al thread di chat, a quel punto il thread di chat è orfano e accodato per l'eliminazione. 
    
## <a name="service-limits"></a>Limiti del servizio   

- Il numero massimo di partecipanti consentiti in un thread di chat è 250.   
- La dimensione massima consentita per i messaggi è approssimativamente di 28 KB.  
- Per i thread di chat con più di 20 partecipanti, le conferme di lettura e le funzionalità dell'indicatore digitante non sono supportate.    
- 
## <a name="chat-architecture"></a>Architettura della chat    

L'architettura della chat è costituita da due parti principali: 1) servizio attendibile e 2) applicazione client.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramma che illustra l'architettura della chat di Servizi di comunicazione."::: 

 - **Servizio attendibile:** Per gestire correttamente una sessione di chat, è necessario un servizio che consenta di connettersi ai servizi di comunicazione usando la stringa di connessione della risorsa. Questo servizio è responsabile della creazione di thread di chat, della gestione degli elenchi dei partecipanti ai thread e dell'invio di token di accesso agli utenti. Per altre informazioni sui token di accesso, vedere l'argomento di avvio rapido relativo [ai token di accesso](../../quickstarts/access-tokens.md).   
 - **Applicazione client:**  l'applicazione client si connette al servizio attendibile e riceve i token di accesso da usare per connettersi direttamente a Servizi di comunicazione. Una volta stabilita la connessione, l'applicazione client può inviare e ricevere messaggi.   
È consigliabile generare token di accesso usando il livello di servizio attendibile. In questo scenario il lato server è responsabile della creazione e della gestione degli utenti e dell'emissione dei relativi token.   
        
## <a name="message-types"></a>Tipi di messaggio    

La chat di Servizi di comunicazione condivide i messaggi generati dall'utente e quelli generati dal sistema, detti **attività del thread**. Le attività del thread vengono generate quando un thread di chat viene aggiornato. Quando si chiama `List Messages` o `Get Messages` su un thread di chat, il risultato conterrà sia i messaggi di testo generati dall'utente che i messaggi di sistema, in ordine cronologico. Ciò consente di identificare il momento in cui un partecipante è stato aggiunto o rimosso o quando l'argomento del thread di chat è stato aggiornato. I tipi di messaggi supportati sono:  
    
 - `Text`: messaggio di testo normale composto e inviato da un utente nell'ambito di una conversazione di chat. 
 - `RichText/HTML`: messaggio di testo formattato. Tenere presente che attualmente gli utenti di Servizi di comunicazione non possono inviare messaggi in formato RTF. Questo tipo di messaggio è supportato per i messaggi inviati da utenti di Teams a utenti di Servizi di comunicazione in scenari di interoperabilità di Teams.   
 - `ThreadActivity/ParticipantAdded`: Un messaggio di sistema che indica che uno o più partecipanti sono stati aggiunti al thread di chat. Ad esempio: 


``` 
{   
            "id": "1613589626560",  
            "type": "participantAdded", 
            "sequenceId": "7",  
            "version": "1613589626560", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:26Z" 
        }   
``` 

- `ThreadActivity/ParticipantRemoved`: Messaggio di sistema che indica che un partecipante è stato rimosso dal thread di chat. Ad esempio:  

``` 
{   
            "id": "1613589627603",  
            "type": "participantRemoved",   
            "sequenceId": "8",  
            "version": "1613589627603", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:27Z" 
        }   
``` 

- `ThreadActivity/TopicUpdate`: Messaggio di sistema che indica che l'argomento thread è stato aggiornato. Ad esempio:   
``` 
{   
            "id": "1613589623037",  
            "type": "topicUpdated", 
            "sequenceId": "2",  
            "version": "1613589623037", 
            "content":  
            {   
                "topic": "New topic",   
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:23Z" 
        }   
``` 

## <a name="real-time-signaling"></a>Segnalazione in tempo reale  

La chat JavaScript SDK include la segnalazione in tempo reale. che consente ai client di restare in ascolto degli aggiornamenti in tempo reale e dei messaggi in arrivo in un thread di chat senza dover eseguire il polling delle API. Gli eventi disponibili includono:

 - `ChatMessageReceived` -Quando un nuovo messaggio viene inviato a un thread di chat. Questo evento non viene inviato per i messaggi di sistema generati automaticamente, descritti nell'argomento precedente.   
 - `ChatMessageEdited` -Quando un messaggio viene modificato in un thread di chat. 
 - `ChatMessageDeleted` -Quando un messaggio viene eliminato in un thread di chat.   
 - `TypingIndicatorReceived` -Quando un altro partecipante sta digitando un messaggio in un thread di chat.   
 - `ReadReceiptReceived` -Quando un altro partecipante ha letto il messaggio inviato da un utente in un thread di chat.     
 - `ChatThreadCreated` -Quando un thread di chat viene creato da un utente di comunicazione. 
 - `ChatThreadDeleted` -Quando un thread di chat viene eliminato da un utente di comunicazione. 
 - `ChatThreadPropertiesUpdated` -Quando vengono aggiornate le proprietà del thread di chat; Attualmente è supportato solo l'aggiornamento dell'argomento per il thread.   
 - `ParticipantsAdded` -Quando un utente viene aggiunto come partecipante a un thread di chat.  
 - `ParticipantsRemoved` -Quando un partecipante esistente viene rimosso dal thread di chat.


## <a name="chat-events"></a>Eventi di chat  

La segnalazione in tempo reale consente agli utenti di chattare in tempo reale. I servizi possono usare Griglia di eventi di Azure per sottoscrivere eventi correlati alla chat. Per altre informazioni, vedere [Gestione degli eventi in Servizi di comunicazione di Azure](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?tabs=event-grid-event-schema).


## <a name="using-cognitive-services-with-chat-sdk-to-enable-intelligent-features"></a>Uso di servizi cognitivi con Chat SDK per abilitare funzionalità intelligenti    

È possibile usare le [API cognitive di Azure](../../../cognitive-services/index.yml) con la chat SDK per aggiungere funzionalità intelligenti alle applicazioni. Ad esempio, è possibile: 

- Consentire agli utenti di chattare in lingue diverse.  
- Consentire a un agente di supporto di classificare in ordine di priorità i ticket rilevando un sentiment negativo in relazione a un problema di un cliente.   
- Analizzare i messaggi in arrivo per il rilevamento delle chiavi e il riconoscimento delle entità e richiedere all'utente informazioni pertinenti nell'app in base al contenuto del messaggio.

Un modo per ottenere questo risultato consiste nel fare in modo che il servizio attendibile funga da partecipante a un thread di chat. Si supponga di voler abilitare il servizio di traduzione. Questo servizio sarà responsabile dell'ascolto dei messaggi scambiati da altri partecipanti [1], chiamando le API cognitive per tradurre il contenuto nella lingua desiderata [2, 3] e inviando il risultato tradotto come messaggio nel thread di chat [4].

In questo modo la cronologia dei messaggi conterrà sia i messaggi originali che quelli tradotti. Nell'applicazione client è possibile aggiungere la logica per visualizzare il messaggio originale o quello tradotto. Per informazioni su come usare le API Servizi cognitivi per tradurre testo in lingue diverse, vedere [questa guida di avvio rapido](../../../cognitive-services/translator/quickstart-translator.md). 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramma che mostra l'interazione tra Servizi cognitivi e Servizi di comunicazione."::: 

## <a name="next-steps"></a>Passaggi successivi   

> [!div class="nextstepaction"] 
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)    

I documenti seguenti possono essere interessanti:  
- Acquisire familiarità con l' [SDK della chat](sdk-features.md)