---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508096"
---
Le voci del documento `deploymentconfig.json` vengono associate ai parametri di [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro per il metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per le destinazioni locali, il valore deve essere `local`. |
| `port` | `port` | La porta locale su cui esporre l'endpoint HTTP del servizio. |

Il codice JSON seguente è un esempio di configurazione della distribuzione da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "local",
    "port": 32267
}
```