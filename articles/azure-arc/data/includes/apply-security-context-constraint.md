---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494014"
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

   > [!NOTE]
   > RedHat OpenShift 4,5 o versione successiva, modifica la modalità di applicazione di SCC all'account del servizio.
   > Usare lo stesso spazio dei nomi qui e nel `azdata arc dc create` comando riportato di seguito. L'esempio è `arc` . 
   > 
   > Se si usa RedHat OpenShift 4,5 o versione successiva, eseguire: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
