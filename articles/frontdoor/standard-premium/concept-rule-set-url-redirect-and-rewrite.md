---
title: Reindirizzamento URL e riscrittura URL con Azure front door standard/Premium (anteprima)
description: Questo articolo illustra come il front-end di Azure supporta il reindirizzamento URL e la riscrittura URL usando il set di regole di Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100008"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Reindirizzamento URL e riscrittura URL con Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra come Azure front door standard/Premium supporta il reindirizzamento URL e la riscrittura URL usati in un set di regole.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Reindirizzamento URL

Lo sportello anteriore di Azure può reindirizzare il traffico a ognuno dei seguenti livelli: protocollo, nome host, percorso, stringa di query e frammento. Queste funzionalità possono essere configurate per il singolo micro-Service perché il reindirizzamento è basato sul percorso. Con reindirizzamento URL è possibile semplificare la configurazione dell'applicazione ottimizzando l'utilizzo delle risorse e supporta nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso.

È possibile configurare il reindirizzamento URL tramite set di regole.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Screenshot della creazione del reindirizzamento URL con il set di regole." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Tipi di Reindirizzamento
Un tipo di reindirizzamento imposta il codice di stato della risposta per i client per comprendere lo scopo del reindirizzamento. Sono supportati i tipi di reindirizzamento seguenti:

* **301 (spostato in modo permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente. Tutti i riferimenti futuri a questa risorsa utilizzeranno uno degli URI racchiusi. Usare il codice di stato 301 per il Reindirizzamento da HTTP a HTTPS.
* **302 (Found)**: indica che la risorsa di destinazione è temporaneamente in un URI diverso. Poiché il reindirizzamento può variare in un'occasione, il client deve continuare a usare l'URI di richiesta effettivo per richieste future.
* **307 (Reindirizzamento temporaneo)**: indica che la risorsa di destinazione è temporaneamente in un URI diverso. L'agente utente non deve modificare il metodo di richiesta se esegue un reindirizzamento automatico a tale URI. Poiché il reindirizzamento può variare nel tempo, il client deve continuare a usare l'URI di richiesta effettivo originale per richieste future.
* **308 (Reindirizzamento permanente)**: indica che alla risorsa di destinazione è stato assegnato un nuovo URI permanente. Tutti i riferimenti futuri a questa risorsa devono usare uno degli URI racchiusi.

### <a name="redirection-protocol"></a>Protocollo di Reindirizzamento
È possibile impostare il protocollo che verrà utilizzato per il reindirizzamento. I casi d'uso più comuni della funzionalità di reindirizzamento, consiste nell'impostare il Reindirizzamento da HTTP a HTTPS.

* **Solo HTTPS**: impostare il protocollo solo su HTTPS, se si intende reindirizzare il traffico da http a HTTPS. Lo sportello anteriore di Azure consiglia di impostare sempre il reindirizzamento solo su HTTPS.
* **Solo http**: reindirizza la richiesta in ingresso a http. Utilizzare questo valore solo se si desidera che il traffico HTTP non sia crittografato.
* **Request match**: questa opzione mantiene il protocollo usato dalla richiesta in ingresso. Pertanto, una richiesta HTTP rimane HTTP e una richiesta HTTPS rimane il reindirizzamento post HTTPS.

### <a name="destination-host"></a>Host di destinazione
Come parte della configurazione di un routing di reindirizzamento, è anche possibile modificare il nome host o il dominio per la richiesta di reindirizzamento. È possibile impostare questo campo per modificare il nome host nell'URL per il reindirizzamento oppure mantenere il nome host dalla richiesta in ingresso. Quindi, usando questo campo è possibile reindirizzare tutte le richieste inviate `https://www.contoso.com/*` a `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Percorso di destinazione
Per i casi in cui si desidera sostituire il segmento di percorso di un URL come parte del reindirizzamento, è possibile impostare questo campo con il nuovo valore del percorso. In caso contrario, è possibile scegliere di mantenere il valore del percorso come parte del reindirizzamento. Quindi, usando questo campo, è possibile reindirizzare tutte le richieste inviate a `https://www.contoso.com/\*` a  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parametri della stringa di query
È anche possibile sostituire i parametri della stringa di query nell'URL reindirizzato. Per sostituire una stringa di query esistente dall'URL della richiesta in ingresso, impostare questo campo su' Sostituisci ' e quindi impostare il valore appropriato. In caso contrario, è possibile mantenere il set originale di stringhe di query impostando il campo su' preserve '. Ad esempio, usando questo campo, è possibile reindirizzare tutto il traffico inviato a `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Frammento di destinazione
Il frammento di destinazione è la parte dell'URL dopo ' #', che viene utilizzata dal browser per la visualizzazione di una sezione specifica di una pagina Web. È possibile impostare questo campo per aggiungere un frammento all'URL di reindirizzamento.

## <a name="url-rewrite"></a>Riscrittura URL

Il front-end di Azure supporta la riscrittura URL per riscrivere il percorso di una richiesta che viene indirizzata all'origine. La riscrittura URL consente di aggiungere condizioni per garantire che l'URL o le intestazioni specificate vengano riscritte solo quando vengono soddisfatte determinate condizioni. Queste condizioni sono basate sulle informazioni della richiesta e della risposta.

Con questa funzionalità è possibile reindirizzare gli utenti a origini diverse in base allo scenario, al tipo di dispositivo e al tipo di file richiesto.

È possibile configurare il reindirizzamento URL tramite set di regole.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Screenshot della creazione della riscrittura URL con il set di regole." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Modello di origine

Modello di origine è il percorso URL nella richiesta di origine da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare una barra (/) come valore del modello di origine.

### <a name="destination"></a>Destination

È possibile definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine.

### <a name="preserve-unmatched-path"></a>Mantieni percorso senza corrispondenza

Mantieni percorso senza corrispondenza consente di aggiungere il percorso rimanente dopo il modello di origine al nuovo percorso.

Ad esempio, se si imposta **Mantieni percorso senza corrispondenza su Sì**.
* Se la richiesta in ingresso è `www.contoso.com/sub/1.jpg` , il modello di origine viene impostato su `/` , la destinazione Get impostata su `/foo/` e il contenuto viene servito da `/foo/sub/1` . jpg dall'origine.

* Se la richiesta in ingresso è `www.contoso.com/sub/image/1.jpg` , il modello di origine viene impostato su `/sub/` , la destinazione Get impostata su `/foo/` , il contenuto viene servito dall' `/foo/image/1.jpg` origine.

Ad esempio, se si imposta **Mantieni percorso senza corrispondenza su No**.
* Se la richiesta in ingresso è `www.contoso.com/sub/image/1.jpg` , il modello di origine viene impostato su `/sub/` , l'oggetto di destinazione viene impostato su `/foo/2.jpg` , il contenuto viene sempre servito dall' `/foo/2.jpg` origine, indipendentemente dai percorsi seguiti `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [set di regole standard/Premium di Azure front door](concept-rule-set.md).
