---
title: Azure Defender per Kubernetes - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Kubernetes.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029081"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduzione ad Azure Defender per Kubernetes

Azure Defender per Kubernetes è il piano di Azure Defender che fornisce le protezioni per i cluster Kubernetes ovunque si trovino. 

È possibile difendere i cluster in:

- **Azure Kubernetes Service (AKS)** -servizio gestito di Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori

- **Ambienti locali e multicloud** -uso [di un'estensione per Kubernetes abilitato per Arc](defender-for-kubernetes-azure-arc.md)

Il Centro sicurezza di Azure e AKS costituiscono un'offerta di sicurezza Kubernetes nativa del cloud con protezione avanzata dell'ambiente, protezione del carico di lavoro e protezione in fase di esecuzione, come descritto in [sicurezza dei contenitori nel centro sicurezza](container-security.md).

Il rilevamento delle minacce a livello di host per i nodi AKS di Linux è disponibile se si abilita [Azure Defender per i server](defender-for-servers-introduction.md) e il relativo agente di log Analytics. Tuttavia, se il cluster viene distribuito in un set di scalabilità di macchine virtuali, l'agente Log Analytics non è attualmente supportato.



## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|**Azure Defender per Kubernetes** viene fatturato come indicato nei [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)|
|Autorizzazioni e ruoli obbligatori:|Il ruolo **Amministratore della sicurezza** può ignorare gli avvisi.<br>Il **ruolo con autorizzazioni di lettura per la sicurezza** può visualizzare i risultati.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quali sono i vantaggi di Azure Defender per Kubernetes?

Azure Defender per Kubernetes offre la **protezione dalle minacce a livello di cluster** monitorando i log dei cluster.

Esempi di eventi di sicurezza monitorati da Azure Defender per Kubernetes includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili. Per un elenco completo degli avvisi a livello di cluster, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

>[!NOTE]
> Azure Defender genera avvisi di sicurezza per le azioni e le distribuzioni che si verificano dopo aver abilitato il piano Defender per Kubernetes sulla sottoscrizione.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender per Kubernetes - Domande frequenti

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>È possibile ottenere ancora le protezioni del cluster senza l'agente di Log Analytics?

Il piano **di Azure Defender per Kubernetes** offre protezioni a livello di cluster. Se si distribuisce anche l'agente di Log Analytics di **Azure Defender per i server**, si otterrà la protezione dalle minacce per i nodi forniti con il piano. Per altre informazioni, vedere [Introduzione ad Azure Defender per i server](defender-for-servers-introduction.md).

È consigliabile distribuire entrambi per ottenere la protezione più completa possibile.

Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Il servizio Azure Kubernetes consente di installare estensioni di VM personalizzate nei nodi del servizio?
Per essere monitorati da Azure Defender, i nodi del servizio Azure Kubernetes devono eseguire l'agente di Log Analytics. 

Il servizio Azure Kubernetes è un servizio gestito e, poiché l'agente di log Analytics è un'estensione gestita da Microsoft, è supportato anche nei cluster del servizio Azure Kubernetes.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se il cluster esegue già un agente di Monitoraggio di Azure per i contenitori, è necessario anche l'agente di Log Analytics?
Per eseguire il monitoraggio dei nodi, Azure Defender deve eseguire l'agente di Log Analytics.

Se i cluster eseguono già l'agente di Monitoraggio di Azure per i contenitori, è possibile installare anche l'agente di Log Analytics e i due agenti possono interagire tra loro senza problemi.

[Altre informazioni sull'agente di Monitoraggio di Azure per i contenitori](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sulla protezione di Kubernetes del Centro sicurezza, incluso Azure Defender per Kubernetes. 

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](enable-azure-defender.md)

Per i materiali correlati, vedere gli articoli seguenti: 

- [Trasmettere avvisi a una soluzione SIEM, SOAR o di gestione dei servizi IT](export-to-siem.md)
- [Tabella di riferimento degli avvisi](alerts-reference.md)
