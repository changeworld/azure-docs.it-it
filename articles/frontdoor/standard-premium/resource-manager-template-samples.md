---
title: Esempi di modelli di Gestione risorse-sportello anteriore di Azure
description: Informazioni sui modelli di Azure Resource Manager di esempio forniti per il front-end di Azure.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561747"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Modelli di Azure Resource Manager per lo sportello anteriore di Azure

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

La tabella seguente include i collegamenti ai modelli di Azure Resource Manager per lo sportello anteriore di Azure, con architetture di riferimento che includono altri servizi di Azure.

| Esempio | Descrizione |
|-|-|
| [Set di regole](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Crea un profilo di sportello anteriore e un set di regole.  |
|**Origini servizio app**| **Descrizione** |
| [Servizio app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Consente di creare un'app del servizio app con un endpoint pubblico e un profilo di porta anteriore.  |
| [Servizio app con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Consente di creare un'app del servizio app con un endpoint privato e un profilo di porta anteriore.  |
| [Ambiente del servizio app con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Consente di creare un ambiente del servizio app, un'app con un endpoint privato e un profilo di porta anteriore.  |
|**Origini di funzioni di Azure**| **Descrizione** |
| [Funzioni di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Consente di creare un'app funzioni di Azure con un endpoint pubblico e un profilo di porta anteriore.  |
| [Funzioni di Azure con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Consente di creare un'app funzioni di Azure con un endpoint privato e un profilo di porta anteriore.  |
|**Origini di gestione API**| **Descrizione** |
| [Gestione API (esterna)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Crea un'istanza di gestione API con integrazione VNet esterna e un profilo di porta anteriore.  |
|**Origini archiviazione**| **Descrizione** |
| [Sito Web statico di archiviazione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Crea un account di archiviazione di Azure e un sito Web statico con un endpoint pubblico e un profilo di sportello anteriore.  |
| [BLOB di archiviazione con collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Crea un account di archiviazione di Azure e un contenitore BLOB con un endpoint privato e un profilo di sportello anteriore.  |
|**Origini del gateway applicazione**| **Descrizione** |
| [Gateway applicazione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Consente di creare un gateway applicazione e un profilo di porta anteriore. |
|**Origini macchina virtuale**| **Descrizione** |
| [Macchina virtuale con servizio di collegamento privato](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Crea una macchina virtuale e un servizio di collegamento privato e un profilo di porta anteriore. |
| | |
