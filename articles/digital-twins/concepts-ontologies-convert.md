---
title: Conversione di ontologi
titleSuffix: Azure Digital Twins
description: Comprendere il processo di conversione di modelli standard del settore in DTDL per Gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484481"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Convertire le ontologie standard del settore in DTDL per Gemelli digitali di Azure

La [maggior parte degli ontologi](concepts-ontologies.md) si basa su standard Web semantici come [OWL,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

Per usare un modello con Gemelli digitali di Azure, deve essere in formato DTDL. Questo articolo descrive linee guida generali sulla progettazione sotto forma di modello di **conversione** per la conversione di modelli basati su RDF in DTDL in modo che possano essere usati con Gemelli digitali di Azure. 

L'articolo contiene anche [**codice di convertitore**](#converter-samples) di esempio per convertitori RDF e OWL, che possono essere estesi per altri schemi nel settore dell'edificio.

## <a name="conversion-pattern"></a>Modello di conversione

Esistono diverse librerie di terze parti che possono essere usate durante la conversione di modelli basati su RDF in DTDL. Alcune di queste librerie consentono di caricare il file del modello in un grafo. È possibile scorrere il grafico cercando costrutti RDFS e OWL specifici e convertirli in DTDL.   

La tabella seguente è un esempio di come è possibile eseguire il mapping dei costrutti RDFS e OWL a DTDL. 

| Concetto di RDFS/OWL | Costrutto RDFS/OWL | Concetto DTDL | Costrutto DTDL |
| --- | --- | --- | --- |
| Classi | `owl:Class`<br>Suffisso IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sottoclassi | `owl:Class`<br>Suffisso IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfaccia | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Proprietà del tipo di dati | `owl:DatatypeProperty`<br>`rdfs:label` o `INode`<br>`rdfs:label`<br>`rdfs:range` | Proprietà dell'interfaccia | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Proprietà degli oggetti | `owl:ObjectProperty`<br>`rdfs:label` o `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (o omesso se non `rdfs:range` è presente )<br>`comment`<br>`displayName`<br>

Il frammento di codice C# seguente illustra come un file di modello RDF viene caricato in un grafo e convertito in DTDL usando la [**libreria dotNetRDF.**](https://www.dotnetrdf.org/) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Esempi di convertitore

### <a name="rdf-converter-application"></a>Applicazione convertitore RDF 

È disponibile un'applicazione di esempio che converte un file di modello basato su RDF in [DTDL (versione 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per l'uso da parte Gemelli digitali di Azure servizio. È stato convalidato per lo schema [Brick](https://brickschema.org/ontology/) e può essere esteso per altri schemi nel settore dell'edificio,ad esempio [Building Topology Ontology (BOT),](https://w3c-lbd-cg.github.io/bot/) [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)o [buildingSmart Industry Foundation Classes (IFC).](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)

L'esempio è un'applicazione della riga di comando .NET Core denominata **RdfToDtdlConverter**.

È possibile ottenere l'esempio qui: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Per scaricare il codice nel  computer, selezionare il pulsante Sfoglia codice sotto il titolo nella pagina di esempio, che consente di accedere al repository GitHub per l'esempio. Selezionare il **pulsante Codice** e **scarica ZIP** per scaricare l'esempio come *. File ZIP* denominato *RdfToDtdlConverter-main.zip*. È quindi possibile decomprimere il file ed esplorare il codice.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Screenshot del repository RdfToDtdlConverter in GitHub. Il pulsante Codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

È possibile usare questo esempio per visualizzare i modelli di conversione nel contesto e avere come blocco predefinito per le proprie applicazioni che eseguono conversioni di modelli in base alle proprie esigenze specifiche.

### <a name="owl2dtdl-converter"></a>Convertitore OWL2DTDL 

[**OWL2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) è un esempio che converte un'ontologia OWL in un set di dichiarazioni di interfaccia DTDL, che possono essere usate con il servizio Gemelli digitali di Azure. Funziona anche per le reti di ontologia, fatta di un'ontologia radice che riutilizza altre ontologi tramite `owl:imports` dichiarazioni.

Questo convertitore è stato usato per convertire [l'ontologia](https://doc.realestatecore.io/3.1/full.html) di Real Estate Core in DTDL e può essere usato per qualsiasi ontologia basata su OWL.

## <a name="next-steps"></a>Passaggi successivi 

* Altre informazioni sull'estensione di ontologi standard del settore per soddisfare le specifiche: [*Concetti: Estensione delle ontologi del settore.*](concepts-ontologies-extend.md)

* Oppure, continuare il percorso per lo sviluppo di modelli basati su ontologie: [*Uso di strategie ontologia in un percorso di sviluppo di modelli*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).