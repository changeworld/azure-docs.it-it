---
title: Metriche di distribuzione & HPA con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive le metriche relative alla distribuzione & la funzionalità di scalabilità automatica di Pod orizzontali che vengono raccolte con informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717707"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Metriche di distribuzione & HPA con informazioni dettagliate sul contenitore

A partire dalla versione dell'agente *ciprod08072020*, container Insights-Agent integrato ora raccoglie le metriche per le distribuzioni & attributi.

## <a name="deployment-metrics"></a>Metriche di distribuzione

Il contenitore Insights avvia automaticamente le distribuzioni di monitoraggio, raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviarle nella tabella **InsightMetrics** :

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Numero totale di Pod pronti assegnati a questa distribuzione (status. readyReplicas). Di seguito sono riportate le dimensioni di questa metrica. <ul> <li> distribuzione: nome della distribuzione </li> <li> k8sNamespace-spazio dei nomi Kubernetes per la distribuzione </li> <li> deploymentStrategy-strategia di distribuzione da usare per sostituire i pod con quelli nuovi (spec. Strategy. Type)</li><li> creationTime-timestamp di creazione della distribuzione </li> <li> spec_replicas: numero di Pod desiderati (spec. repliche) </li> <li>status_replicas_available: numero totale di pod disponibili (pronti per almeno minReadySeconds) di destinazione di questa distribuzione (status. availableReplicas)</li><li>status_replicas_updated: numero totale di Pod non terminati assegnati da questa distribuzione con la specifica del modello desiderata (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>Metriche HPA

Il contenitore Insights avvia automaticamente il monitoraggio attributi, raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviando tali metriche nella tabella **InsightMetrics** :

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, TargetName | Numero corrente di repliche di Pod gestiti da questo ridimensionatore automatico (status. currentReplicas). Di seguito sono riportate le dimensioni di questa metrica. <ul> <li> hPa: nome del HPA </li> <li> k8sNamespace-spazio dei nomi Kubernetes per HPA </li> <li> lastScaleTime-ultima volta in cui l'HPA ha ridimensionato il numero di Pod (status. lastScaleTime)</li><li> timestamp di creazione creationTime-HPA </li> <li> spec_max_replicas-limite massimo per il numero di pod che possono essere impostati dal ridimensionamento automatico (spec. maxReplicas) </li> <li> spec_min_replicas-limite inferiore per il numero di repliche a cui è possibile ridurre il ridimensionamento automatico (spec. minReplicas) </li><li>status_desired_replicas il numero di repliche di Pod gestito da questo ridimensionatore automatico (status. desiredReplicas)</li><li>targetKind: tipo di destinazione di HPA (spec. scaleTargetRef. Kind) </li><li>TargetName: nome della destinazione di HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Grafici di distribuzione & HPA 

Il contenitore Insights include grafici preconfigurati per le metriche elencate in precedenza nella tabella come cartella di lavoro per ogni cluster. È possibile trovare le distribuzioni & le distribuzioni di cartelle di lavoro HPA **& hPa** direttamente da un cluster AKS selezionando **cartelle di lavoro** nel riquadro a sinistra e nell'elenco a discesa **Visualizza cartelle di lavoro** in informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le [metriche di stato Kube in Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) per altre informazioni sulle metriche di stato Kube.