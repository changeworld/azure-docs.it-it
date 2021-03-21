---
title: Aggiungere tag ai dispositivi gemelli digitali
titleSuffix: Azure Digital Twins
description: Scopri come implementare i tag sui dispositivi gemelli digitali
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 70bf46de072a97eca810dda60a5331df14172ed6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555158"
---
# <a name="add-tags-to-digital-twins"></a>Aggiungere tag ai dispositivi gemelli digitali 

È possibile usare il concetto di tag per identificare e classificare ulteriormente i dispositivi gemelli digitali. In particolare, è possibile che gli utenti desiderino replicare i tag dei sistemi esistenti, ad esempio i [tag di paglia](https://project-haystack.org/doc/TagModel), all'interno delle istanze di Azure Digital gemelli. 

Questo documento descrive i modelli che possono essere usati per implementare tag sui dispositivi gemelli digitali.

I tag vengono innanzitutto aggiunti come proprietà all'interno del [modello](concepts-models.md) che descrive un dispositivo gemello digitale. Tale proprietà viene quindi impostata sul dispositivo gemello quando viene creato in base al modello. Successivamente, i tag possono essere usati nelle [query](concepts-query-language.md) per identificare e filtrare i gemelli.

## <a name="marker-tags"></a>Tag marcatore 

Un **tag del marcatore** è una stringa semplice utilizzata per contrassegnare o categorizzare un gemello digitale, ad esempio "blu" o "rosso". Questa stringa è il nome del tag e i tag del marcatore non hanno un valore significativo, il tag è significativo solo in base alla sua presenza (o assenza). 

### <a name="add-marker-tags-to-model"></a>Aggiungi tag marcatore al modello 

I tag del marcatore sono modellati come mappa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) da `string` a `boolean` . Il valore booleano `mapValue` viene ignorato, in quanto la presenza del tag è importante. 

Di seguito è riportato un Estratto di un modello di dispositivo gemello che implementa un tag del marcatore come proprietà:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Aggiungere i tag del marcatore ai dispositivi gemelli digitali

Quando la `tags` proprietà fa parte del modello di un gemello digitale, è possibile impostare il tag del marcatore nel dispositivo gemello digitale impostando il valore di questa proprietà. 

Di seguito è riportato un esempio che popola il marcatore `tags` per tre dispositivi gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Query con tag marcatore

Una volta aggiunti i tag ai dispositivi gemelli digitali, è possibile usare i tag per filtrare i gemelli nelle query. 

Ecco una query per ottenere tutti i dispositivi gemelli contrassegnati come "rossi": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

È anche possibile combinare i tag per query più complesse. Ecco una query per ottenere tutti i gemelli che sono rotondi e non rossi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Tag Value 

Un **tag Value** è una coppia chiave-valore usata per assegnare un valore a ogni tag, ad esempio `"color": "blue"` o `"color": "red"` . Una volta creato, un tag di valore può essere usato anche come tag del marcatore ignorando il valore del tag. 

### <a name="add-value-tags-to-model"></a>Aggiungere tag di valore al modello 

I tag di valore vengono modellati come mappa [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) da `string` a `string` . Sia `mapKey` che `mapValue` sono significativi. 

Di seguito è riportato un Estratto di un modello di dispositivo gemello che implementa un tag di valore come proprietà:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Aggiungere tag valore ai dispositivi gemelli digitali

Come per i tag del marcatore, è possibile impostare il tag value in un dispositivo gemello digitale impostando il valore di questa `tags` Proprietà dal modello. Per usare un tag di valore come tag del marcatore, è possibile impostare il `tagValue` campo sul valore stringa vuoto ( `""` ). 

Di seguito è riportato un esempio che popola il valore `tags` per tre dispositivi gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Si noti che `red` e `purple` vengono usati come tag del marcatore in questo esempio.

### <a name="query-with-value-tags"></a>Query con tag di valore

Come per i tag del marcatore, è possibile usare i tag di valore per filtrare i gemelli nelle query. È anche possibile usare i tag di valore e i tag del marcatore.

Nell'esempio precedente `red` viene usato come tag del marcatore. Tenere presente che si tratta di una query per ottenere tutti i dispositivi gemelli contrassegnati come "rossi": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Ecco una query per ottenere tutte le entità di dimensioni ridotte (tag di valore) e non rosse: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla progettazione e la gestione di modelli di dispositivi gemelli digitali:
* [*Procedura: gestire i modelli DTDL*](how-to-manage-model.md)

Altre informazioni sull'esecuzione di query sul grafico gemello:
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)