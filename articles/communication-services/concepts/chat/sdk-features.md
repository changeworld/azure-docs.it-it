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
ms.openlocfilehash: 1d40d90f0f64328bbf7103015f52ea4b132d2e51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729627"
---
# <a name="chat-sdk-overview"></a>Panoramica di Chat SDK 

Azure Communication Services Chat SDK può essere usato per aggiungere chat avanzate e in tempo reale alle applicazioni.
    
## <a name="chat-sdk-capabilities"></a>Funzionalità di Chat SDK    

Nell'elenco seguente viene presentato il set di funzionalità attualmente disponibili negli SDK di chat di Communication Services.  

| Gruppo di funzionalità | Funzionalità | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Funzionalità principali | Creare un thread di chat tra due o più utenti                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aggiornare l'argomento di un thread di chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Aggiunta o rimozione di partecipanti da un thread di chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Scegliere se condividere la cronologia dei messaggi di chat con il partecipante aggiunto                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Ottenere un elenco di partecipanti in un thread di chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Eliminare un thread di chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dato un utente di comunicazione, ottenere l'elenco dei thread di chat di cui l'utente fa parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Ottenere informazioni per un thread di chat specifico                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Inviare e ricevere messaggi in un thread di chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Aggiornare il contenuto del messaggio inviato                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Eliminare un messaggio inviato in precedenza                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Conferme di lettura per i messaggi che sono stati letti da altri partecipanti in una chat                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ricevere una notifica quando i partecipanti digitano attivamente un messaggio in un thread di chat                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Ottenere tutti i messaggi in un thread di chat                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Invia emoji Unicode come parte del contenuto del messaggio                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Notifiche in tempo reale (abilitate dal pacchetto di segnalazione proprietario * *)|  I client di chat possono sottoscrivere per ottenere gli aggiornamenti in tempo reale per i messaggi in arrivo e altre operazioni che si verificano in un thread di chat. Per visualizzare un elenco degli aggiornamenti supportati per le notifiche in tempo reale, vedere [concetti relativi alla chat](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  | 
| Integrazione con Griglia di eventi di Azure             | Usare gli eventi di chat disponibili in griglia di eventi di Azure per inserire i servizi di notifica personalizzati o pubblicare l'evento in un webhook per eseguire la logica di business, ad esempio l'aggiornamento dei record CRM al termine di una chat   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Creazione di report </br>(Queste informazioni sono disponibili nella scheda monitoraggio per la risorsa di servizi di comunicazione in portale di Azure)      | Informazioni sul traffico API dall'app Chat monitorando le metriche pubblicate in Azure Esplora metriche e impostare gli avvisi per rilevare le anomalie     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorare ed eseguire il debug della soluzione Servizi di comunicazione abilitando la registrazione diagnostica per la risorsa    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


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
- Informazioni sul funzionamento dei [prezzi](../pricing.md#chat) per la chat
