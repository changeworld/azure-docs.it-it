---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032934"
---
Le query sul contenitore vengono fatturate secondo il piano tariffario della risorsa di Azure usata per `ApiKey`.

I contenitori di servizi cognitivi di Azure non sono concessi in licenza per l'esecuzione senza essere connessi all'endpoint di misurazione/fatturazione. È necessario consentire ai contenitori di comunicare sempre le informazioni di fatturazione all'endpoint di fatturazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

### <a name="connect-to-azure"></a>Connettersi ad Azure

Per eseguire il contenitore, sono necessari i valori dell'argomento di fatturazione. Questi valori consentono al contenitore di connettersi all'endpoint di fatturazione. Il contenitore segnala l'utilizzo ogni 10-15 minuti. Se il contenitore non si connette ad Azure entro la finestra temporale consentita, continuerà a essere eseguito ma non fornirà query finché l'endpoint di fatturazione non verrà ripristinato. Il tentativo di connessione viene effettuato 10 volte nello stesso intervallo di tempo di 10-15 minuti. Se non è possibile connettersi all'endpoint di fatturazione entro 10 tentativi, il contenitore smette di soddisfare le richieste. Per un esempio delle informazioni inviate a Microsoft per la fatturazione, vedere [domande frequenti sul contenitore di servizi cognitivi](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) .

### <a name="billing-arguments"></a>Argomenti di fatturazione

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` Il <span class="docon docon-navigate-external x-hidden-focus"></span> </a> comando avvierà il contenitore quando tutte e tre le opzioni seguenti vengono fornite con valori validi:

| Opzione | Descrizione |
|--------|-------------|
| `ApiKey` | Chiave API della risorsa di Servizi cognitivi usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa di cui è stato effettuato il provisioning specificata in `Billing`. |
| `Billing` | Endpoint della risorsa di Servizi cognitivi usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa di Azure di cui è stato effettuato il provisioning.|
| `Eula` | Indica che è la licenza per il contenitore è stata accettata.<br/>Il valore di questa opzione deve essere impostato su **accept**. |
