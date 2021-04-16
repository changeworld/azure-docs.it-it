---
title: Panoramica di Chat SDK per Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni su Servizi di comunicazione di Azure Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500686"
---
# <a name="chat-sdk-overview"></a>Panoramica di Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Servizi di comunicazione di Azure CHAT SDK possono essere usati per aggiungere chat in tempo reale alle applicazioni.
    
## <a name="chat-sdk-capabilities"></a>Funzionalità di Chat SDK    

L'elenco seguente presenta il set di funzionalità attualmente disponibili negli SDK di chat di Servizi di comunicazione.  

| Gruppo di funzionalità | Funzionalità | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Funzionalità principali | Creare un thread di chat tra 2 o più utenti                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aggiornare l'argomento di un thread di chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Aggiungere o rimuovere partecipanti da un thread di chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Scegliere se condividere la cronologia dei messaggi di chat con il partecipante aggiunto                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Ottenere un elenco di partecipanti in un thread di chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Eliminare un thread di chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Dato un utente di comunicazione, ottenere l'elenco dei thread di chat di cui fa parte                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Ottenere informazioni per un thread di chat specifico                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Inviare e ricevere messaggi in un thread di chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Aggiornare il contenuto del messaggio inviato                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Eliminare un messaggio inviato in precedenza                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Conferme di lettura per i messaggi letti da altri partecipanti in una chat                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ricevere una notifica quando i partecipanti digitano attivamente un messaggio in un thread di chat                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Ottenere tutti i messaggi in un thread di chat                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Inviare emoji Unicode come parte del contenuto del messaggio                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Notifiche in tempo reale (abilitate dal pacchetto di segnalazione proprietario**)|  I client di chat possono sottoscrivere per ottenere aggiornamenti in tempo reale per i messaggi in ingresso e altre operazioni che si verificano in un thread di chat. Per visualizzare un elenco degli aggiornamenti supportati per le notifiche in tempo reale, vedere Concetti [relativi alle chat](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integrazione con Griglia di eventi di Azure             | Usare gli eventi di chat disponibili in Griglia di eventi di Azure per collegare servizi di notifica personalizzati o pubblicare l'evento in un webhook per eseguire la logica di business, ad esempio l'aggiornamento dei record CRM al termine di una chat   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Creazione di report </br>Queste informazioni sono disponibili nella scheda Monitoraggio per la risorsa servizi di comunicazione portale di Azure)      | Informazioni sul traffico API dall'app di chat monitorando le metriche pubblicate in Azure Esplora metriche e impostando gli avvisi per rilevare le anomalie     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorare ed eseguire il debug della soluzione Servizi di comunicazione abilitando la registrazione diagnostica per la risorsa    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**Il pacchetto di segnalazione proprietario viene implementato tramite Web Socket. Esegue il fallback al polling lungo se i Web Socket non sono supportati.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Supporto di JavaScript Chat SDK da parte del sistema operativo e del browser    

La tabella seguente rappresenta il set di versioni e browser supportati attualmente disponibili.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | Sistema operativo per iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat SDK** | Firefox,*Chrome,* nuovo edge | Firefox,*Chrome,* Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Si noti che la versione più recente è supportata oltre alle due versioni precedenti.<br/>   

## <a name="next-steps"></a>Passaggi successivi   

> [!div class="nextstepaction"] 
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)    

I documenti seguenti possono essere interessanti:  
- Acquisire familiarità con i [concetti relativi alle chat](../chat/concepts.md)
- Informazioni sul funzionamento [dei prezzi](../pricing.md#chat) per la chat
