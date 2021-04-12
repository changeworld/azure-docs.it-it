---
title: Riferimento ai dati di monitoraggio Azure Machine Learning | Microsoft Docs
description: Documentazione di riferimento per il monitoraggio Azure Machine Learning. Informazioni sui dati & risorse raccolte e disponibili in monitoraggio di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031070"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitoraggio del riferimento ai dati di Azure Machine Learning

Informazioni sui dati e sulle risorse raccolti da monitoraggio di Azure dall'area di lavoro Azure Machine Learning. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Metriche

Questa sezione elenca tutte le metriche della piattaforma raccolte automaticamente raccolte per Azure Machine Learning. Il provider di risorse per queste metriche è [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modello**

| Metrica | Unità | Descrizione |
|--|--|--|
| Model Register Succeeded (Registrazione di modelli riuscita) | Conteggio | Numero di registrazioni del modello riuscite in questa area di lavoro |
| Model Register Failed (Registrazione di modelli non riuscita) | Conteggio | Numero di registrazioni del modello non riuscite in questa area di lavoro |
| Model Deploy Started (Distribuzione di modelli avviata) | Conteggio | Numero di distribuzioni di modelli avviate in questa area di lavoro |
| Model Deploy Succeeded (Distribuzione di modelli riuscita) | Conteggio | Numero di distribuzioni di modelli riuscite in questa area di lavoro |
| Model Deploy Failed (Distribuzione di modelli non riuscita) | Conteggio | Numero di distribuzioni di modelli non riuscite in questa area di lavoro |

**Quota**

Le informazioni sulle quote sono destinate solo ai Azure Machine Learning di calcolo.

| Metrica | Unità | Descrizione |
|--|--|--|
| Totale nodi | Conteggio | Numero totale di nodi. Il totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi con precedenza, lasciando nodi |
| Active Nodes (Nodi attivi) | Conteggio | Numero di nodi attivi. Nodi che eseguono attivamente un processo. |
| Idle Nodes (Nodi inattivi) | Conteggio | Numero di nodi inattivi. I nodi inattivi sono i nodi che non eseguono alcun processo, ma possono accettare un nuovo processo, se disponibile. |
| Unusable Nodes (Nodi non utilizzabili) | Conteggio | Numero di nodi inutilizzabili. I nodi inutilizzabili non sono funzionali a causa di un problema irrisolvibile. Azure ricicla questi nodi. |
| Preempted Nodes (Nodi superati) | Conteggio | Numero di nodi con precedenza. Questi nodi sono i nodi con priorità bassa che vengono sottratti dal pool di nodi disponibile. |
| Leaving Nodes (Nodi di uscita) | Conteggio | Numero di nodi in uscita. Lasciando i nodi sono i nodi che hanno appena terminato l'elaborazione di un processo e passano allo stato inattivo. |
| Core totali | Conteggio | Numero di core totali |
| Active Cores (Core attivi) | Conteggio | Numero di core attivi |
| Idle Cores (Core inattivi) | Conteggio | Numero di core inattivi |
| Unusable Cores (Core non utilizzabili) | Conteggio | Numero di core inutilizzabili |
| Preempted Cores (Core superati) | Conteggio | Numero di core con precedenza |
| Leaving Cores (Core di uscita) | Conteggio | Numero di core in uscita |
| Quota Utilization Percentage (Percentuale di utilizzo quota) | Conteggio | Percentuale di quota utilizzata |

**Risorsa**

| Metrica| Unità | Descrizione |
|--|--|--|
| CpuUtilization | Conteggio | Percentuale di utilizzo in un nodo della CPU. L'utilizzo viene segnalato a intervalli di un minuto. |
| GpuUtilization | Conteggio | Percentuale di utilizzo in un nodo GPU. L'utilizzo viene segnalato a intervalli di un minuto. |
| GpuMemoryUtilization | Conteggio | Percentuale di utilizzo della memoria in un nodo GPU. L'utilizzo viene segnalato a intervalli di un minuto. |
| GpuEnergyJoules | Conteggio | Energia di intervallo in Joule in un nodo GPU. L'energia viene segnalata a intervalli di un minuto. |

**Esegui**

Informazioni sulle esecuzioni di training per l'area di lavoro.

| Metrica | Unità | Descrizione |
|--|--|--|
| Esecuzioni annullate | Conteggio | Numero di esecuzioni annullate per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione viene annullata correttamente. |
| Annulla esecuzioni richieste | Conteggio | Numero di esecuzioni in cui è stato richiesto l'annullamento per questa area di lavoro. Il conteggio viene aggiornato quando è stata ricevuta una richiesta di annullamento per un'esecuzione. |
| Esecuzioni completate | Conteggio | Il numero di esecuzioni è stato completato correttamente per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è stata completata e l'output è stato raccolto. |
| Esecuzioni non riuscite | Conteggio | Numero di esecuzioni non riuscite per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione ha esito negativo. |
| Finalizzazione delle esecuzioni | Conteggio | Numero di esecuzioni immesse per lo stato di completamento per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è stata completata, ma la raccolta di output è ancora in corso. | 
| Esecuzioni non rispondenti | Conteggio | Numero di esecuzioni non rispondenti per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione entra in uno stato che non risponde. |
| Esecuzioni non avviate | Conteggio | Numero di esecuzioni in stato non avviato per questa area di lavoro. Il conteggio viene aggiornato quando viene ricevuta una richiesta per creare un'esecuzione, ma le informazioni di esecuzione non sono state ancora popolate. |
| Preparazione delle esecuzioni | Conteggio | Numero di esecuzioni preparate per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione entra nello stato di preparazione mentre è in corso la preparazione dell'ambiente di esecuzione. |
| Esecuzioni del provisioning | Conteggio | Numero di esecuzioni di cui viene eseguito il provisioning per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è in attesa della creazione o del provisioning di una destinazione di calcolo. |
| Esecuzioni in coda | Conteggio | Numero di esecuzioni accodate per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione viene accodata nella destinazione di calcolo. Può verificarsi durante l'attesa che i nodi di calcolo necessari siano pronti. |
| Esecuzioni avviate | Conteggio | Numero di esecuzioni in esecuzione per l'area di lavoro. Il conteggio viene aggiornato quando viene avviata l'esecuzione delle risorse necessarie. |
| Avvio delle esecuzioni | Conteggio | Numero di esecuzioni avviate per l'area di lavoro. Il conteggio viene aggiornato dopo la richiesta di creazione delle informazioni di esecuzione ed esecuzione, ad esempio l'ID esecuzione, è stato popolato |
| Errors | Conteggio | Numero di errori di esecuzione in questa area di lavoro. Il conteggio viene aggiornato ogni volta che viene rilevato un errore durante l'esecuzione. |
| Avvisi | Conteggio | Numero di avvisi di esecuzione in questa area di lavoro. Il conteggio viene aggiornato ogni volta che un'esecuzione rileva un avviso. |

## <a name="metric-dimensions"></a>Dimensioni metrica

Per altre informazioni sulle dimensioni della metrica, vedere [metriche multidimensionali](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning presenta le dimensioni seguenti associate alle metriche.

| Dimension | Descrizione |
| ---- | ---- |
| Cluster Name | Nome della risorsa cluster di calcolo. Disponibile per tutte le metriche delle quote. |
| Nome della famiglia di macchine virtuali | Nome della famiglia di macchine virtuali usata dal cluster. Disponibile per la percentuale di utilizzo della quota. |
| Priorità VM | Priorità della macchina virtuale. Disponibile per la percentuale di utilizzo della quota.
| CreatedTime | Disponibile solo per CpuUtilization e GpuUtilization. |
| DeviceId | ID del dispositivo (GPU). Disponibile solo per GpuUtilization. |
| NodeId | ID del nodo creato in cui è in esecuzione il processo. Disponibile solo per CpuUtilization e GpuUtilization. |
| RunId | ID dell'esecuzione/processo. Disponibile solo per CpuUtilization e GpuUtilization. |
| ComputeType | Tipo di calcolo utilizzato dall'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| PipelineStepType | Tipo di [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) utilizzato nell'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| PublishedPipelineId | ID della pipeline pubblicata utilizzata nell'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| RunType | Tipo di esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |

I valori validi per la dimensione RunType sono i seguenti:

| Valore | Descrizione |
| ----- | ----- |
| Esperimento | Esecuzioni non pipeline. |
| PipelineRun | Esecuzione di una pipeline, che è l'elemento padre di un StepRun. |
| StepRun | Esecuzione di un passaggio della pipeline. |
| ReusedStepRun | Esecuzione di un passaggio della pipeline che riutilizza un'esecuzione precedente. |

## <a name="activity-log"></a>Log attività

Nella tabella seguente sono elencate le operazioni correlate a Azure Machine Learning che possono essere create nel log attività.

| Operazione | Descrizione |
|:---|:---|
| Crea o aggiorna un'area di lavoro Machine Learning | Un'area di lavoro è stata creata o aggiornata |
| CheckComputeNameAvailability | Controllare se un nome di calcolo è già in uso |
| Crea o aggiorna le risorse di calcolo | Una risorsa di calcolo è stata creata o aggiornata |
| Elimina le risorse di calcolo | Una risorsa di calcolo è stata eliminata |
| List secrets | Sui segreti dell'operazione elencati per un'area di lavoro Machine Learning |

## <a name="resource-logs"></a>Log risorse

Questa sezione elenca i tipi di log delle risorse che è possibile raccogliere per Azure Machine Learning area di lavoro.

Provider di risorse e tipo: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Category | Nome visualizzato |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schemi

Gli schemi seguenti sono utilizzati da Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>Tabella AmlComputeJobEvents

| Proprietà | Descrizione |
|:--- |:---|
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| JobId | ID del processo inviato |
| ExperimentId | ID dell'esperimento |
| Experimentname | Nome dell'esperimento |
| CustomerSubscriptionId | SubscriptionId in cui l'esperimento e il processo sono stati inviati |
| WorkspaceName | Nome dell'area di lavoro di Machine Learning |
| ClusterName | Nome del cluster |
| ProvisioningState | Stato dell'invio del processo |
| ResourceGroupName | Nome del gruppo di risorse |
| JobName | Nome del processo |
| ClusterId | ID del cluster |
| EventType | Tipo di evento del processo. Ad esempio, JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Stato del processo (esecuzione). Ad esempio, in coda, in esecuzione, riuscita, non riuscita |
| ErrorDetails | Dettagli dell'errore del processo |
| CreationApiVersion | Versione dell'API usata per creare il processo |
| ClusterResourceGroupName | Nome del gruppo di risorse del cluster |
| TFWorkerCount | Numero di processi di lavoro TF |
| TFParameterServerCount | Conteggio del server dei parametri TF |
| ToolType | Tipo di strumento utilizzato |
| RunInContainer | Flag che descrive se il processo deve essere eseguito all'interno di un contenitore |
| JobErrorMessage | messaggio dettagliato di errore del processo |
| NodeId | ID del nodo creato in cui è in esecuzione il processo |

### <a name="amlcomputeclusterevents-table"></a>Tabella AmlComputeClusterEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ProvisioningState | Stato del provisioning del cluster |
| ClusterName | Nome del cluster |
| ClusterType | Tipo di cluster |
| CreatedBy | Utente che ha creato il cluster |
| CoreCount | Conteggio dei core nel cluster |
| VmSize | Dimensioni della macchina virtuale del cluster |
| VmPriority | Priorità dei nodi creati all'interno di un cluster dedicato/LowPriority |
| ScalingType | Tipo di ridimensionamento manuale/automatico del cluster |
| InitialNodeCount | Numero iniziale di nodi del cluster |
| MinimumNodeCount | Numero minimo di nodi del cluster |
| MaximumNodeCount | Numero massimo di nodi del cluster |
| NodeDeallocationOption | Come deallocare il nodo |
| Publisher | Server di pubblicazione del tipo di cluster |
| Offerta | Offerta con cui viene creato il cluster |
| Sku | SKU del nodo/VM creato nel cluster |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| SubnetId | SubnetId del cluster |
| AllocationState | Stato di allocazione cluster |
| CurrentNodeCount | Numero corrente di nodi del cluster |
| TargetNodeCount | Numero di nodi di destinazione del cluster durante la scalabilità verso l'alto o verso il basso |
| EventType | Tipo di evento durante la creazione del cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo di inattività in secondi prima che il cluster venga ridimensionato |
| PreemptedNodeCount | Numero di nodi di cui è stato interrotto il cluster |
| IsResizeGrow | Flag che indica la scalabilità verticale del cluster |
| VmFamilyName | Nome della famiglia di macchine virtuali dei nodi che possono essere creati nel cluster |
| LeavingNodeCount | Uscita dal numero di nodi del cluster |
| UnusableNodeCount | Numero di nodi non utilizzabili del cluster |
| IdleNodeCount | Numero di nodi inattivi del cluster |
| RunningNodeCount | Conteggio dei nodi in esecuzione del cluster |
| PreparingNodeCount | Preparazione del conteggio dei nodi del cluster |
| QuotaAllocated | Quota allocata al cluster |
| QuotaUtilized | Quota utilizzata del cluster |
| AllocationStateTransitionTime | Tempo di transizione da uno stato a un altro |
| ClusterErrorCodes | Codice di errore ricevuto durante la creazione o il ridimensionamento del cluster |
| CreationApiVersion | Versione API usata durante la creazione del cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabella AmlComputeClusterNodeEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ClusterName | Nome del cluster |
| NodeId | ID del nodo del cluster creato |
| VmSize | Dimensioni della macchina virtuale del nodo |
| VmFamilyName | Famiglia di macchine virtuali a cui appartiene il nodo |
| VmPriority | Priorità del nodo creato/LowPriority dedicato |
| Publisher | Autore dell'immagine della macchina virtuale. Ad esempio, Microsoft-dsvm |
| Offerta | Offerta associata alla creazione della macchina virtuale |
| Sku | SKU del nodo o della macchina virtuale creata |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| ClusterCreationTime | Ora di creazione del cluster |
| ResizeStartTime | Ora di inizio/discesa della scalabilità del cluster |
| ResizeEndTime | Ora di fine della scalabilità verticale del cluster |
| NodeAllocationTime | Ora di allocazione del nodo |
| NodeBootTime | Ora di avvio del nodo |
| StartTaskStartTime | Ora in cui l'attività è stata assegnata a un nodo e avviata |
| StartTaskEndTime | Ora di fine dell'attività assegnata a un nodo |
| TotalE2ETimeInSeconds | Tempo totale nodo attivo |


## <a name="see-also"></a>Vedi anche

- Per una descrizione del monitoraggio Azure Machine Learning, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/essentials/monitor-azure-resource.md).
