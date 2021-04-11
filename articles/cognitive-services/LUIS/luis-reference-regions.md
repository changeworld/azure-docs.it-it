---
title: Aree di pubblicazione & endpoint-LUIS
description: L'area specificata nella portale di Azure è la stessa in cui verrà pubblicata l'app LUIS e viene generato un URL dell'endpoint per la stessa area.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: fd02bf5c3291569b71416392b651967e0da701d9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226668"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Creazione e pubblicazione di aree e chiavi associate

Le aree di authoring LUIS sono supportate dal portale LUIS. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Aree di creazione LUIS

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS ha un portale che è possibile usare indipendentemente dall'area, [www.Luis.ai](https://www.luis.ai). È comunque necessario creare e pubblicare nella stessa area.

Le aree di creazione hanno [associato aree di failover](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Aree di pubblicazione e risorse di Azure

L'app è pubblicata in tutte le regioni associate alle risorse LUIS aggiunte nel portale di LUIS. Per un'app creata in [www.Luis.ai][www.luis.ai], ad esempio, se si crea una risorsa Luis o cognitive Service in **westus** e la si [aggiunge all'app come risorsa](luis-how-to-azure-subscription.md), l'app viene pubblicata in tale area.

## <a name="public-apps"></a>App pubbliche
Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la propria chiave di risorsa.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Le aree di pubblicazione sono associate alle aree di creazione

L'app della regione di creazione può essere pubblicata solo in una regione di pubblicazione corrispondente. Se l'app si trova nella regione di creazione errata, esportarla e importarla nella regione di creazione corretta per la regione di pubblicazione.

> [!NOTE]
> Le app LUIS create in https://www.luis.ai possono ora essere pubblicate in tutti gli endpoint, incluse le aree [europee](#publishing-to-europe) e [australiane](#publishing-to-australia) .

## <a name="publishing-to-europe"></a>Pubblicazione in Europa

 Regione globale | Area API di creazione | Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Europa | `westeurope`| Francia centrale<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa settentrionale<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa occidentale<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Regno Unito meridionale<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Svizzera settentrionale<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Pubblicazione in Australia

 Regione globale | Area API di creazione | Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Australia | `australiaeast` | Australia orientale<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Altre aree di pubblicazione

 Regione globale | Area API di creazione | Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Africa | `westus`<br>[www.luis.ai][www.luis.ai]| Sudafrica settentrionale<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| India centrale<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia orientale<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone orientale<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone occidentale<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Corea centrale<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia sud-orientale<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| UAE del Nord<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Canada centrale<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centrali<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-settentrionali<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-meridionali<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-occidentali<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| America del Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brasile meridionale<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Endpoint

Altre informazioni sugli [endpoint di creazione e stima](developer-reference-resource.md).

## <a name="failover-regions"></a>Aree di failover

Per ogni area è presente un'area secondaria in cui eseguire il failover. L'Europa esegue il failover all'interno dell'Europa e l'Australia esegue il failover in Australia.

Per le aree di creazione sono state [associate aree di failover](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimento a entità predefinite](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai