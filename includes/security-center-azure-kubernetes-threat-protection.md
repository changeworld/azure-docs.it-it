---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007814"
---
Il Centro sicurezza offre protezione dalle minacce in tempo reale per gli ambienti basati su contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre protezione dalle minacce a diversi livelli: 

* **Livello di host (fornito da Azure Defender per server)** : usando lo stesso agente di Log Analytics usato dal Centro sicurezza in altre macchine virtuali, Azure Defender monitora i nodi del servizio Azure Kubernetes per Linux per rilevare attività sospette, come il rilevamento della shell Web e la connessione con indirizzi IP sospetti noti. L'agente monitora anche le analisi specifiche dei contenitori, come la creazione di contenitori con privilegi, l'accesso sospetto ai server API e la presenza di server SSH (Secure Shell) in esecuzione all'interno di un contenitore Docker.

    Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

    >[!IMPORTANT]
    > Attualmente non è supportata l'installazione dell'agente di Log Analytics nei cluster di servizi Kubernetes di Azure in esecuzione nei set di scalabilità di macchine virtuali.

    Per un elenco degli avvisi a livello di host del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Livello di cluster del servizio Azure Kubernetes (fornito da Azure Defender per Kubernetes)** : la protezione dalle minacce a questo livello è basata sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio **senza agente**, abilitare Azure Defender. Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti dal servizio Azure Kubernetes usando i log recuperati da quest'ultimo. Esempi di eventi a questo livello includono dashboard di Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Azure Kubernetes che si verificano dopo l'abilitazione dell'opzione Kubernetes nelle impostazioni della sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster del servizio Azure Kubernetes, vedere la [tabella di riferimento degli avvisi](../articles/security-center/alerts-reference.md#alerts-akscluster).

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).