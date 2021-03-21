---
title: Configurare le azioni del set di regole standard/Premium di Azure front door
description: Questo articolo fornisce un elenco delle varie azioni che è possibile eseguire con il set di regole di front-end di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100048"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Azioni del set di regole standard/Premium di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Un [set](concept-rule-set.md) di regole di Azure front door è costituito da regole con una combinazione di condizioni e azioni di corrispondenza. Questo articolo fornisce una descrizione dettagliata delle azioni che è possibile usare in un set di regole. L'azione definisce il comportamento che viene applicato a un tipo di richiesta identificato da una o più condizioni di corrispondenza. In un set di regole di Azure front door, una regola può contenere fino a cinque azioni. La variabile server è supportata in tutte le azioni.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le azioni seguenti sono disponibili per l'uso nel set di regole di front-end di Azure.  

## <a name="cache-expiration"></a>Ora di scadenza della cache

Usare questa azione per sovrascrivere il valore TTL (time to Live) dell'endpoint per le richieste specificate dalle condizioni di corrispondenza delle regole.

### <a name="required-fields"></a>Required fields

La descrizione seguente si applica quando si selezionano questi comportamenti della cache e la regola corrisponde a:

Comportamento cache |  Descrizione              
---------------|----------------
Ignora cache | Il contenuto non viene memorizzato nella cache.
Sostituisci | Il valore TTL restituito dall'origine viene sovrascritto con il valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Per l'intestazione della risposta Cache-Control con i valori "no-cache", "private", "No-Store", l'azione non sarà applicabile.
Imposta se mancante | Se dall'origine non viene restituito alcun valore TTL, la regola imposta la durata (TTL) sul valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Per l'intestazione della risposta Cache-Control con i valori "no-cache", "private", "No-Store", l'azione non sarà applicabile.

### <a name="additional-fields"></a>Altri campi

Giorni | Ore | Minuti | Secondi
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Stringa di query della chiave della cache

Usare questa azione per modificare la chiave della cache in base alle stringhe di query.

### <a name="required-fields"></a>Required fields

La descrizione seguente si applica quando si selezionano questi comportamenti e la regola corrisponde a:

Comportamento | Descrizione
---------|------------
Includi | Le stringhe di query specificate nei parametri vengono incluse quando viene generata la chiave di cache. 
Memorizza nella cache tutti gli URL univoci | Ogni URL univoco ha una propria chiave di cache. 
Escludi | Le stringhe di query specificate nei parametri vengono escluse quando viene generata la chiave di cache.
Ignora stringhe di query | Le stringhe di query non vengono considerate quando viene generata la chiave di cache. 

## <a name="modify-request-header"></a>Modifica intestazione richiesta

Usare questa azione per modificare le intestazioni presenti nelle richieste inviate all'origine.

### <a name="required-fields"></a>Required fields

La descrizione seguente si applica quando si selezionano queste azioni e la regola corrisponde a:

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | L'intestazione specificata nel **nome dell'intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto a quello esistente. | string
Overwrite | L'intestazione specificata nel **nome dell'intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive quello esistente. | string
Delete | Se è presente l'intestazione specificata nella regola, l'intestazione viene eliminata dalla richiesta. | string

## <a name="modify-response-header"></a>Modifica intestazione risposta

Usare questa azione per modificare le intestazioni presenti nelle risposte restituite ai client.

### <a name="required-fields"></a>Required fields

La descrizione seguente si applica quando si selezionano queste azioni e la regola corrisponde a:

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | L'intestazione specificata nel **nome dell'intestazione** viene aggiunta alla risposta usando il **valore** specificato. Se l'intestazione è già presente, il **valore** viene aggiunto a quello esistente. | string
Overwrite | L'intestazione specificata nel **nome dell'intestazione** viene aggiunta alla risposta usando il **valore** specificato. Se l'intestazione è già presente, il **valore** sovrascrive quello esistente. | string
Delete | Se è presente l'intestazione specificata nella regola, l'intestazione viene eliminata dalla risposta. | string

## <a name="url-redirect"></a>Reindirizzamento URL

Usare questa azione per reindirizzare i client a un nuovo URL. 

### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Tipo di reindirizzamento | Selezionare il tipo di risposta da restituire al richiedente: Trovato (302), Spostato (301), Reindirizzamento temporaneo (307) e Reindirizzamento permanente (308).
Protocollo di reindirizzamento | Corrispondenza richiesta, HTTP, HTTPS.
Host di destinazione | Selezionare il nome host a cui reindirizzare la richiesta. Lasciare vuoto questo campo per mantenere l'host in ingresso.
Percorso di destinazione | Definire il percorso da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il percorso in ingresso.  
Stringa di query | Definire la stringa di query usata nel reindirizzamento. Lasciare vuoto questo campo per mantenere la stringa di query in ingresso. 
Frammento di destinazione | Definire il frammento da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il frammento in ingresso. 

## <a name="url-rewrite"></a>Riscrittura URL

Usare questa azione per riscrivere il percorso di una richiesta instradata all'origine.

### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare una barra ( **/** ) come valore del modello di origine.
Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine.
Mantieni percorso senza corrispondenza | Se impostato su **Sì**, il percorso rimanente dopo il modello di origine viene aggiunto al nuovo percorso di destinazione. 

## <a name="server-variable"></a>Variabile server

### <a name="supported-variables"></a>Variabili supportate

| Nome variabile | Descrizione                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | Indirizzo IP della connessione diretta al perimetro di Azure front door. Se il client usa un proxy HTTP o un servizio di bilanciamento del carico per inviare la richiesta, il valore di SocketIp è l'indirizzo IP del proxy o del servizio di bilanciamento del carico. |
| client_ip                  | Indirizzo IP del client che ha effettuato la richiesta originale. Se nella richiesta è presente un'intestazione X-Inoltred-for, l'indirizzo IP del client viene selezionato dallo stesso. |
| client_port                | Porta IP del client che ha effettuato la richiesta. |
| hostname                      | Nome host nella richiesta dal client. |
| geo_country                     | Indica il paese/l'area geografica di origine del richiedente tramite il relativo codice paese/area geografica. |
| http_method                | Metodo utilizzato per effettuare la richiesta dell'URL. Ad esempio, GET o POST. |
| http_version               | Protocollo della richiesta. In genere HTTP/1.0, HTTP/1.1 o HTTP/2.0. |
| query_string               | Elenco di coppie variabile/valore che seguono "?" nell'URL richiesto. Esempio: nella richiesta *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING valore sarà *ID = 123&title = Fabrikam* |
| request_scheme             | Schema di richiesta: http o HTTPS. |
| request_uri                | URI completo della richiesta originale (con argomenti). Esempio: nella richiesta *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI valore sarà */article.aspx? ID = 123&title = Fabrikam* |
| server_port                | Porta del server che ha accettato una richiesta. |
| ssl_protocol    | Protocollo di una connessione TLS stabilita. |
| url_path                   | Identifica la risorsa specifica nell'host a cui il client Web vuole accedere. Questa è la parte dell'URI della richiesta senza gli argomenti. Esempio: nella richiesta *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path valore sarà */article.aspx* |

### <a name="server-variable-format"></a>Formato variabile server    

**Formato:** {variabile: offset}, {variabile: offset: lunghezza}, {Variable}

### <a name="supported-server-variable-actions"></a>Azioni delle variabili server supportate

* Intestazione della richiesta
* Intestazione risposta
* Stringa di query della chiave della cache
* Riscrittura URL
* Reindirizzamento URL

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [set di regole stanard/Premium di Azure front door](concept-rule-set.md).
* Altre informazioni sulle [condizioni di corrispondenza del set di regole](concept-rule-set-match-conditions.md).
