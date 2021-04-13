---
author: baanders
description: file di inclusione per caricare un modello in un'istanza di Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304299"
---
Il modello ha un aspetto simile al seguente:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Per **caricare il modello nell'istanza dei dispositivi gemelli**, eseguire il comando dell'interfaccia della riga di comando di Azure seguente, che consente di caricare il modello precedente come JSON inline. È possibile eseguire il comando in [Azure cloud Shell](../articles/cloud-shell/overview.md) nel browser (usare l'ambiente **bash** ) o nel computer se l'interfaccia della riga di comando è [installata localmente](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```