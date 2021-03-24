---
author: baanders
description: file di inclusione per caricare un modello in un'istanza di Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950596"
---
Il modello ha un aspetto simile al seguente:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Per **caricare il modello nell'istanza dei dispositivi gemelli**, eseguire il comando dell'interfaccia della riga di comando di Azure seguente, che consente di caricare il modello precedente come JSON inline. È possibile eseguire il comando in [Azure cloud Shell](../articles/cloud-shell/overview.md) nel browser o nel computer se l'interfaccia della riga di comando è [installata localmente](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Se si usa Cloud Shell nell'ambiente PowerShell, potrebbe essere necessario eseguire l'escape dei caratteri di virgoletta nei campi JSON inline affinché i relativi valori vengano analizzati correttamente. Ecco il comando per caricare il modello con questa modifica:
>
> Modello di caricamento:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```