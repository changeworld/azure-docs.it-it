---
title: 'Sportello anteriore di Azure: set di regole'
description: Questo articolo fornisce una panoramica della funzionalità di set di regole standard/Premium di Azure front door.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099969"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Che cos'è un set di regole per Azure front door standard/Premium (anteprima)?

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Un set di regole è un motore regole di dominio personalizzato che raggruppa una combinazione di regole in un singolo set che è possibile associare a più route. Il set di regole consente di personalizzare il modo in cui le richieste vengono elaborate al perimetro e il modo in cui le richieste vengono gestite da Azure front door.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Scenari supportati comuni

* Implementazione delle intestazioni di sicurezza per impedire le vulnerabilità basate su browser come HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-frame-options e Access-Control-Allow-Origin Headers per scenari di condivisione di risorse tra le origini (CORS). Gli attributi basati sulla sicurezza possono anche essere definiti con i cookie.

* Instradare le richieste alle versioni desktop o per dispositivi mobili dell'applicazione in base al tipo di dispositivo client.

* L'uso delle funzionalità di reindirizzamento per restituire 301, 302, 307 e 308 reindirizza al client per indirizzarli a nuovi nomi host, percorsi, stringhe di query o protocolli.

* Modificare dinamicamente la configurazione della memorizzazione nella cache della route in base alle richieste in ingresso.

* Riscrivere il percorso dell'URL della richiesta e quindi la richiesta all'origine appropriata nel gruppo di origine configurato.

* Aggiungere, modificare o rimuovere l'intestazione di richiesta/risposta per nascondere le informazioni riservate o acquisire informazioni importanti tramite le intestazioni.

* Supportare le variabili server per modificare dinamicamente le intestazioni di richiesta/risposta o i percorsi di riscrittura URL/stringhe di query, ad esempio quando viene inviato un nuovo caricamento della pagina o quando viene pubblicato un modulo. La variabile server è attualmente supportata solo per le **[azioni del set di regole](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Architettura

Il set di regole gestisce le richieste al perimetro. Quando una richiesta arriva all'endpoint standard/Premium di Azure front door, WAF viene eseguito per primo, seguito dalle impostazioni configurate in Route. Tali impostazioni includono il set di regole associato alla route. I set di regole vengono elaborati dall'alto verso il basso nella route. Lo stesso vale per le regole all'interno di un set di regole. Per consentire l'esecuzione di tutte le azioni in ogni regola, è necessario che tutte le condizioni di corrispondenza all'interno di una regola siano soddisfatte. Se una richiesta non corrisponde ad alcuna condizione nella configurazione del set di regole, verranno eseguite solo le configurazioni in Route.

Se l'opzione **Interrompi la valutazione delle regole rimanenti** viene controllata, tutti i set di regole rimanenti associati alla route non vengono eseguiti.  

### <a name="example"></a>Esempio

Nel diagramma seguente i criteri WAF vengono eseguiti per primi. Un set di regole viene configurato per aggiungere un'intestazione di risposta. Quindi, l'intestazione modifica la validità massima del controllo della cache se viene soddisfatta la condizione di corrispondenza.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagramma che mostra l'architettura del set di regole." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologia

Con il set di regole di front-end di Azure è possibile creare una combinazione di configurazioni di set di regole, ognuna composta da un set di regole. Di seguito sono riportate alcune termini utili per la configurazione del set di regole.

Per un maggiore limite di quota, vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Set di regole*: set di regole che vengono associate a una o più [Route](concept-route.md). Ogni configurazione può contenere un massimo di 25 regole. È possibile creare fino a 10 configurazioni.

* *Regola set di regole*: una regola composta da un massimo di 10 condizioni di corrispondenza e 5 azioni. Le regole sono locali rispetto a un set di regole e non possono essere esportate per l'utilizzo tra set di regole. Gli utenti possono creare la stessa regola in più set di regole.

* *Condizione di corrispondenza*: esistono diverse condizioni di corrispondenza che è possibile utilizzare per analizzare le risposte in ingresso. Una regola può contenere fino a 10 condizioni di corrispondenza. Le condizioni di corrispondenza vengono valutate con un operatore **AND**. L' *espressione regolare è supportata nelle condizioni*. Un elenco completo delle condizioni di corrispondenza si trova nella [condizione del set di regole](concept-rule-set-match-conditions.md).

* *Azione*: le azioni determinano il modo in cui AFD gestisce le richieste in ingresso in base alle condizioni di corrispondenza. È possibile modificare i comportamenti di memorizzazione nella cache, modificare le intestazioni di richiesta/risposta, eseguire la riscrittura URL e il reindirizzamento URL. Le *variabili server sono supportate sull'azione*. Una regola può contenere fino a 10 condizioni di corrispondenza. È possibile trovare un elenco completo di azioni per il [set di regole](concept-rule-set-actions.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
* Informazioni su come configurare il primo [set di regole](how-to-configure-rule-set.md).
 
