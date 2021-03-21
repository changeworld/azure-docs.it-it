---
title: Risolvere i problemi di configurazione standard/Premium di Azure front door
description: In questa esercitazione si apprenderà come risolvere alcuni dei problemi comuni che si potrebbero incontrare per l'istanza di Azure front door standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100199"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Risoluzione dei problemi di routing comuni con Azure front door standard/Premium

Questo articolo descrive come risolvere i problemi di routing comuni che è possibile affrontare per la configurazione di Azure front door.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 risposta da Azure front door dopo alcuni secondi

### <a name="symptom"></a>Sintomo

* Le richieste normali inviate al back-end senza passare attraverso la porta anteriore di Azure hanno esito positivo. L'ingresso tramite il front-end di Azure genera risposte di errore 503.
* L'errore dal portello anteriore di Azure viene in genere visualizzato dopo circa 30 secondi.

### <a name="cause"></a>Causa

La causa di questo problema può essere una delle seguenti:
 
* L'origine richiede più tempo del timeout configurato (il valore predefinito è 30 secondi) per ricevere la richiesta dal front-end di Azure.
* Il tempo necessario per inviare una risposta alla richiesta dal portello anteriore di Azure richiede più tempo del valore di timeout. 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

* Inviare la richiesta al back-end direttamente (senza passare attraverso l'ingresso di Azure). Vedere per quanto tempo il back-end richiede in genere la risposta.
* Inviare la richiesta tramite il front-end di Azure per verificare se sono presenti risposte 503. In caso contrario, il problema potrebbe non essere un problema di timeout. Contattare il supporto tecnico.
* Se si passa attraverso la porta anteriore di Azure in un codice di risposta di errore 503, configurare il `sendReceiveTimeout` campo per lo sportello anteriore di Azure. È possibile estendere il timeout predefinito per un massimo di 4 minuti (240 secondi). L'impostazione è sotto `Endpoint Setting` e viene chiamato `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Le richieste inviate al dominio personalizzato restituiscono un codice di stato 400

### <a name="symptom"></a>Sintomo

* È stata creata un'istanza di front door di Azure, ma una richiesta all'host del dominio o front-end sta restituendo un codice di stato HTTP 400.
* È stato creato un mapping DNS per un dominio personalizzato all'host front-end configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400. Non sembra essere indirizzato al back-end configurato.

### <a name="cause"></a>Causa

Il problema si verifica se non è stata configurata una regola di routing per il dominio personalizzato che è stato aggiunto come host front-end. È necessario aggiungere in modo esplicito una regola di routing per l'host front-end. Questo vale anche se è già stata configurata una regola di routing per l'host front-end nel sottodominio di Azure front door (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Aggiungere una regola di routing per il dominio personalizzato per indirizzare il traffico al gruppo di origine selezionato.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure front door non reindirizza HTTP a HTTPS

### <a name="symptom"></a>Sintomo

Il front-end di Azure dispone di una regola di routing che reindirizza HTTP a HTTPS, ma l'accesso al dominio mantiene comunque HTTP come protocollo.

### <a name="cause"></a>Causa

Questo comportamento può verificarsi se le regole di routing non sono state configurate correttamente per il front-end di Azure. In pratica, la configurazione corrente non è specifica e potrebbe avere regole in conflitto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>La richiesta al nome host front-end restituisce un codice di stato 411

### <a name="symptom"></a>Sintomo

È stata creata un'istanza standard/Premium di Azure front door e è stato configurato un host front-end, un gruppo di origine con almeno un'origine e una regola di routing che connette l'host front-end al gruppo di origine. Il contenuto non sembra essere disponibile quando una richiesta passa all'host front-end configurato perché viene restituito un codice di stato HTTP 411.

Le risposte a queste richieste possono anche contenere una pagina di errore HTML nel corpo della risposta che include un'istruzione esplicativa. Ad esempio: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Questo sintomo può essere dovuto a diverse cause. Il motivo generale è che la richiesta HTTP non è completamente conforme a RFC. 

Un esempio di non conformità è una `POST` richiesta inviata senza un' `Content-Length` `Transfer-Encoding` intestazione o (ad esempio, usando `curl -X POST https://example-front-door.domain.com` ). Questa richiesta non soddisfa i requisiti definiti nella [specifica RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Lo sportello anteriore di Azure lo blocca con una risposta HTTP 411.

Questo comportamento è separato dalla funzionalità Web Application Firewall (WAF) di Azure front door. Attualmente, non è possibile disabilitare questo comportamento. Tutte le richieste HTTP devono soddisfare i requisiti, anche se la funzionalità WAF non è in uso.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

- Verificare che le richieste siano conformi ai requisiti impostati nelle RFC necessarie.

- Prendere nota di qualsiasi corpo del messaggio HTML restituito in risposta alla richiesta. Il corpo di un messaggio spiega spesso esattamente il *modo* in cui la richiesta non è conforme.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
