---
title: Panoramica di Chat SDK per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni su Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110815"
---
# <a name="chat-sdk-overview"></a>Panoramica di Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Azure Communication Services Chat SDK può essere usato per aggiungere chat avanzate e in tempo reale alle applicazioni.
    
## <a name="chat-sdk-capabilities"></a>Funzionalità di Chat SDK    

Nell'elenco seguente viene presentato il set di funzionalità attualmente disponibili negli SDK di chat di Communication Services.  

| Gruppo di funzionalità | Funzionalità | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Funzionalità principali | Creare un thread di chat tra 2 o più utenti (fino a 250 utenti)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aggiornare l'argomento di un thread di chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Aggiunta o rimozione di partecipanti da un thread di chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Scegliere se condividere la cronologia dei messaggi di chat con il partecipante aggiunto                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Ottenere un elenco di partecipanti in un thread di chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Eliminare un thread di chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dato un utente di comunicazione, ottenere l'elenco dei thread di chat di cui l'utente fa parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Ottenere informazioni per un thread di chat specifico                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Inviare e ricevere messaggi in un thread di chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Modificare il contenuto di un messaggio inviato                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Eliminare un messaggio                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Conferme di lettura per i messaggi che sono stati letti da altri partecipanti in una chat <br/> *Non disponibile quando sono presenti più di 20 partecipanti in un thread di chat*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ricevere una notifica quando i partecipanti digitano attivamente un messaggio in un thread di chat <br/> *Non disponibile quando sono presenti più di 20 membri in un thread di chat*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Ottenere tutti i messaggi in un thread di chat <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Invia emoji Unicode come parte del contenuto del messaggio                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Segnalazione in tempo reale (abilitata dal pacchetto di segnalazione proprietario * *)|  Sottoscrivere per ottenere gli aggiornamenti in tempo reale per i messaggi in arrivo e altre operazioni nell'app di chat. Per visualizzare un elenco degli aggiornamenti supportati per la segnalazione in tempo reale, vedere [concetti relativi alla chat](concepts.md#real-time-signaling)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Supporto griglia di eventi             | Usare l'integrazione con griglia di eventi di Azure e configurare il servizio di comunicazione per eseguire la logica di business in base alle attività di chat o per collegare un servizio di notifica push personalizzato   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Monitoraggio        | Usare le metriche delle richieste API emesse nel portale di Azure per compilare dashboard, monitorare l'integrità dell'app di chat e impostare gli avvisi per rilevare le anomalie      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Configurare la risorsa di servizi di comunicazione per ricevere i log operativi della chat a scopo di monitoraggio e diagnostica          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


* * Il pacchetto di segnalazione proprietario viene implementato utilizzando i socket Web. Verrà eseguito il fallback al polling lungo se i socket Web non sono supportati.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Supporto JavaScript Chat SDK per sistema operativo e browser    

La tabella seguente rappresenta il set di versioni e browser supportati attualmente disponibili.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operativo per iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **SDK Chat** | Firefox *, Chrome*, nuovo Edge | Firefox *, Chrome*, Safari * | Chrome*  | Chrome* | Chrome* | Safari | Safari |

* Si noti che la versione più recente è supportata in aggiunta alle due versioni precedenti.<br/>   

## <a name="next-steps"></a>Passaggi successivi   

> [!div class="nextstepaction"] 
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)    

I documenti seguenti possono essere interessanti:  
- Acquisire familiarità con i [concetti relativi alle chat](../chat/concepts.md)