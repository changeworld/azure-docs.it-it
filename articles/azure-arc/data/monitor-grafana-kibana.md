---
title: Visualizzare i log e le metriche con Kibana e Grafana
description: Visualizzare i log e le metriche con Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670002"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Visualizzare i log e le metriche con Kibana e Grafana

I dashboard Web Kibana e Grafana offrono informazioni e chiarezza sugli spazi dei nomi Kubernetes usati dai servizi dati abilitati per Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorare le istanze gestite di SQL Azure in Azure Arc

Per accedere ai log e ai dashboard di monitoraggio per la Istanza gestita SQL abilitata per Arc, eseguire il comando seguente dell' `azdata` interfaccia della riga di comando

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
I dashboard di Grafana pertinenti sono:

* "Metriche dell'istanza gestita di SQL di Azure"
* "Metriche del nodo host"
* "Metriche di Pod host"


> [!NOTE]
>  Quando viene richiesto di immettere un nome utente e una password, immettere il nome utente e la password specificati al momento della creazione del controller di dati di Azure Arc.

> [!NOTE]
>  Verrà visualizzato un avviso di certificato perché i certificati usati in anteprima sono certificati autofirmati.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Monitorare l'iperscalabilità di database di Azure per PostgreSQL in Azure Arc

Per accedere ai log e ai dashboard di monitoraggio per l'iperscalabilità di PostgreSQL, eseguire il `azdata` comando CLI seguente

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

I dashboard di postgreSQL pertinenti sono:

* "Metriche postgres"
* "Metriche tabella postgres"
* "Metriche del nodo host"
* "Metriche di Pod host"


## <a name="additional-firewall-configuration"></a>Configurazione aggiuntiva del firewall

A seconda della posizione in cui viene distribuito il controller dati, potrebbe essere necessario aprire le porte nel firewall per accedere agli endpoint Kibana e Grafana.

Di seguito è riportato un esempio di come eseguire questa operazione per una macchina virtuale di Azure. È necessario eseguire questa operazione se Kubernetes è stato distribuito con lo script.

I passaggi seguenti evidenziano come creare una regola NSG per gli endpoint Kibana e Grafana:

### <a name="find-the-name-of-the-nsg"></a>Trovare il nome del gruppo di sicurezza di rete

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Aggiungere la regola NSG

Una volta ottenuto il nome del NSG, è possibile aggiungere una regola usando il comando seguente:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Passaggi successivi
- Provare [a caricare metriche e log in monitoraggio di Azure](upload-metrics-and-logs-to-azure-monitor.md)
- Leggere le informazioni su Grafana:
   - [Attività iniziali](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Nozioni fondamentali su Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Esercitazioni su Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Scopri di più su Kibana
   - [Introduzione](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guida a Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introduzione ai dashboard DrillDown con visualizzazioni dei dati in Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Come compilare dashboard Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

