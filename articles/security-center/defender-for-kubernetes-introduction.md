---
title: Azure Defender per Kubernetes - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1f013f22b482c1e1d093f106bd786be870103f3d
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008503"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduzione ad Azure Defender per Kubernetes

Il servizio Azure Kubernetes è il servizio gestito da Microsoft per lo sviluppo, la distribuzione e la gestione di applicazioni in contenitori.

Il Centro sicurezza di Azure e AKS costituiscono un'offerta di sicurezza Kubernetes nativa del cloud con protezione avanzata dell'ambiente, protezione del carico di lavoro e protezione in fase di esecuzione, come descritto in [sicurezza dei contenitori nel centro sicurezza](container-security.md).

Per rilevare le minacce per i cluster Kubernetes, abilitare **Azure Defender per Kubernetes**.

Il rilevamento delle minacce a livello di host per i nodi AKS di Linux è disponibile se si abilita [Azure Defender per i server](defender-for-servers-introduction.md) e il relativo agente di log Analytics. Tuttavia, se il cluster AKS è distribuito in un set di scalabilità di macchine virtuali, l'agente Log Analytics non è attualmente supportato.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|**Azure Defender per Kubernetes** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Autorizzazioni e ruoli obbligatori:|Il ruolo **Amministratore della sicurezza** può ignorare gli avvisi.<br>Il **ruolo con autorizzazioni di lettura per la sicurezza** può visualizzare i risultati.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Quali sono i vantaggi di Azure Defender per Kubernetes?

Azure Defender per Kubernetes offre la **protezione dalle minacce a livello di cluster** monitorando i servizi gestiti da AKS tramite i log recuperati dal servizio Azure KUBERNETES (AKS).

Esempi di eventi di sicurezza monitorati da Azure Defender per Kubernetes includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili. Per un elenco completo degli avvisi a livello di cluster AKS, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

> [!TIP]
> È possibile simulare gli avvisi relativi ai contenitori seguendo le istruzioni riportate in [questo post di blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Inoltre, il team globale di ricercatori Microsoft che si occupano di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici dei contenitori man mano che vengono individuati.

>[!NOTE]
> Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Kubernetes di Azure che si verificano **dopo aver** abilitato Azure Defender per Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender per Kubernetes - Domande frequenti

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>È possibile ottenere le protezioni del servizio Azure Kubernetes anche senza l'agente di Log Analytics?

Il piano **di Azure Defender per Kubernetes** offre protezioni a livello di cluster. Se si distribuisce anche l'agente di Log Analytics di **Azure Defender per i server**, si otterrà la protezione dalle minacce per i nodi forniti con il piano. Per altre informazioni, vedere [Introduzione ad Azure Defender per i server](defender-for-servers-introduction.md).

È consigliabile distribuire entrambi per ottenere la protezione più completa possibile.

Se si sceglie di non installare gli agenti negli host, si riceverà solo una parte dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Si riceveranno comunque gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Il servizio Azure Kubernetes consente di installare estensioni di VM personalizzate nei nodi del servizio?
Per essere monitorati da Azure Defender, i nodi del servizio Azure Kubernetes devono eseguire l'agente di Log Analytics. 

Il servizio Azure Kubernetes è un servizio gestito e, poiché l'agente di log Analytics è un'estensione gestita da Microsoft, è supportato anche nei cluster del servizio Azure Kubernetes.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Se il cluster esegue già un agente di Monitoraggio di Azure per i contenitori, è necessario anche l'agente di Log Analytics?
Per essere monitorati da Azure Defender, i nodi del servizio Azure Kubernetes devono eseguire l'agente di Log Analytics.

Se i cluster eseguono già l'agente di Monitoraggio di Azure per i contenitori, è possibile installare anche l'agente di Log Analytics e i due agenti possono interagire tra loro senza problemi.

[Altre informazioni sull'agente di Monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sulla protezione di Kubernetes del Centro sicurezza, incluso Azure Defender per Kubernetes. 

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](security-center-pricing.md#enable-azure-defender)

Per i materiali correlati, vedere gli articoli seguenti: 

- [Trasmettere avvisi a una soluzione SIEM, SOAR o di gestione dei servizi IT](export-to-siem.md)
- [Tabella di riferimento degli avvisi](alerts-reference.md)
