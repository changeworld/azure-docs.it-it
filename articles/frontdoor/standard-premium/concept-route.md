---
title: Che cos'è il percorso standard o Premium di Azure front door?
description: Questo articolo consente di comprendere il modo in cui Azure front door standard/Premium corrisponde alla regola di routing da usare per una richiesta in ingresso.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100049"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Che cos'è il percorso Azure front door standard/Premium (Preview)?

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Azure front door standard/Premium Route definisce il modo in cui il traffico viene gestito quando la richiesta in ingresso arriva all'ambiente di front-end di Azure. Tramite le impostazioni di route, viene definita un'associazione tra un dominio e un gruppo di origine back-end. Attivando le funzionalità avanzate, ad esempio il modello a Mach, il set di regole, il controllo più granulare sul traffico è realizzabile.

Una configurazione di routing standard/Premium per la porta anteriore è costituita da due parti principali: "lato sinistro" e "lato destro". La richiesta in ingresso viene abbinata al lato sinistro della route e il lato destro definisce il modo in cui viene elaborata la richiesta.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Corrispondenza in ingresso (lato sinistro)

Le seguenti proprietà determinano se la richiesta in ingresso corrisponde alla regola di routing (o lato sinistro):

* **Protocolli HTTP** (HTTP/HTTPS)
* **Host** (ad esempio, www \. foo.com, \* . bar.com)
* **Percorsi** (ad esempio, /\*, /users/\*, /file.gif)

Queste proprietà vengono espanse internamente, pertanto ogni combinazione di protocollo/host/percorso è un set di corrispondenze possibili.

### <a name="route-data-right-hand-side"></a>Dati della route (lato destro)

La decisione di elaborare la richiesta varia a seconda che la memorizzazione nella cache sia abilitata o meno per la route. Se una risposta memorizzata nella cache non è disponibile, la richiesta viene trasmessa al back-end appropriato.

## <a name="route-matching"></a>Corrispondenza delle route

Questa sezione descrive la modalità di individuazione della corrispondenza per una determinata regola di routing in Frontdoor. Il concetto di base è che viene sempre verificata **prima la corrispondenza più specifica** esaminando solo il "lato sinistro".  Viene prima verificata la corrispondenza in base al protocollo HTTP, quindi in base all'host front-end e infine in base al percorso.

### <a name="frontend-host-matching"></a>Corrispondenza di host front-end

Quando si abbinano host front-end, viene usata la logica definita di seguito:

1. Cercare eventuali routing con una corrispondenza esatta per l'host.
2. Se nessun host front-end corrisponde in modo esatto, rifiutare la richiesta e inviare un errore 400 - Richiesta non valida.

Per spiegare ulteriormente questo processo, esaminiamo una configurazione di esempio delle route di Frontdoor (solo lato sinistro):

| Regola di routing | Host front-end | Percorso |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www \. Fabrikam.com, foo.Adventure-Works.com  | /\*, /images/\* |

Se venissero inviate a Frontdoor, le richieste in ingresso seguenti corrisponderebbero alle regole di routing indicate di seguito dall'esempio precedente:

| Host front-end in ingresso | Regole di routing corrispondenti |
|---------------------|---------------|
| foo.contoso.com | A, B |
| \.Fabrikam.com www | C |
| images.fabrikam.com | Errore 400 - Richiesta non valida |
| foo.adventure-works.com | C |
| contoso.com | Errore 400 - Richiesta non valida |
| \.Adventure-Works.com www | Errore 400 - Richiesta non valida |
| \.northwindtraders.com www | Errore 400 - Richiesta non valida |

### <a name="path-matching"></a>Corrispondenza del percorso

Quando Azure front door standard/Premium determina l'host front-end specifico e filtra le regole di routing possibili solo per le route con tale host front-end. Il portello anteriore filtra quindi le regole di routing in base al percorso della richiesta. Usiamo una logica simile a quella per gli host front-end:

1. Cercare eventuali regole di routing con una corrispondenza esatta per il percorso
2. Se nessun percorso corrisponde in modo esatto, cercare le regole di routing con un percorso con caratteri jolly corrispondente
3. Se non viene trovata alcuna regola di routing con un percorso corrispondente, rifiutare la richiesta e restituire una risposta HTTP di errore 400 - Richiesta non valida.

>[!NOTE]
> Eventuali percorsi senza caratteri jolly vengono considerati percorsi di corrispondenza esatta. Anche se il percorso termina con una barra, viene comunque considerato una corrispondenza esatta.

Per chiarire ulteriormente il processo, esaminiamo un altro set di esempi:

| Regola di routing | Host front-end    | Percorso     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Tale configurazione produrrà la tabella corrispondente di esempio seguente :

| Richiesta in ingresso    | Route corrispondente |
|---------------------|---------------|
| \.contoso.com/www            | A             |
| \.contoso.com/a www           | B             |
| \.contoso.com/AB www          | C             |
| \.contoso.com/ABC www         | D             |
| \.contoso.com/abzzz www       | B             |
| \.contoso.com/ABC/www        | E             |
| \.contoso.com/ABC/d www       | F             |
| \.contoso.com/ABC/def www     | G             |
| \.contoso.com/ABC/defzzz www  | F             |
| \.contoso.com/ABC/def/ghi www | F             |
| \.contoso.com/path www        | B             |
| \.contoso.com/path/www       | H             |
| \.contoso.com/path/zzz www    | B             |

>[!WARNING]
> </br> Se non sono presenti regole di routing per un host front-end con corrispondenza esatta con un percorso di route catch-all (`/*`), non vi sarà alcuna corrispondenza con alcuna regola di routing.
>
> Configurazione di esempio:
>
> | Route | Host             | Percorso    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Tabella corrispondente:
>
> | Richiesta in ingresso       | Route corrispondente |
> |------------------------|---------------|
> | profile.domain.com/other | Nessuna. Errore 400 - Richiesta non valida |

### <a name="routing-decision"></a>Decisione di routing

Una volta che la porta anteriore di Azure standard/Premium corrisponde a una singola regola di routing, è necessario scegliere come elaborare la richiesta. Se Azure front door standard/Premium dispone di una risposta memorizzata nella cache per la regola di routing corrispondente, la richiesta viene rifornita al client. L'aspetto successivo di Azure front door standard/Premium valuta se è presente o meno un set di regole per la regola di routing corrispondente. Se non è definito un set di regole, la richiesta viene trasmessa al pool back-end così com'è. In caso contrario, il set di regole viene eseguito nell'ordine in cui sono state configurate.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
