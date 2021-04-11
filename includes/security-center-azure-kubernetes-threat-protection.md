---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029141"
---
Azure Defender fornisce la protezione dalle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Azure Defender offre protezione dalle minacce a diversi livelli: 

* **Livello host (fornito da Azure Defender per i server)** : usando lo stesso agente log Analytics usato dal centro sicurezza in altre macchine virtuali, Azure Defender monitora i nodi Kubernetes Linux per attività sospette, ad esempio il rilevamento della shell Web e la connessione con indirizzi IP sospetti noti. L'agente monitora anche le analisi specifiche dei contenitori, come la creazione di contenitori con privilegi, l'accesso sospetto ai server API e la presenza di server SSH (Secure Shell) in esecuzione all'interno di un contenitore Docker.

    Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

    >[!IMPORTANT]
    > Attualmente non è supportata l'installazione dell'agente di Log Analytics nei cluster di servizi Kubernetes di Azure in esecuzione nei set di scalabilità di macchine virtuali.

    Per un elenco degli avvisi a livello di cluster, vedere la [tabella di riferimento degli avvisi](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Livello di cluster (fornito da Azure Defender per Kubernetes)** : a livello di cluster, la protezione dalle minacce si basa sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio **senza agente**, abilitare Azure Defender. Se il cluster è in locale o in un altro provider di servizi cloud, abilitare [Arc Enabled Kubernetes e l'estensione di Azure Defender](../articles/security-center/defender-for-kubernetes-azure-arc.md).

    Per generare avvisi a questo livello, Azure Defender monitora i log del cluster. Esempi di eventi a questo livello includono dashboard di Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    >[!NOTE]
    > Azure Defender genera avvisi di sicurezza per le azioni e le distribuzioni che si verificano dopo aver abilitato il piano Defender per Kubernetes sulla sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster, vedere la [tabella di riferimento degli avvisi](../articles/security-center/alerts-reference.md#alerts-akscluster).

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).