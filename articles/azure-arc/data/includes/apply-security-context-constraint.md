---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696066"
---
In questa sezione viene illustrato come applicare un vincolo del contesto di sicurezza (SCC). Per la versione di anteprima, questi vincoli di sicurezza sono attenuati. 

1. Scaricare il vincolo del contesto di sicurezza personalizzato (SCC). Usare uno dei seguenti: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - (Non[elaborato](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Il comando seguente Scarica Arc-data-SCC. YAML:

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