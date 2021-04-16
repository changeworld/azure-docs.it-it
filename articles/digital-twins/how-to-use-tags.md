---
title: Aggiungere tag ai gemelli digitali
titleSuffix: Azure Digital Twins
description: Informazioni su come implementare i tag nei gemelli digitali
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376716"
---
# <a name="add-tags-to-digital-twins"></a>Aggiungere tag ai gemelli digitali 

È possibile usare il concetto di tag per identificare e classificare ulteriormente i gemelli digitali. In particolare, gli utenti possono voler replicare i tag dai sistemi esistenti, ad esempio [i tag Diostack,](https://project-haystack.org/doc/TagModel)all'interno delle Gemelli digitali di Azure esistenti. 

Questo documento descrive i modelli che possono essere usati per implementare tag nei gemelli digitali.

I tag vengono prima aggiunti come proprietà all'interno [del modello](concepts-models.md) che descrive un gemello digitale. Tale proprietà viene quindi impostata sul dispositivo gemello quando viene creata in base al modello. Successivamente, i tag possono essere usati nelle [query per](concepts-query-language.md) identificare e filtrare i gemelli.

## <a name="marker-tags"></a>Tag marcatore 

Un **tag marcatore** è una stringa semplice usata per contrassegnare o classificare un gemello digitale, ad esempio "blu" o "rosso". Questa stringa è il nome del tag e i tag marcatore non hanno alcun valore significativo. Il tag è significativo solo per la presenza (o l'assenza). 

### <a name="add-marker-tags-to-model"></a>Aggiungere tag marcatore al modello 

I tag marcatore vengono modellati come mapping [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) da `string` a `boolean` . Il valore booleano viene ignorato, perché la presenza `mapValue` del tag è tutto ciò che è importante. 

Ecco un estratto di un modello gemello che implementa un tag marcatore come proprietà:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Aggiungere tag marcatore ai gemelli digitali

Quando la proprietà fa parte del modello di un gemello digitale, è possibile impostare il tag marcatore nel dispositivo gemello digitale impostando `tags` il valore di questa proprietà. 

Ecco un esempio che popola il marcatore `tags` per tre gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Ecco un esempio di codice su come impostare il marcatore `tags` per un dispositivo gemello usando [.NET SDK:](/dotnet/api/overview/azure/digitaltwins/client)

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Eseguire query con tag marcatore

Dopo aver aggiunto i tag ai gemelli digitali, è possibile usare i tag per filtrare i gemelli nelle query. 

Ecco una query per ottenere tutti i gemelli contrassegnati come "rosso": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

È anche possibile combinare tag per query più complesse. Ecco una query per ottenere tutti i gemelli arrotondati e non rossi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Tag valore 

Un **tag value** è una coppia chiave-valore usata per assegnare un valore a ogni tag, ad esempio o `"color": "blue"` `"color": "red"` . Una volta creato, un tag di valore può essere usato anche come tag marcatore ignorando il valore del tag. 

### <a name="add-value-tags-to-model"></a>Aggiungere tag di valore al modello 

I tag value vengono modellati come mapping [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) da `string` a `string` . Sia che `mapKey` sono `mapValue` significativi. 

Ecco un estratto di un modello gemello che implementa un tag di valore come proprietà:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Aggiungere tag di valore ai gemelli digitali

Come per i tag marcatore, è possibile impostare il tag value in un gemello digitale impostando il valore di questa `tags` proprietà dal modello. Per usare un tag di valore come tag marcatore, è possibile impostare il `tagValue` campo sul valore di stringa vuota ( `""` ). 

Di seguito è riportato un esempio che popola il valore `tags` per tre gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Si noti `red` che e vengono usati come tag `purple` marcatore in questo esempio.

### <a name="query-with-value-tags"></a>Eseguire query con tag di valore

Come per i tag marcatore, è possibile usare tag valore per filtrare i gemelli nelle query. È anche possibile usare tag valore e tag marcatore insieme.

Nell'esempio precedente viene `red` usato come tag del marcatore. Tenere presente che si tratta di una query per ottenere tutti i gemelli contrassegnati come "red": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Ecco una query per ottenere tutte le entità piccole (tag value) e non rosse: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla progettazione e la gestione di modelli gemelli digitali:
* [*Procedura: Gestire modelli DTDL*](how-to-manage-model.md)

Altre informazioni sull'esecuzione di query sul grafo gemello:
* [*Procedura: Eseguire query sul grafo gemello*](how-to-query-graph.md)
