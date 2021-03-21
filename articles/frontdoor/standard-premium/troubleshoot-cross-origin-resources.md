---
title: Uso di Azure front door standard/Premium con condivisione risorse tra le origini
description: Informazioni su come usare il portale di Azure (AFD) con la condivisione di risorse tra le origini (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099367"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Uso di Azure front door standard/Premium con la condivisione di risorse tra le origini (CORS)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

## <a name="what-is-cors"></a>Che cos'è CORS?

CORS (Cross Origin Resource Sharing) è una funzionalità HTTP che consente a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. Per ridurre la possibilità di attacchi di scripting tra siti, tutti i Web browser moderni implementano una restrizione di sicurezza nota come [criterio di stessa origine](https://www.w3.org/Security/wiki/Same_Origin_Policy). Questo impedisce a una pagina Web di chiamare le API in un dominio diverso.  CORS offre un modo sicuro per consentire a una origine, ovvero il dominio di origine, di chiamare le API in un'altra origine.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Funzionamento

Esistono due tipi di richieste CORS, le *richieste semplici* e le *richieste complesse*.

### <a name="for-simple-requests"></a>Per le richieste semplici:

1. Il browser invia la richiesta CORS con un'altra intestazione di richiesta HTTP di **origine** . Il valore di questa intestazione è l'origine che ha gestito la pagina padre, definita come la combinazione di *protocollo,* *dominio* e *porta*.  Quando una pagina da https \: //www.contoso.com tenta di accedere ai dati di un utente nell'origine fabrikam.com, l'intestazione della richiesta seguente viene inviata a Fabrikam.com:

   `Origin: https://www.contoso.com`

2. Il server può rispondere con uno degli elementi seguenti:

   * Un'intestazione **Access-Control-Allow-Origin** presente nella risposta, per indicare il sito di origine consentito. Ad esempio:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Un codice di errore HTTP, ad esempio 403, se il server non consente la richiesta Cross-Origin dopo aver verificato l'intestazione Origin

   * Un'intestazione **Access-Control-Allow-Origin** con un carattere jolly che consente tutte le origini:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Per le richieste complesse:

Una richiesta complessa è una richiesta CORS in cui il browser deve inviare una *richiesta preliminare*, ovvero un probe preliminare, prima di inviare la richiesta CORS effettiva. La richiesta preliminare richiede l'autorizzazione del server se la richiesta CORS originale può continuare ed è una `OPTIONS` richiesta allo stesso URL.

> [!TIP]
> Per altre informazioni sui flussi CORS e i problemi comuni, vedere [Guide to CORS for REST APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/) (Guida di CORS per le API REST).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenari con caratteri jolly o singola origine
CORS in front door di Azure funzionerà automaticamente senza alcuna configurazione aggiuntiva quando l'intestazione **Access-Control-Allow-Origin** è impostata su wildcard (*) o su un'unica origine.  Il front-end di Azure memorizza nella cache la prima risposta e le richieste successive utilizzeranno la stessa intestazione.

Se sono già state apportate richieste al front-end di Azure prima dell'impostazione di CORS nell'origine, sarà necessario ripulire il contenuto nel contenuto dell'endpoint per ricaricare il contenuto con l'intestazione **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scenari con più origini
Se si desidera autorizzare per CORS uno specifico elenco di origini, le operazioni da eseguire sono più complesse. Il problema si verifica quando la rete CDN memorizza nella cache l'intestazione **Access-Control-Allow-Origin** per la prima origine CORS.  Quando un'origine CORS diversa effettua un'altra richiesta, la rete CDN servirà l'intestazione **Access-Control-Allow-Origin** memorizzata nella cache, che non corrisponderà. Esistono diversi modi per risolvere il problema.

### <a name="azure-front-door-rule-set"></a>Set di regole di Azure front door

Sul portale di Azure è possibile creare una regola nel [set di regole](concept-rule-set.md) di Azure front door per verificare l'intestazione **Origin** nella richiesta. Se si tratta di un'origine valida, la regola imposterà l'intestazione **Access-Control-Allow-Origin** con il valore corretto. In questo caso, l'intestazione **Access-Control-Allow-Origin** dal server di origine del file viene ignorata e il motore regole di AFD gestisce completamente le origini CORS consentite.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Screenshot dell'esempio di regole con il set di regole.":::

> [!TIP]
> È possibile aggiungere altre azioni alla regola per modificare intestazioni di risposta aggiuntive, ad esempio **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare una Frontdoor](create-front-door-portal.md).
