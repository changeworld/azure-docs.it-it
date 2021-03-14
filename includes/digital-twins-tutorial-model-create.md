---
author: baanders
description: file di inclusione per i dispositivi gemelli digitali di Azure-istruzioni modello per la riga di comando esercitazione
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463786"
---
1. **Aggiornare il numero di versione** per indicare che si specifica una versione più aggiornata di questo modello. A tale scopo, sostituire *1* alla fine del valore `@id` con *2*. È anche possibile inserire qualsiasi numero maggiore del numero di versione corrente.
1. **Modificare una proprietà**. Cambiare il nome della proprietà `Humidity` in *HumidityLevel* (o qualcosa di diverso se si preferisce). Se si usa un valore diverso da *HumidityLevel*, prendere nota del valore immesso e continuare a usarlo al posto di *HumidityLevel* nell'intera esercitazione.
1. **Aggiungere una proprietà**. Sotto la proprietà `HumidityLevel` che termina alla riga 15 incollare il codice seguente per aggiungere una proprietà `RoomName` alla stanza:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Aggiungere una relazione**. Sotto la proprietà `RoomName` appena aggiunta incollare il codice seguente per aggiungere la possibilità che questo tipo di dispositivo gemello formi relazioni *contains* con altri gemelli:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Al termine, il modello aggiornato sarà simile al seguente:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Assicurarsi di salvare il file prima di procedere.