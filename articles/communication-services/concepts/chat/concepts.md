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
ms.openlocfilehash: 2360cc8d202ed29051551231d14bef69c0e66ce4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657809"
---
# <a name="chat-concepts"></a>Concetti sulle chat

Le librerie client Chat di Servizi di comunicazione di Azure possono essere usate per aggiungere chat di testo in tempo reale alle applicazioni. Questa pagina riepiloga i principali concetti e funzionalità della libreria Chat.

Per altre informazioni su linguaggi e funzionalità specifici delle librerie client, vedere [Panoramica della libreria client Chat di Servizi di comunicazione](./sdk-features.md).

## <a name="chat-overview"></a>Panoramica della chat

Le conversazioni di chat avvengono all'interno di thread. Un thread di chat può contenere numerosi messaggi e molti utenti. Ogni messaggio appartiene a un solo thread e un utente può fare parte di uno o più thread.

Ogni utente nel thread di chat è detto membro. Un thread di chat può contenere fino a 250 membri. Solo i membri di un thread di chat possono inviare e ricevere messaggi e aggiungere o rimuovere membri nel thread. La dimensione massima consentita per i messaggi è di circa 28 KB. È possibile recuperare tutti i messaggi di un thread di chat mediante l'operazione `List/Get Messages`. Servizi di comunicazione archivia la cronologia della chat fino a quando non si esegue un'operazione di eliminazione nel thread della chat o nel messaggio oppure fino a quando non ci sono più membri nel thread della chat, che viene quindi ritenuto orfano ed elaborato per l'eliminazione.

Per i thread di chat con oltre 20 membri le conferme di lettura e l'indicatore di digitazione sono disabilitati.

## <a name="chat-architecture"></a>Architettura della chat

L'architettura della chat è costituita da due parti principali: 1) servizio attendibile e 2) applicazione client.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramma che illustra l'architettura della chat di Servizi di comunicazione.":::

 - **Servizio attendibile:** per gestire correttamente una sessione di chat, è necessario un servizio che consenta di connettersi a Servizi di comunicazione usando la stringa di connessione della risorsa. Questo servizio è responsabile della creazione di thread di chat, della gestione delle appartenenze ai thread e della distribuzione di token di accesso agli utenti. Per altre informazioni sui token di accesso, vedere l'argomento di avvio rapido relativo [ai token di accesso](../../quickstarts/access-tokens.md).

 - **Applicazione client:**  l'applicazione client si connette al servizio attendibile e riceve i token di accesso da usare per connettersi direttamente a Servizi di comunicazione. Una volta stabilita la connessione, l'applicazione client può inviare e ricevere messaggi.

È consigliabile generare token di accesso usando il livello di servizio attendibile. In questo scenario il lato server è responsabile della creazione e della gestione degli utenti e dell'emissione dei relativi token.

## <a name="message-types"></a>Tipi di messaggio

La chat di Servizi di comunicazione condivide i messaggi generati dall'utente e quelli generati dal sistema, detti **attività del thread**. Le attività del thread vengono generate quando un thread di chat viene aggiornato. Quando si chiama `List Messages` o `Get Messages` su un thread di chat, il risultato conterrà sia i messaggi di testo generati dall'utente che i messaggi di sistema, in ordine cronologico. In questo modo è più facile identificare il momento in cui un membro è stato aggiunto o rimosso o in cui l'argomento del thread di chat è stato aggiornato. I tipi di messaggi supportati sono:

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

La libreria client Chat per JavaScript include la segnalazione in tempo reale, che consente ai client di restare in ascolto degli aggiornamenti in tempo reale e dei messaggi in arrivo in un thread di chat senza dover eseguire il polling delle API. Gli eventi disponibili includono:

 - `ChatMessageReceived`: quando un nuovo messaggio viene inviato a un thread di chat di cui l'utente è membro. Questo evento non viene inviato per i messaggi di sistema generati automaticamente, illustrati nell'argomento precedente.
 - `ChatMessageEdited`: quando un messaggio viene modificato in un thread di chat di cui l'utente è membro.
 - `ChatMessageDeleted`: quando un messaggio viene eliminato in un thread di chat di cui l'utente è membro.
 - `TypingIndicatorReceived`: quando un altro membro digita un messaggio in un thread di chat di cui l'utente è membro.
 - `ReadReceiptReceived`: quando un altro membro ha letto il messaggio inviato dall'utente in un thread di chat.

## <a name="chat-events"></a>Eventi di chat

La segnalazione in tempo reale consente agli utenti di chattare in tempo reale. I servizi possono usare Griglia di eventi di Azure per sottoscrivere eventi correlati alla chat. Per altre informazioni, vedere [Gestione degli eventi in Servizi di comunicazione di Azure](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Uso di Servizi cognitivi con la libreria client Chat per abilitare le funzionalità intelligenti

È possibile usare le [API Servizi cognitivi di Azure](../../../cognitive-services/index.yml) con la libreria client Chat per aggiungere funzionalità intelligenti alle applicazioni. Ad esempio, è possibile:

- Consentire agli utenti di chattare in lingue diverse.
- Consentire a un agente di supporto di classificare in ordine di priorità i ticket rilevando un sentiment negativo in relazione a un problema di un cliente.
- Analizzare i messaggi in arrivo per il rilevamento delle chiavi e il riconoscimento delle entità e richiedere all'utente informazioni pertinenti nell'app in base al contenuto del messaggio.

A questo scopo è possibile impostare il servizio attendibile come membro di un thread di chat. Si supponga di voler abilitare il servizio di traduzione. Questo servizio dovrà restare in ascolto dei messaggi scambiati da altri membri [1], chiamare le API Servizi cognitivi per tradurre il contenuto nella lingua desiderata [2,3] e inviare il risultato tradotto come messaggio nel thread di chat [4].

In questo modo la cronologia dei messaggi conterrà sia i messaggi originali che quelli tradotti. Nell'applicazione client è possibile aggiungere la logica per visualizzare il messaggio originale o quello tradotto. Per informazioni su come usare le API Servizi cognitivi per tradurre testo in lingue diverse, vedere [questa guida di avvio rapido](../../../cognitive-services/translator/quickstart-translator.md).

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramma che mostra l'interazione tra Servizi cognitivi e Servizi di comunicazione.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con la [libreria client Chat](sdk-features.md)
