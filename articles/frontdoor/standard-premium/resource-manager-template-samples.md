---
title: Resource Manager modelli di esempio - Frontdoor di Azure
description: Informazioni sui modelli Azure Resource Manager di esempio forniti per Frontdoor di Azure.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: e939aec8f1cf5fa615904bb81bad82ea3cf22c61
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587477"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager modelli per Frontdoor di Azure

> [!Note]
> Questa documentazione è relativa Frontdoor di Azure Standard/Premium (anteprima). Per informazioni sulle Frontdoor di Azure? Visualizzare [qui](../front-door-overview.md).

La tabella seguente include collegamenti a modelli Azure Resource Manager per Frontdoor di Azure, con architetture di riferimento, inclusi altri servizi di Azure.

| Esempio | Descrizione |
|-|-|
| [Front door (creazione rapida)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Crea un profilo Front door di base che include un endpoint, un gruppo di origine, un'origine e una route.  |
| [Set di regole](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Crea un profilo Front door e un set di regole.  |
| [Criteri WAF con set di regole gestite](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | Crea un profilo Front door e waf con set di regole gestite.  |
| [Criteri WAF con regola personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Crea un profilo Front door e waf con una regola personalizzata.  |
|**Origini del servizio app**| **Descrizione** |
| [Servizio app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Crea un'app del servizio app con un endpoint pubblico e un profilo Front door.  |
| [Servizio app con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Crea un'app del servizio app con un endpoint privato e un profilo Front door.  |
| [Ambiente del servizio app con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Crea un ambiente del servizio app, un'app con un endpoint privato e un profilo Front door.  |
|**Funzioni di Azure origini**| **Descrizione** |
| [Funzioni di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Crea un Funzioni di Azure app con un endpoint pubblico e un profilo Front door.  |
| [Funzioni di Azure con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Crea un Funzioni di Azure app con un endpoint privato e un profilo Front door.  |
|**API Management origini**| **Descrizione** |
| [API Management (esterno)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Crea un'API Management con integrazione della rete virtuale esterna e un profilo Front door.  |
|**Origini di archiviazione**| **Descrizione** |
| [Sito Web statico di archiviazione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Crea un account Archiviazione di Azure sito Web statico con un endpoint pubblico e un profilo Front door.  |
| [BLOB di archiviazione con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Crea un Archiviazione di Azure e un contenitore BLOB con un endpoint privato e un profilo Front door.  |
|**Origini del gateway applicazione**| **Descrizione** |
| [Gateway applicazione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Crea un gateway applicazione e un profilo front-door. |
|**Origini delle macchine virtuali**| **Descrizione** |
| [Macchina virtuale con servizio Collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Crea una macchina virtuale e un servizio Collegamento privato e un profilo front-door. |
| | |
