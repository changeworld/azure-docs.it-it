---
title: Ridimensionamento e hosting di Funzioni di Azure
description: Scopri come scegliere tra il piano a consumo di funzioni di Azure e il piano Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936763"
---
# <a name="azure-functions-hosting-options"></a>Opzioni di hosting di funzioni di Azure

Quando si crea un'app per le funzioni in Azure, è necessario scegliere un piano di hosting per l'app. Sono disponibili tre piani di hosting di base per funzioni di Azure: piano a [consumo](consumption-plan.md), [piano Premium](functions-premium-plan.md)e [piano dedicato (servizio app)](dedicated-plan.md). Tutti i piani di hosting sono disponibili a livello generale nelle macchine virtuali Linux e Windows.

Il piano di hosting scelto determina i comportamenti seguenti:

* Modalità di ridimensionamento dell'app per le funzioni.
* Risorse disponibili per ogni istanza dell'app per le funzioni.
* Supporto per le funzionalità avanzate, ad esempio la connettività di rete virtuale di Azure.

Questo articolo fornisce un confronto dettagliato tra i diversi piani di hosting, oltre all'hosting basato su Kubernetes.

## <a name="overview-of-plans"></a>Panoramica dei piani

Di seguito è riportato un riepilogo dei vantaggi dei tre piani di hosting principali per le funzioni:

| | |
| --- | --- |  
|**[Piano a consumo](consumption-plan.md)**| Ridimensiona automaticamente e paga solo per le risorse di calcolo quando le funzioni sono in esecuzione.<br/><br/>Nel piano a consumo, le istanze dell'host funzioni vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso.<br/><br/> ✔ Piano di hosting predefinito.<br/>✔ Pagare solo quando le funzioni sono in esecuzione.<br/>✔ Ridimensiona automaticamente, anche durante i periodi di carico elevato.|  
|**[Piano Premium](functions-premium-plan.md)**|Esegue automaticamente il ridimensionamento in base alla richiesta utilizzando i thread di lavoro pre-riscaldati che eseguono applicazioni senza alcun ritardo dopo essere inattivi, vengono eseguite in istanze più potenti e si connettono a reti virtuali. <br/><br/>Si consideri il piano Premium di funzioni di Azure nelle situazioni seguenti: <br/><br/>✔ Le app per le funzioni vengono eseguite in modo continuo o quasi continuo.<br/>✔ Si dispone di un numero elevato di esecuzioni di piccole dimensioni e di una fattura di esecuzione elevata, ma con un numero di secondi inferiore nel piano a consumo.<br/>✔ Sono necessarie più opzioni di CPU o memoria rispetto a quelle fornite dal piano a consumo.<br/>✔ Il codice deve essere eseguito più a lungo del tempo di esecuzione massimo consentito nel piano a consumo.<br/>✔ Sono necessarie funzionalità che non sono disponibili nel piano a consumo, ad esempio la connettività di rete virtuale.|  
|**[Piano dedicato](dedicated-plan.md)** |Eseguire le funzioni all'interno di un piano di servizio app alle normali [tariffe del piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Ideale per scenari con esecuzione prolungata in cui non è possibile usare [Durable Functions](durable/durable-functions-overview.md) . Si consideri un piano di servizio app nelle situazioni seguenti:<br/><br/>✔ Sono presenti VM sottoutilizzate esistenti che eseguono già altre istanze del servizio app.<br/>✔ Si desidera fornire un'immagine personalizzata su cui eseguire le funzioni. <br/>Sono necessari ✔ il ridimensionamento predittivo e i costi.|  

Le tabelle di confronto in questo articolo includono anche le opzioni di hosting seguenti, che forniscono la massima quantità di controllo e isolamento in cui eseguire le app per le funzioni.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | Ambiente del servizio app (ASE) è una funzionalità del servizio app che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app su vasta scala.<br/><br/>Gli ambienti sono appropriati per i carichi di lavoro delle applicazioni che richiedono: <br/><br/>✔ Scalabilità molto elevata.<br/>✔ L'isolamento completo del calcolo e l'accesso alla rete sicuro.<br/>✔ Utilizzo elevato della memoria.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes fornisce un ambiente completamente isolato e dedicato in esecuzione nella piattaforma Kubernetes.<br/><br/> Kubernetes è appropriato per i carichi di lavoro delle applicazioni che richiedono: <br/>✔ Requisiti hardware personalizzati.<br/>Isolamento ✔ e accesso alla rete protetto.<br/>✔ La possibilità di eseguire in un ambiente ibrido o multicloud.<br/>✔ Eseguiti insieme a servizi e applicazioni Kubernetes esistenti.|  

Le tabelle rimanenti di questo articolo confrontano i piani su diverse funzionalità e comportamenti. Per un confronto dei costi tra i piani di hosting dinamici (consumo e Premium), vedere la [pagina dei prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/). Per i prezzi delle varie opzioni di piano dedicato, vedere la [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Sistema operativo/Runtime

La tabella seguente illustra il supporto del sistema operativo e del runtime di linguaggio supportati per i piani di hosting.

| | Linux<sup>1</sup><br/>Solo codice | Windows<sup>2</sup><br/>Solo codice | Linux<sup>1, 3</sup><br/>Contenitore Docker |
| --- | --- | --- | --- |
| **[Piano a consumo](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Nessun supporto  |
| **[Piano Premium](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Piano dedicato](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/d | n/d |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux è l'unico sistema operativo supportato per lo stack di runtime di Python. <br/>
<sup>2</sup> Windows è l'unico sistema operativo supportato per lo stack del runtime di PowerShell.<br/>
<sup>3</sup> Linux è l'unico sistema operativo supportato per i contenitori docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Scalabilità

Nella tabella seguente vengono confrontati i comportamenti di ridimensionamento dei vari piani di hosting.

| | Aumentare il numero di istanze | Numero massimo di istanze |
| --- | --- | --- |
| **[Piano a consumo](consumption-plan.md)** | [Gestito da eventi](event-driven-scaling.md). Aumento automatico del numero di istanze anche in periodo di carico elevato. L'infrastruttura di funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi trigger in ingresso. | 200 |
| **[Piano Premium](functions-premium-plan.md)** | [Gestito da eventi](event-driven-scaling.md). Aumento automatico del numero di istanze anche in periodo di carico elevato. L'infrastruttura di funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni, in base al numero di eventi su cui vengono attivate le relative funzioni. |100|
| **[Piano](dedicated-plan.md)**<sup>1</sup> dedicato | Scalabilità manuale/automatica |10-20|
| **[](dedicated-plan.md)** Ambiente del servizio app <sup>1</sup> | Scalabilità manuale/automatica |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Scalabilità automatica guidata dagli eventi per i cluster Kubernetes con [Keda](https://keda.sh). | Varia &nbsp; a seconda del &nbsp; cluster&nbsp;&nbsp;|

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Comportamento di avvio a freddo

|    |    | 
| -- | -- |
| **[Piano a consumo &nbsp;](consumption-plan.md)** | Le app possono essere ridimensionate a zero quando inattive, ovvero alcune richieste potrebbero avere latenza aggiuntiva all'avvio.  Il piano a consumo ha alcune ottimizzazioni che consentono di ridurre l'ora di avvio a freddo, incluso il pull da funzioni segnaposto pre-riscaldate che hanno già l'host funzione e i processi del linguaggio in esecuzione. |
| **[Piano Premium](functions-premium-plan.md)** | Per evitare l'avvio a freddo, le istanze sono perennemente calde. |
| **[Piano dedicato](dedicated-plan.md)** | Quando è in esecuzione in un piano dedicato, l'host di funzioni può essere eseguito in modo continuo, il che significa che l'avvio a freddo non è realmente un problema. |
| **[ASE](dedicated-plan.md)** | Quando è in esecuzione in un piano dedicato, l'host di funzioni può essere eseguito in modo continuo, il che significa che l'avvio a freddo non è realmente un problema. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | A seconda della configurazione di KEDA, le app possono essere configurate per evitare l'avvio a freddo. Se è configurato per la scalabilità a zero, viene rilevato un avvio a freddo per i nuovi eventi. 

## <a name="service-limits"></a>Limiti del servizio

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Funzionalità di rete

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Fatturazione

| | | 
| --- | --- |
| **[Piano a consumo](consumption-plan.md)** | Pagare solo per il tempo di esecuzione delle funzioni. La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. |
| **[Piano Premium](functions-premium-plan.md)** | Il piano Premium è basato sul numero di secondi e di memoria Core usati nelle istanze necessarie e pre-surriscaldate. È necessario che almeno un'istanza per piano rimanga sempre calda. Questo piano offre i prezzi più prevedibili. |
| **[Piano dedicato](dedicated-plan.md)* | Si paga lo stesso per le app per le funzioni in un piano di servizio app come per le altre risorse del servizio app, ad esempio le app Web.|
| **[Ambiente del servizio app](dedicated-plan.md)** | Esiste una tariffa mensile fissa per un ambiente del servizio app che paga l'infrastruttura e non cambia con le dimensioni dell'ambiente del servizio app. Esiste anche un costo per ogni piano di servizio app vCPU. Tutte le app ospitate in un ambiente del servizio app fanno parte di uno SKU di prezzi isolato. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Si pagano solo i costi del cluster Kubernetes. nessuna fatturazione aggiuntiva per le funzioni. L'app per le funzioni viene eseguita come carico di lavoro dell'applicazione nella parte superiore del cluster, proprio come per una normale app. |

## <a name="next-steps"></a>Passaggi successivi

+ [Tecnologie di distribuzione in funzioni di Azure](functions-deployment-technologies.md) 
+ [Guida per sviluppatori di Funzioni di Azure](functions-reference.md)