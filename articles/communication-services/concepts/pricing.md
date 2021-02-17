---
title: Scenari relativi ai prezzi per chiamate (vocali/video) e chat
titleSuffix: An Azure Communication Services concept document
description: Informazioni sul modello di determinazione prezzi di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 883be25716d94130984baef5e7a1eab80888a2b9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558466"
---
# <a name="pricing-scenarios"></a>Scenari relativi ai prezzi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


I prezzi per Servizi di comunicazione di Azure sono in genere basati su un modello con pagamento in base al consumo. I prezzi negli esempi seguenti sono a scopo illustrativo e potrebbero non riflettere i prezzi di Azure più recenti.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chiamate vocali/video e condivisione dello schermo

Servizi di comunicazione di Azure consente l'aggiunta di chiamate vocali/video e della condivisione dello schermo alle applicazioni. È possibile incorporare l'esperienza nelle applicazioni usando le librerie client JavaScript, Objective-C (Apple), Java (Android) o .NET. Vedere l'[elenco completo delle librerie client disponibili](./sdk-options.md).

### <a name="pricing"></a>Prezzi

I servizi di chiamata e condivisione dello schermo vengono addebitati al costo di 0,004 USD al minuto per ogni partecipante per le chiamate di gruppo. Per comprendere i vari flussi di chiamate possibili, vedere [questa pagina](./call-flows.md).

Per ogni partecipante alla chiamata verrà conteggiato in fattura il numero di minuti di connessione alla chiamata. Questo criterio vale indipendentemente dal fatto che l'utente effettui una chiamata video, una chiamata vocale o la condivisione dello schermo.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Esempio di prezzi: chiamata vocale/video di gruppo tramite le librerie client JS e iOS

Alice ha effettuato una chiamata di gruppo con i colleghi Bob e Charlie. Alice e Bob hanno usato le librerie client JS, mentre Charlie ha usato le librerie client iOS.

- La chiamata è durata complessivamente 60 minuti.
- Alice e Bob hanno partecipato all'intera chiamata. Alice ha attivato il video per cinque minuti e ha condiviso lo schermo per 23 minuti. Bob ha tenuto il video attivo per l'intera chiamata (60 minuti) e ha condiviso lo schermo per 12 minuti.
- Charlie ha abbandonato la chiamata dopo 43 minuti. Charlie ha usato audio e video per l'intervallo di tempo in cui è stato presente (43 minuti).

**Calcoli dei costi**

- 2 partecipanti x 60 minuti x 0,004 USD per partecipante al minuto = 0,48 USD [video e audio vengono addebitati alla stessa tariffa]
- 1 partecipante x 43 minuti x 0,004 USD per partecipante al minuto = 0,172 USD [video e audio vengono addebitati alla stessa tariffa]

**Costo totale della chiamata di gruppo**: 0,48 USD + 0,172 USD = 0,652 USD

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Esempio di prezzi: un utente della libreria client di Communication Services JS partecipa a una riunione pianificata Microsoft Teams

Alice è una riunione del dottore con il suo paziente, Bob. Alice si unirà alla visita dell'applicazione desktop teams. Bob riceverà un collegamento per partecipare usando il sito Web del provider sanitario, che si connette alla riunione usando la libreria client JS di Communication Services. Bob userà il suo telefono cellulare per partecipare alla riunione usando un browser Web (iPhone con Safari). La chat sarà disponibile durante la visita virtuale. 

- La chiamata dura un totale di 30 minuti.
- Alice e Bob partecipano all'intera chiamata. Alice accende il video cinque minuti dopo l'avvio della chiamata e condivide lo schermo per 13 minuti. Bob ha il video su per l'intera chiamata.
- Alice invia cinque messaggi, Bob risponde con tre messaggi.


**Calcoli dei costi**

- 1 partecipante (Bob) x 30 minuti x $0,004 per partecipante al minuto = $0,12 [sia il video che l'audio vengono addebitati alla stessa tariffa]
- 1 partecipante (Alice) x 30 minuti x $0,000 per partecipante al minuto = $0,0 *. 
- 1 partecipante (Bob) x 3 messaggi di chat x $0,0008 = $0,0024.
- 1 partecipante (Alice) x 5 messaggi di chat x $0,000 = $0,0 *. 

* La partecipazione di Alice è coperta dalla licenza dei team. Con la fattura di Azure verranno visualizzati i minuti e i messaggi di chat che i team hanno avuto con gli utenti di servizi di comunicazione per praticità, ma i minuti e i messaggi originati dal client dei team non costeranno.

**Costo totale per la visita**: 
- Aggiunta dell'utente tramite la libreria client JS di Communication Services: $0,12 + $0,0024 = $0,1224
- Aggiunta di un utente all'applicazione desktop teams: $0 (coperta dalla licenza Teams) 


## <a name="chat"></a>Chat

Con servizi di comunicazione è possibile migliorare l'applicazione con la possibilità di inviare e ricevere messaggi di chat tra due o più utenti. Le librerie client di chat sono disponibili per JavaScript, .NET, Python e Java. Vedere [questa pagina per informazioni sulle librerie client](./sdk-options.md).

### <a name="price"></a>Prezzo

Vengono addebitati $ 0,0008 per ogni messaggio di chat inviato.

### <a name="pricing-example-chat-between-two-users"></a>Esempio di prezzi: chat tra due utenti 

Geeta avvia un thread di chat con Emily per condividere un aggiornamento e inviare 5 messaggi. La chat dura 10 minuti. Gita e Emily inviano altri 15 messaggi ciascuno.

**Calcoli dei costi** 
- Numero di messaggi inviati (5 + 15 + 15) x 0,0008 USD = 0,028 USD

### <a name="pricing-example-group-chat-with-multiple-users"></a>Esempio di prezzi: chat di gruppo con più utenti 

Charlie avvia un thread di chat con gli amici Casey e Jasmine per pianificare una vacanza. Chattano per un intervallo di tempo durante il quale Charlie, Casey e Jasmine inviano rispettivamente 20, 30 e 18 messaggi. Si rendono conto che anche la loro amica Rose potrebbe essere interessata al viaggio, quindi l'aggiungono al thread di chat e condividono con lei tutta la cronologia dei messaggi. 

Rose visualizza i messaggi e inizia a chattare. Nel frattempo, Casey riceve una chiamata e decide di riprendere la conversazione in un secondo momento. Charlie, Jasmine e Rose decidono le date del viaggio e inviano rispettivamente altri 30, 25 e 35 messaggi.

**Calcoli dei costi** 

- Numero di messaggi inviati (20 + 30 + 18 + 30 + 25 + 35) x 0,0008 USD = 0,1264 USD


## <a name="telephony-and-sms"></a>Telefonia e SMS

## <a name="price"></a>Prezzo 

Per i servizi di telefonia i prezzi vengono calcolati al minuto, mentre per gli SMS in base a singolo messaggio. I prezzi vengono determinati in base al tipo e alla località del numero in uso, oltre che alla destinazione delle chiamate e dei messaggi SMS.

### <a name="telephone-number-leasing"></a>Leasing di numeri di telefono

Le tariffe per il leasing di numeri di telefono vengono addebitate in anticipo e quindi ricorrono su base mensile:

|Tipo di numero   |Tariffa mensile   |
|--------------|-----------|
|Locale (Stati Uniti)     |$ 1/mese        |
|Numero verde (Stati Uniti) |$ 2/mese |


### <a name="telephone-calling"></a>Chiamate telefoniche

Le tradizionali chiamate telefoniche, ossia quelle effettuate tramite rete PSTN (Public Switched Telephone Network) sono disponibili con pagamento in base al consumo per i numeri di telefono degli Stati Uniti. Il prezzo è un addebito al minuto basato sul tipo di numero usato e sulla destinazione della chiamata. La tabella seguente include i dettagli relativi ai prezzi per le destinazioni più comuni delle chiamate. Per l'elenco completo di destinazioni, vedere l'[elenco dettagliato dei prezzi](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv).


#### <a name="united-states-calling-prices"></a>Prezzi per le chiamate negli Stati Uniti

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Tipo di numero   |Per effettuare chiamate   |Per ricevere chiamate|
|--------------|-----------|------------|
|Locale     |A partire da $ 0,013/min       |$ 0,0085/min        |
|Numero verde |$ 0,013/min   |$ 0,0220/min |

#### <a name="other-calling-destinations"></a>Altre destinazioni delle chiamate

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Per effettuare chiamate a   |Prezzo al minuto|
|-----------|------------|
|Canada     |A partire da $ 0,013/min   |
|Regno Unito     |A partire da $ 0,015/min   |
|Germania     |A partire da $ 0,015/min   |
|Francia     |A partire da $ 0,016/min   |


### <a name="sms"></a>SMS

Per gli SMS è disponibile il pagamento in base al consumo. Il prezzo è un addebito per messaggio basato sulla destinazione del messaggio. I messaggi possono essere inviati da numeri verdi a numeri di telefono situati all'interno degli Stati Uniti. Si noti che i numeri di telefono locali (geografici) non possono essere usati per l'invio di messaggi SMS.

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Paese   |Inviare messaggi|Ricevere messaggi|
|-----------|------------|------------|
|USA (numero verde)    |$ 0,0075/messaggio   | $ 0,0075/messaggio |
