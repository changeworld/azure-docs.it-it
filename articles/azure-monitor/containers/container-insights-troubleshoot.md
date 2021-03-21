---
title: Come risolvere i problemi relativi a informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come è possibile risolvere i problemi con le informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 60a6e76d43d954b27336b9631c48328aeff0b69b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708306"
---
# <a name="troubleshooting-container-insights"></a>Risoluzione dei problemi relativi a informazioni dettagliate sul contenitore

Quando si configura il monitoraggio del cluster di Azure Kubernetes Service (AKS) con informazioni dettagliate sul contenitore, è possibile che si verifichi un problema che impedisce lo stato della raccolta dati o della creazione di report. Questo articolo illustra alcuni problemi comuni e i passaggi per la risoluzione dei problemi.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Errore di autorizzazione durante l'operazione di onboarding o aggiornamento

Quando si Abilita il contenitore Insights o si aggiorna un cluster per supportare la raccolta di metriche, è possibile che venga visualizzato un errore simile al seguente: *il client <l'identità dell'utente>' con ID oggetto ' <objectId>' non dispone dell'autorizzazione per eseguire l'azione ' Microsoft. Authorization/roleAssignments/Write ' sull'ambito*

Durante il processo di onboarding o di aggiornamento, viene effettuato un tentativo di concedere l'assegnazione del ruolo server di pubblicazione per la **metrica di monitoraggio** sulla risorsa cluster. L'utente che avvia il processo per abilitare il contenitore Insights o l'aggiornamento per supportare la raccolta di metriche deve avere accesso all'autorizzazione **Microsoft. Authorization/roleAssignments/Write** nell'ambito della risorsa del cluster AKS. Solo ai membri dei ruoli predefiniti **proprietario** e **amministratore accesso utenti** viene concesso l'accesso a questa autorizzazione. Se i criteri di sicurezza richiedono l'assegnazione di autorizzazioni a livello granulare, è consigliabile visualizzare i [ruoli personalizzati](../../role-based-access-control/custom-roles.md) e assegnarli agli utenti che lo richiedono.

È anche possibile concedere manualmente questo ruolo dal portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco di risorse digitare **Kubernetes**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Azure Kubernetes**.
3. Nell'elenco dei cluster Kubernetes selezionarne uno dall'elenco.
2. Nel menu a sinistra fare clic su **controllo di accesso (IAM)**.
3. Selezionare **+ Aggiungi** per aggiungere un'assegnazione di ruolo e selezionare il ruolo **server di pubblicazione metriche di monitoraggio** e nella casella di **selezione** digitare **AKS** per filtrare i risultati solo nelle entità servizio del cluster definite nella sottoscrizione. Selezionare quello nell'elenco specifico del cluster.
4. Selezionare **Salva** per completare l'assegnazione del ruolo.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Il contenitore Insights è abilitato ma non segnala informazioni

Se il contenitore Insights è stato abilitato e configurato correttamente, ma non è possibile visualizzare le informazioni sullo stato o nessun risultato viene restituito da una query di log, è possibile diagnosticare il problema attenendosi alla procedura seguente:

1. Controllare lo stato dell'agente eseguendo il comando:

    `kubectl get ds omsagent --namespace=kube-system`

    L'output dovrebbe essere simile all'esempio seguente, da cui risulta che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Se sono presenti nodi di Windows Server, controllare lo stato dell'agente eseguendo il comando:

    `kubectl get ds omsagent-win --namespace=kube-system`

    L'output dovrebbe essere simile all'esempio seguente, da cui risulta che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Verificare lo stato della distribuzione con la versione dell'agente *06072018* o successiva con il comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    L'output dovrebbe essere simile all'esempio seguente, da cui risulta che la distribuzione è stata eseguita correttamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Controllare lo stato del pod per verificare se è in esecuzione con il comando: `kubectl get pods --namespace=kube-system`

    L'output dovrebbe essere simile all'esempio seguente con uno stato *In esecuzione* per omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>messaggi di errore

La tabella seguente riepiloga gli errori noti che possono verificarsi durante l'uso di informazioni dettagliate sul contenitore.

| messaggi di errore  | Azione |
| ---- | --- |
| Messaggio di errore `No data for selected filters`  | L'individuazione del flusso di dati di monitoraggio per i nuovi cluster potrebbe richiedere alcuni minuti. Consentire la visualizzazione dei dati per il cluster per almeno 10-15 minuti. |
| Messaggio di errore `Error retrieving data` | Mentre il cluster di servizi Kubernetes di Azure è configurato per il monitoraggio dello stato e delle prestazioni, viene stabilita una connessione tra il cluster e l'area di lavoro di Azure Log Analytics. Un'area di lavoro Log Analytics viene usata per archiviare tutti i dati di monitoraggio per il cluster. Questo errore può verificarsi quando l'area di lavoro Log Analytics è stata eliminata. Controllare se l'area di lavoro è stata eliminata e, in caso contrario, sarà necessario riabilitare il monitoraggio del cluster con informazioni dettagliate sul contenitore e specificare un'area di lavoro esistente o crearne una nuova. Per abilitare nuovamente, è necessario [disabilitare](container-insights-optout.md) il monitoraggio per il cluster e [abilitare](container-insights-enable-new-cluster.md) nuovamente le informazioni dettagliate sul contenitore. |
| `Error retrieving data` dopo l'aggiunta di container Insights tramite AZ AKS CLI | Quando si Abilita il monitoraggio usando `az aks cli` , le informazioni dettagliate sul contenitore potrebbero non essere distribuite correttamente. Controllare se la soluzione è stata distribuita. Per verificare, passare all'area di lavoro di Log Analytics e verificare se la soluzione è disponibile selezionando **soluzioni** dal riquadro sul lato sinistro. Per risolvere questo problema, è necessario ridistribuire la soluzione seguendo le istruzioni su [come distribuire informazioni dettagliate sul contenitore](container-insights-onboard.md) |

Per facilitare la diagnosi del problema, è stato fornito uno [script per la risoluzione dei problemi](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>I pod ReplicaSet dell'agente di container Insights non sono pianificati in un cluster Kubernetes non Azure

I pod ReplicaSet dell'agente di container Insights hanno una dipendenza dai selettori di nodo seguenti nei nodi di lavoro (o agente) per la pianificazione:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se ai nodi di lavoro non sono associate etichette dei nodi, i pod ReplicaSet dell'agente non vengono pianificati. Per istruzioni su come associare l'etichetta, vedere [Kubernetes Assign label Selectors](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>I grafici delle prestazioni non mostrano la CPU o la memoria dei nodi e dei contenitori in un cluster non di Azure

Container Insights Agent Pod usa l'endpoint cAdvisor nell'agente del nodo per raccogliere le metriche delle prestazioni. Verificare che l'agente in contenitori nel nodo sia configurato in modo da consentire l' `cAdvisor port: 10255` apertura in tutti i nodi del cluster per raccogliere le metriche delle prestazioni.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Il cluster Kubernetes non Azure non viene visualizzato in informazioni dettagliate sul contenitore

Per visualizzare il cluster non Azure Kubernetes in informazioni dettagliate sul contenitore, è necessario l'accesso in lettura nell'area di lavoro Log Analytics che supporta questa informazione e nella risorsa della soluzione contenitore Insights **ContainerInsights (*area di lavoro*)**.

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per acquisire le metriche di integrità sia per i nodi del cluster servizio Azure Kubernetes che per i pod, queste metriche di integrità sono disponibili nel portale di Azure. Per informazioni su come usare il contenitore Insights, vedere [visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).
