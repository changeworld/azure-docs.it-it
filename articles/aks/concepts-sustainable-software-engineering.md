---
title: Concetti-ingegneria del software sostenibile nei servizi Kubernetes di Azure (AKS)
description: Scopri di più sulla progettazione software sostenibile in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011492"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Principi di progettazione software sostenibili in Azure Kubernetes Service (AKS)

I principi di progettazione software sostenibili sono un insieme di competenze che consentono di definire, compilare ed eseguire applicazioni sostenibili. L'obiettivo complessivo è quello di ridurre l'impronta di carbonio di ogni aspetto dell'applicazione. [I principi della progettazione di software sostenibili][principles-sse] hanno una panoramica dei principi della progettazione software sostenibile.

L'ingegneria del software sostenibile è uno spostamento nelle priorità e nello stato attivo. In molti casi, la maggior parte del software è progettata ed eseguita in modo da ottenere prestazioni rapide e bassa latenza. Nel frattempo, l'ingegneria del software sostenibile è incentrata sulla riduzione del maggior volume possibile di emissioni di CO2. Tenere in considerazione:

* L'applicazione di principi di progettazione software sostenibili può offrire prestazioni più elevate o una minore latenza, ad esempio abbassando il totale dei viaggi di rete. 
* Ridurre le emissioni di CO2 può causare prestazioni più lente o una latenza maggiore, ad esempio ritardare i carichi di lavoro con priorità bassa. 

Prima di applicare principi di progettazione software sostenibili alla propria applicazione, rivedere le priorità, le esigenze e i compromessi dell'applicazione.

## <a name="measure-and-optimize"></a>Misura e ottimizza

Per ridurre il footprint di carbonio dei cluster AKS, è necessario comprendere come vengono usate le risorse del cluster. [Monitoraggio di Azure][azure-monitor] fornisce informazioni dettagliate sull'utilizzo delle risorse del cluster, ad esempio l'utilizzo della memoria e della CPU. Questi dati segnalano la decisione di ridurre l'impronta di carbonio del cluster e osservare l'effetto delle modifiche. 

È anche possibile installare [Microsoft Sustainability Calculator][sustainability-calculator] per visualizzare l'impronta di carbonio di tutte le risorse di Azure.

## <a name="increase-resource-utilization"></a>Aumentare l'utilizzo delle risorse

Un approccio per abbassare il footprint di CO2 consiste nel ridurre il tempo di inattività. La riduzione del tempo di inattività comporta l'aumento dell'utilizzo delle risorse di calcolo. Ad esempio:
1. Nel cluster sono presenti quattro nodi, ognuno in esecuzione con una capacità pari al 50%. Quindi, tutti e quattro i nodi hanno una capacità inutilizzata del 50% rimane inattiva. 
1. Il cluster è stato ridotto a tre nodi, ognuno in esecuzione con una capacità pari al 67% con lo stesso carico di lavoro. È stato possibile ridurre la capacità inutilizzata al 33% in ogni nodo e aumentare l'utilizzo.

> [!IMPORTANT]
> Quando si valuta la modifica delle risorse nel cluster, verificare che i [pool di sistema][system-pools] dispongano di risorse sufficienti per mantenere la stabilità dei componenti di sistema principali del cluster. **Non ridurre mai** le risorse del cluster fino al punto in cui il cluster potrebbe diventare instabile.

Dopo aver esaminato l'utilizzo del cluster, provare a usare le funzionalità offerte da [più pool di nodi][multiple-node-pools]: 

* Dimensionamento nodo

    Usare il [ridimensionamento dei nodi][node-sizing] per definire i pool di nodi con profili di CPU e memoria specifici, consentendo di personalizzare i nodi in base alle esigenze del carico di lavoro. Ridimensionando i nodi in base alle esigenze del carico di lavoro, è possibile eseguire alcuni nodi a un utilizzo superiore. 

* Scalabilità del cluster

    Configurare la [scalabilità][scale]del cluster. Usare il [ridimensionamento automatico del Pod orizzontale][scale-horizontal] e il servizio di [scalabilità][scale-auto] automatica del cluster per ridimensionare automaticamente il cluster in base alla configurazione. Controllare la scalabilità del cluster per mantenere tutti i nodi in esecuzione a un utilizzo elevato e rimanere sincronizzati con le modifiche apportate al carico di lavoro del cluster. 

* Pool di spot

    Per i casi in cui un carico di lavoro è tollerante a interruzioni o interruzioni improvvise, è possibile usare i [pool di spot][spot-pools]. I pool di spot sfruttano la capacità inattiva in Azure. Ad esempio, i pool di spot possono funzionare correttamente per i processi batch o gli ambienti di sviluppo.

> [!NOTE]
>L'aumento dell'utilizzo può anche ridurre i nodi in eccesso, riducendo così l'energia utilizzata dalle [prenotazioni di risorse in ogni nodo][resource-reservations].

Infine, esaminare le *richieste* di CPU e memoria e i *limiti* nei manifesti Kubernetes delle applicazioni. 
* Quando si riducono i valori di memoria e CPU, per il cluster sono disponibili più memoria e CPU per eseguire altri carichi di lavoro. 
* Quando si eseguono più carichi di lavoro con CPU e memoria inferiori, il cluster viene allocato in modo più denso, aumentando così l'utilizzo. 

Quando si riduce la CPU e la memoria per le applicazioni, è possibile che il comportamento delle applicazioni risulti degradato o instabile se si impostano valori di CPU e memoria troppo bassi. Prima di modificare le *richieste* e i *limiti* della CPU e della memoria, eseguire alcuni test di benchmarking per verificare se i valori sono impostati in modo appropriato. Non ridurre mai questi valori al punto di instabilità dell'applicazione.

## <a name="reduce-network-travel"></a>Ridurre i viaggi di rete

Riducendo le richieste e le risposte a distanza da e verso il cluster, è possibile ridurre le emissioni di CO2 e il consumo di energia elettrica mediante i dispositivi di rete. Dopo aver esaminato il traffico di rete, è consigliabile creare cluster [in aree][regions] più vicine all'origine del traffico di rete. È possibile usare [Gestione traffico di Azure][azure-traffic-manager] per instradare il traffico al cluster più vicino e ai [gruppi di posizionamento di prossimità][proiximity-placement-groups] e ridurre la distanza tra le risorse di Azure.

> [!IMPORTANT]
> Quando si considerano le modifiche apportate alla rete del cluster, non ridurre mai i viaggi di rete al costo di soddisfare i requisiti del carico di lavoro. Quando si usano le [zone di disponibilità][availability-zones] , ad esempio, è possibile che si verifichi un maggior numero di spostamenti di rete nel cluster, per gestire i requisiti del carico di lavoro.

## <a name="demand-shaping"></a>Data Shaping

Laddove possibile, provare a spostare la richiesta per le risorse del cluster in orari o aree in cui è possibile usare la capacità in eccesso. Ad esempio, considerare quanto segue:
* Modifica dell'ora o dell'area per l'esecuzione di un processo batch.
* Uso di [pool di spot][spot-pools]. 
* Refactoring dell'applicazione per l'uso di una coda per rinviare i carichi di lavoro in esecuzione che non necessitano di elaborazione immediata.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità di AKS citate in questo articolo:

* [Pool di più nodi][multiple-node-pools]
* [Dimensionamento nodo][node-sizing]
* [Scalabilità di un cluster][scale]
* [Utilità di scalabilità automatica orizzontale dei pod][scale-horizontal]
* [Utilità di scalabilità automatica dei cluster][scale-auto]
* [Pool di spot][spot-pools]
* [Pool di sistema][system-pools]
* [Prenotazioni di risorse][resource-reservations]
* [Gruppi di selezione host di prossimità][proiximity-placement-groups]
* [Zone di disponibilità][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/