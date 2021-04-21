---
title: Supporto del protocollo per le intestazioni HTTP in Frontdoor di Azure | Microsoft Docs
description: Questo articolo descrive i protocolli di intestazione HTTP supportati da Front door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785758"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Supporto del protocollo per le intestazioni HTTP in Frontdoor di Azure
Questo articolo descrive il protocollo supportato da Front door con parti del percorso di chiamata (vedere l'immagine). Le sezioni seguenti forniscono altre informazioni sulle intestazioni HTTP supportate da Front door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Frontdoor di Azure di intestazioni HTTP":::

>[!IMPORTANT]
>Front door non certifica le intestazioni HTTP non documentate qui.

## <a name="client-to-front-door"></a>Da client a Frontdoor
Front door accetta la maggior parte delle intestazioni per la richiesta in ingresso senza modificarle. Alcune intestazioni riservate vengono rimosse dalla richiesta in ingresso se inviate, incluse le intestazioni con il prefisso X-FD-*.

## <a name="front-door-to-backend"></a>Da Frontdoor a back-end

Front door include intestazioni per una richiesta in ingresso, a meno che non vengano rimosse a causa di restrizioni. Front door aggiunge anche le intestazioni seguenti:

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| Via |  *Tramite: 1.1 Azure* </br> Front door aggiunge la versione HTTP del client seguita da *Azure* come valore per l'intestazione Via. Questa intestazione indica la versione HTTP del client e che Front door era un destinatario intermedio per la richiesta tra il client e il back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Rappresenta l'indirizzo IP del client associato alla richiesta in fase di elaborazione. Ad esempio, una richiesta proveniente da un proxy potrebbe aggiungere l'intestazione X-Forwarded-For per indicare l'indirizzo IP del chiamante originale. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Rappresenta l'indirizzo IP del socket associato alla connessione TCP da cui ha avuto origine la richiesta corrente. L'indirizzo IP client di una richiesta potrebbe non essere uguale all'indirizzo IP socket perché l'indirizzo IP client può essere sovrascritto arbitrariamente da un utente.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Stringa di riferimento univoca che identifica una richiesta servita da Front door. Viene usato per cercare log di accesso e critici per la risoluzione dei problemi.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Intestazione utilizzata da Front door per rilevare i cicli di richiesta e gli utenti non devono prendere una dipendenza da essa. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Stringa di riferimento che identifica la richiesta proveniva da una risorsa front-door specifica. Il valore può essere visualizzato nel portale di Azure o recuperato usando l'API di gestione. È possibile usare questa intestazione in combinazione con gli ACL IP per bloccare l'endpoint in modo da accettare solo le richieste provenienti da una risorsa front-door specifica. Per altri dettagli, vedere [le domande frequenti](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Il campo dell'intestazione HTTP X-Forwarded-For (XFF) identifica spesso l'indirizzo IP di origine di un client che si connette a un server Web tramite un proxy HTTP o un servizio di bilanciamento del carico. Se è presente un'intestazione XFF, Front door aggiunge l'INDIRIZZO IP del socket client o aggiunge l'intestazione XFF con l'INDIRIZZO IP del socket client. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Il campo di intestazione HTTP X-Forwarded-Host è un metodo comune usato per identificare l'host originale richiesto dal client nell'intestazione della richiesta HTTP host. Ciò è dovuto al fatto che il nome host di Front door può essere diverso per il server back-end che gestisce la richiesta. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Il campo di intestazione HTTP X-Forwarded-Proto viene spesso usato per identificare il protocollo di origine di una richiesta HTTP. Front door basato sulla configurazione potrebbe comunicare con il back-end tramite HTTPS. Questo vale anche se la richiesta al proxy inverso è HTTP. |
| X-FD-HealthProbe | Il campo di intestazione HTTP X-FD-HealthProbe viene usato per identificare il probe di integrità da Front door. Se questa intestazione è impostata su 1, la richiesta è un probe di integrità. È possibile usare quando si vuole un accesso rigoroso da un particolare front-door con il campo di intestazione X-Forwarded-Host. |
| X-Azure-FDID | *Intestazione X-Azure-FDID: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Questo campo contiene frontdoorID che può essere usato per identificare la frontdoor da cui viene inviata la richiesta in ingresso. Questo campo viene popolato dal servizio Front door. | 

## <a name="front-door-to-client"></a>Da Frontdoor a client

Anche tutte le intestazioni inviate a Front door dal back-end vengono passate al client. Di seguito sono riportate le intestazioni inviate da Front door ai client.

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Si tratta di una stringa di riferimento univoca che identifica una richiesta servita da Front door, che è fondamentale per la risoluzione dei problemi perché viene usata per cercare i log di accesso.|
| X-Cache | *X-Cache: TCP_HIT* </br> Questa intestazione descrive lo stato della cache della richiesta, che consente di identificare se il contenuto della risposta viene servito dalla cache della front door. |

È necessario inviare l'intestazione della richiesta "X-Azure-DebugInfo: 1" per abilitare le intestazioni di risposta facoltative seguenti.

| Intestazione  | Esempio e descrizione |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Questa intestazione contiene il codice di stato HTTP restituito dal back-end. Usando questa intestazione è possibile identificare il codice di stato HTTP restituito dall'applicazione in esecuzione nel back-end senza passare attraverso i log back-end. Questo codice di stato potrebbe essere diverso dal codice di stato HTTP nella risposta inviata al client da Front door. Questa intestazione consente di determinare se il back-end è in stato di errore o se il problema riguarda il servizio Front door. |
| X-Azure-InternalError | Questa intestazione conterrà il codice di errore rilevato da Front door durante l'elaborazione della richiesta. Questo errore indica che il problema è interno al servizio o all'infrastruttura front-door. Segnalare un problema da supportare.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, l'autorità di certificazione non ha familiarità.* </br> Questa intestazione mostra il codice di errore rilevato dai server front-end durante la connessione al server back-end per elaborare una richiesta. Questa intestazione consente di identificare i problemi di connessione tra Front door e l'applicazione back-end. Questa intestazione includerà un messaggio di errore dettagliato che consente di identificare i problemi di connettività al back-end, ad esempio la risoluzione DNS, il certificato non valido e così via. |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una front door](quickstart-create-front-door.md)
- [Funzionamento di Front door](front-door-routing-architecture.md)
