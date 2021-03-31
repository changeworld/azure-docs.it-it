---
title: Conversione di ontologie
titleSuffix: Azure Digital Twins
description: Informazioni sul processo di conversione dei modelli standard del settore in DTDL per i dispositivi gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100561755"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Converti le ontologie standard del settore in DTDL per i dispositivi gemelli digitali di Azure

La maggior parte delle [ontologie](concepts-ontologies.md) si basa su standard web semantici come [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [FTR](https://www.w3.org/2001/sw/wiki/RDFS). 

Per usare un modello con i dispositivi gemelli digitali di Azure, deve essere in formato DTDL. Questo articolo descrive le indicazioni generali per la progettazione sotto forma di **modello di conversione** per la conversione di modelli basati su RDF in DTDL in modo che possano essere usati con i dispositivi gemelli digitali di Azure. 

L'articolo contiene anche [**codice del convertitore di esempio**](#converter-samples) per i convertitori RDF e Owl, che possono essere estesi per altri schemi nel settore della compilazione.

## <a name="conversion-pattern"></a>Modello di conversione

Sono disponibili diverse librerie di terze parti che possono essere usate per la conversione di modelli basati su RDF in DTDL. Alcune di queste librerie consentono di caricare il file del modello in un grafico. È possibile scorrere in ciclo il grafo cercando specifici costrutti di FTR e OWL e convertirli in DTDL.   

La tabella seguente è un esempio di come è possibile eseguire il mapping dei costrutti di FTR e OWL a DTDL. 

| Concetto di FTR/gufo | Costrutto FTR/OWL | Concetto di DTDL | Costrutto DTDL |
| --- | --- | --- | --- |
| Classi | `owl:Class`<br>Suffisso IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sottoclassi | `owl:Class`<br>Suffisso IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Proprietà DataType | `owl:DatatypeProperty`<br>`rdfs:label` o `INode`<br>`rdfs:label`<br>`rdfs:range` | Proprietà dell'interfaccia | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Proprietà degli oggetti | `owl:ObjectProperty`<br>`rdfs:label` o `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (o omesso se no `rdfs:range` )<br>`comment`<br>`displayName`<br>

Il frammento di codice C# seguente illustra come caricare un file di modello RDF in un grafico e convertirlo in DTDL usando la libreria [**dotNetRDF**](https://www.dotnetrdf.org/) . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Esempi di convertitore

### <a name="rdf-converter-application"></a>Applicazione convertitore RDF 

È disponibile un'applicazione di esempio che converte un file di modello basato su RDF in [DTDL (versione 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per l'uso da parte del servizio Azure Digital Twins. È stata convalidata per lo schema [Brick](https://brickschema.org/ontology/) ed è possibile estenderla per altri schemi nel settore della compilazione (ad esempio, la [compilazione della topologia di ontologia (bot)](https://w3c-lbd-cg.github.io/bot/), della rete dei [sensori semantici](https://www.w3.org/TR/vocab-ssn/)o di [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

L'esempio è un'applicazione della riga di comando di .NET Core denominata **RdfToDtdlConverter**.

È possibile ottenere l'esempio seguente: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Per scaricare il codice nel computer, fare clic sul pulsante *Scarica zip* sotto il titolo nella pagina di destinazione dell'esempio. Verrà scaricato un file *zip* con il nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, che sarà quindi possibile decomprimere ed esplorare.

È possibile usare questo esempio per visualizzare i modelli di conversione nel contesto e per avere come blocco predefinito per le applicazioni che eseguono conversioni di modelli in base alle proprie esigenze specifiche.

### <a name="owl2dtdl-converter"></a>Convertitore OWL2DTDL 

Il [**convertitore OWL2DTDL**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) è un esempio che converte una ontologia Owl in un set di dichiarazioni di interfaccia DTDL, che possono essere usate con il servizio Azure Digital gemelli. Funziona anche per le reti di ontologia, costituite da una ontologia radice che riutilizza altre ontologie attraverso le `owl:imports` dichiarazioni.

Questo convertitore è stato usato per tradurre l' [ontologia principale di Real estate](https://doc.realestatecore.io/3.1/full.html) in DTDL e può essere usato per qualsiasi ontologia basata su Owl.

## <a name="next-steps"></a>Passaggi successivi 

* Altre informazioni sull'estensione delle ontologie standard del settore per soddisfare le specifiche: [*concetti: estensione delle ontologie del settore*](concepts-ontologies-extend.md).

* In alternativa, continuare con il percorso per lo sviluppo di modelli basati su ontologie: [*uso delle strategie di ontologia in un percorso di sviluppo del modello*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).