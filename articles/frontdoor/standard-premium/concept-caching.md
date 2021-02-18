---
title: 'Sportello anteriore di Azure: memorizzazione nella cache'
description: Questo articolo consente di comprendere il comportamento di Azure front door standard/Premium con le regole di routing che hanno abilitato la memorizzazione nella cache.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 73b2e8e59774e12ddb9aa684382510d1f2c151b8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100151"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Caching con Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

In questo articolo si apprenderà come si comporterà il comportamento delle route standard/Premium (anteprima) e del set di regole quando la memorizzazione nella cache è abilitata. Il front-end di Azure è una rete per la distribuzione di contenuti (CDN) moderna con accelerazione sito dinamico e bilanciamento del carico.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="delivery-of-large-files"></a>Recapito di file di grandi dimensioni

La porta anteriore standard/Premium (anteprima) offre file di grandi dimensioni senza limiti per le dimensioni dei file. Frontdoor di Azure usa una tecnica chiamata suddivisione degli oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, la porta anteriore recupera parti più piccole del file dall'origine. Dopo la ricezione di una richiesta di file completa o di intervallo di byte, l'ambiente della porta anteriore richiede il file dalle origini in blocchi di 8 MB.

Una volta che il blocco raggiunge l'ambiente di sportello anteriore, viene memorizzato nella cache e servito immediatamente all'utente. Frontdoor esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua fino a quando non viene scaricato l'intero file (se richiesto) o il client chiude la connessione.

Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
La porta anteriore memorizza nella cache tutti i blocchi Man mano che vengono ricevuti, in modo che l'intero file non debba essere memorizzato nella cache della porta anteriore. Le richieste successive per il file o gli intervalli di byte vengono gestite dalla cache. Se i blocchi non sono tutti memorizzati nella cache, il recupero preliminare viene usato per richiedere i blocchi dal back-end. Questa ottimizzazione si basa sulla capacità di supportare le richieste di intervallo di byte. Se l'origine non supporta le richieste di intervalli di byte, questa ottimizzazione non è efficace.

## <a name="file-compression"></a>Compressione di file

Per migliorare le prestazioni, è possibile comprimere i file in ingresso di Azure.

## <a name="query-string-behavior"></a>Comportamento di memorizzazione della stringa di query

Frontdoor consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non è rilevante.

* **Ignora stringhe di query**: in questa modalità, la porta anteriore passa le stringhe di query dal richiedente all'origine alla prima richiesta e memorizza nella cache l'asset. Tutte le richieste successive per l'asset che vengono gestite dall'ambiente della porta anteriore ignorano le stringhe di query fino alla scadenza dell'asset memorizzato nella cache.

* **Memorizza nella cache tutti gli URL univoci**: in questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta dall'origine per una richiesta per `www.example.ashx?q=test1` viene memorizzata nella cache nell'ambiente della porta anteriore e restituita per le cache successive con la stessa stringa di query. Una richiesta di `www.example.ashx?q=test2` viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).
* È anche possibile usare set di regole per specificare il comportamento **della stringa di query della chiave della cache** , includere o escludere i parametri specificati quando viene generata la chiave di cache. Ad esempio, la chiave di cache predefinita è:/foo/image/asset.html e la richiesta di esempio è `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . È presente una regola del set di regole per escludere la stringa di query ' userid '. La chiave di cache della stringa di query sarà quindi `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Ripulire la cache

Vedere ripulitura della cache.

## <a name="cache-expiration"></a>Ora di scadenza della cache
Per determinare per quanto tempo un elemento verrà archiviato nella cache, viene usato l'ordine di intestazioni seguente:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Scadenza \<http-date>

Cache-Control intestazioni di risposta che indicano che la risposta non verrà memorizzata nella cache, ad esempio cache-Control: private, cache-Control: No-cache e cache-Control: No-Store viene rispettato.  Se non è presente alcun Cache-Control, il comportamento predefinito prevede che la risorsa venga memorizzata nella cache della risorsa per un periodo di tempo pari a X. Dove X viene selezionato in modo casuale tra 1 e 3 giorni.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni di richiesta seguenti non verranno trasmesse a un'origine quando si usa la memorizzazione nella cache.
* Content-Length
* Transfer-encoding

## <a name="cache-duration"></a>Durata cache

La durata della cache può essere configurata nel set di regole. La durata della cache impostata tramite set di regole è un vero e proprio override della cache. Il che significa che utilizzerà il valore di override indipendentemente dall'intestazione della risposta Origin.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [condizioni di corrispondenza del set di regole](concept-rule-set-match-conditions.md)
* Altre informazioni sulle [azioni del set di regole](concept-rule-set-actions.md)