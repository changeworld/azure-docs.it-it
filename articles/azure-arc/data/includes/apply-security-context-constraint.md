---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687592"
---
In questa sezione viene illustrato come applicare un vincolo del contesto di sicurezza (SCC). Per la versione di anteprima, questi vincoli di sicurezza sono attenuati. 

1. Scaricare il vincolo del contesto di sicurezza personalizzato (SCC). Usare uno dei seguenti: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Il comando seguente Scarica Arc-data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Crea SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Applicare l'oggetto SCC all'account del servizio.

   > [!NOTE]
   > Usare lo stesso spazio dei nomi qui e nel `azdata arc dc create` comando riportato di seguito. L'esempio è `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
