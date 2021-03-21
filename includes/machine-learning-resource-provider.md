---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75893981"
---
Quando si crea un'area di lavoro di Azure Machine Learning o una risorsa usata dall'area di lavoro, è possibile che venga visualizzato un errore simile ai messaggi seguenti:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Molti provider di risorse vengono registrati automaticamente, ma non tutti. Se si riceve questo messaggio, è necessario registrare il provider indicato.

Per informazioni sulla registrazione dei provider di risorse, vedere [Risoluzione degli errori di registrazione del provider di risorse](../articles/azure-resource-manager/templates/error-register-resource-provider.md).