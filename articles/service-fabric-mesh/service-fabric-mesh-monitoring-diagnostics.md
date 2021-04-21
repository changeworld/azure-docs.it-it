---
title: Monitoraggio e diagnostica nelle Azure Service Fabric Mesh app
description: Informazioni sul monitoraggio e sulla diagnostica dell'applicazione in mesh Service Fabric su Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766170"
---
# <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

> [!IMPORTANT]
> L'anteprima Azure Service Fabric Mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric Mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere Azure Service Fabric Mesh [ritiro dell'anteprima.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Il monitoraggio e la diagnostica di mesh Service Fabric sono suddivisi in tre tipi principali di dati di diagnostica:

- Registri applicazioni: sono definiti come i registri delle applicazioni in contenitori, in base al modo in cui è stata instrumentata l'applicazione (ad esempio, log di Docker)
- Eventi piattaforma: eventi dalla piattaforma Mesh relativi all'operazione del contenitore, che attualmente includono l'attivazione, la disattivazione e la terminazione del contenitore.
- Metriche contenitore: uso delle risorse e metriche delle prestazioni per i contenitori (statistiche Docker)

Questo articolo illustra le opzioni di monitoraggio e diagnostica per la versione di anteprima più recente disponibile.

## <a name="application-logs"></a>Log applicazioni

È possibile visualizzare i log di Docker dai contenitori distribuiti, in base al contenitore. Nel modello di applicazione di mesh Service Fabric, ogni contenitore è un pacchetto di codice nell'applicazione. Per visualizzare i log associati a un pacchetto di codice usare il comando seguente:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> È possibile usare il comando "az mesh service-replica" per ottenere il nome della replica. I nomi delle repliche incrementano i numeri interi da 0.

Ecco come appare per visualizzare i log dal contenitore VotingWeb.Code dall'applicazione di voto:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriche dei contenitori 

L'ambiente Mesh espone alcune metriche che indicano le prestazioni dei contenitori. Le metriche seguenti sono disponibili tramite l'interfaccia della riga portale di Azure e l'interfaccia della riga di comando di Monitoraggio di Azure:

| Metrica | Descrizione | Unità|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu come percentuale | % |
| MemoryUtilization | ActualMem/AllocatedMem come percentuale | % |
| AllocatedCpu | Cpu allocata in base Azure Resource Manager modello | Millicores |
| AllocatedMemory | Memoria allocata in base Azure Resource Manager modello | MB |
| ActualCpu | Utilizzo CPU | Millicores |
| ActualMemory | Utilizzo memoria | MB |
| ContainerStatus | 0 - Non valido: lo stato del contenitore è sconosciuto <br> 1 - In sospeso: è stato pianificato l'avvio del contenitore <br> 2 - Avvio: il contenitore è in corso di avvio <br> 3 - Avviato: il contenitore è stato avviato correttamente <br> 4 - Arresto: il contenitore è in fase di arresto <br> 5 - Arrestato: il contenitore è stato arrestato correttamente | N/D |
| ApplicationStatus | 0 - Sconosciuto: lo stato non è recuperabile <br> 1 - Pronto: l'applicazione è in esecuzione correttamente <br> 2 - Aggiornamento: È in corso un aggiornamento <br> 3 - Creazione: è in corso la creazione dell'applicazione <br> 4 - Eliminazione: l'applicazione è in fase di eliminazione <br> 5 - Operazione non riuscita: non è stato possibile distribuire l'applicazione | N/D |
| ServiceStatus | 0 - Non valido: il servizio non ha attualmente uno stato di integrità <br> 1 - Ok: il servizio è integro  <br> 2 - Avviso: è possibile che si sia verificato un errore durante l'analisi <br> 3 - Errore: Si è verificato un errore che richiede l'analisi <br> 4 - Sconosciuto: lo stato non è recuperabile | N/D |
| ServiceReplicaStatus | 0 - Non valido: la replica non ha attualmente uno stato di integrità <br> 1 - Ok: il servizio è integro  <br> 2 - Avviso: è possibile che si sia verificato un errore durante l'analisi <br> 3 - Errore: Si è verificato un errore che richiede l'analisi <br> 4 - Sconosciuto: lo stato non è recuperabile | N/D | 
| RestartCount | Numero di riavvii del contenitore | N/D |

> [!NOTE]
> I valori ServiceStatus e ServiceReplicaStatus sono gli stessi di [HealthState](/dotnet/api/system.fabric.health.healthstate) in Service Fabric.

Ogni metrica è disponibile in dimensioni diverse, quindi è possibile visualizzare le aggregazioni a livelli diversi. L'elenco corrente delle dimensioni è il seguente:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> La dimensione CodePackageName non è disponibile per le applicazioni Linux. 

Ogni dimensione corrisponde a diversi componenti del modello di [Service Fabric application](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Monitoraggio di Azure'interfaccia della riga di comando

Un elenco completo dei comandi è disponibile nella documentazione [Monitoraggio di Azure'interfaccia](/cli/azure/monitor/metrics#az_monitor_metrics_list) della riga di comando, ma di seguito sono stati inclusi alcuni esempi utili 

In ogni esempio, l'ID risorsa segue questo modello

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilizzo della CPU dei contenitori in un'applicazione

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilizzo della memoria per ogni replica del servizio
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Riavvii per ogni contenitore in una finestra di 1 ora 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilizzo medio della CPU tra i servizi denominati "VotingWeb" in una finestra di 1 ora
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Esplora metriche

Esplora metriche è un pannello nel portale in cui è possibile visualizzare tutte le metriche per l'applicazione Mesh. Questo pannello è accessibile nella pagina dell'applicazione nel portale e nel pannello Monitoraggio di Azure, quest'ultimo che è possibile usare per visualizzare le metriche per tutte le risorse di Azure che supportano Monitoraggio di Azure. 

![Esplora metriche](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
* Per altre informazioni sui comandi Monitoraggio di Azure metriche, vedere la documentazione Monitoraggio di Azure [dell'interfaccia della riga di comando.](/cli/azure/monitor/metrics#az_monitor_metrics_list)
