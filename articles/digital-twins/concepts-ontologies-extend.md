---
title: Estensione di ontologi
titleSuffix: Azure Digital Twins
description: Informazioni sui motivi e sulle strategie alla base dell'estensione di un'ontologia
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480231"
---
# <a name="extending-ontologies"></a>Estensione di ontologi 

[Un'ontologia](concepts-ontologies.md)standard del settore, ad esempio [l'ontologia RealEstateCore](https://github.com/Azure/opendigitaltwins-building)basata su DTDL per edifici intelligenti, è un ottimo modo per iniziare a creare la soluzione IoT. Gli ontologi del settore offrono un set completo di interfacce di base progettate per il dominio e progettate per funzionare in modo predefinito nei servizi Azure IoT, ad esempio Gemelli digitali di Azure. 

Tuttavia, è possibile che la soluzione abbia esigenze specifiche che non sono coperte dall'ontologia del settore. Ad esempio, è possibile collegare i gemelli digitali ai modelli 3D archiviati in un sistema separato. In questo caso, è possibile estendere una di queste ontologie per aggiungere funzionalità proprie mantenendo al tempo stesso tutti i vantaggi dell'ontologia originale.

Questo articolo usa l'ontologia [RealEstateCore](https://www.realestatecore.io/) basata su DTDL come base per esempi di estensione di ontologi con nuove proprietà DTDL. Le tecniche descritte qui sono tuttavia generali e possono essere applicate a qualsiasi parte di un'ontologia basata su DTDL con qualsiasi funzionalità DTDL (telemetria, proprietà, relazione, componente o comando). 

## <a name="realestatecore-space-hierarchy"></a>Gerarchia dello spazio RealEstateCore 

Nell'ontologia RealEstateCore basata su DTDL, la gerarchia Spazio viene usata per definire vari tipi di spazi: stanze, edifici, zona e così via. La gerarchia si estende da ognuno di questi modelli per definire vari tipi di stanze, edifici e zone. 

Una parte della gerarchia è simile al diagramma seguente. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Diagramma di flusso che illustra parte della gerarchia dello spazio RealEstateCore. Al livello superiore è presente un elemento denominato Space. è connesso da una freccia &quot;estende&quot; verso il basso di un livello fino a Room; La stanza è connessa da due frecce &quot;estende&quot; verso il basso di un livello fino a ConferenceRoom e Office."::: 

Per altre informazioni sull'ontologia Di RealEstateCore, vedere [*Concetti: Adozione di ontologi standard del settore.*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)

## <a name="extending-the-realestatecore-space-hierarchy"></a>Estensione della gerarchia dello spazio RealEstateCore 

A volte la soluzione ha esigenze specifiche che non sono coperte dall'ontologia del settore. In questo caso, l'estensione della gerarchia consente di continuare a usare l'ontologia del settore durante la personalizzazione in base alle esigenze. 

In questo articolo vengono illustrati due casi diversi in cui è utile estendere la gerarchia dell'ontologia: 

* Aggiunta di nuove interfacce per i concetti non nell'ontologia del settore. 
* Aggiunta di altre proprietà (o relazioni, componenti, dati di telemetria o comandi) alle interfacce esistenti.

### <a name="add-new-interfaces-for-new-concepts"></a>Aggiungere nuove interfacce per i nuovi concetti 

In questo caso, si vogliono aggiungere interfacce per i concetti necessari per la soluzione, ma che non sono presenti nell'ontologia del settore. Ad esempio, se la soluzione ha altri tipi di stanze che non sono rappresentate nell'ontologia RealEstateCore basata su DTDL, è possibile aggiungerle estendendole direttamente dalle interfacce RealEstateCore. 

L'esempio seguente presenta una soluzione che deve rappresentare le "sale riunioni", che non sono presenti nell'ontologia RealEstateCore. Una sala di interesse è un piccolo spazio progettato per consentire agli utenti di concentrarsi su un'attività per un paio di ore alla volta. 

Per estendere l'ontologia del settore con questo [](concepts-models.md#model-inheritance) nuovo concetto, creare una nuova interfaccia che si estende dalle interfacce nell'ontologia del settore. 

Dopo aver aggiunto l'interfaccia della stanza di interesse, la gerarchia estesa mostra il nuovo tipo di stanza. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Diagramma di flusso che illustra la gerarchia dello spazio RealEstateCore precedente, con una nuova aggiunta. Al livello inferiore con ConferenceRoom e Office è presente un nuovo elemento denominato FocusRoom (connesso anche tramite una freccia &quot;estende&quot; da Room)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Aggiungere altre funzionalità alle interfacce esistenti 

In questo caso, si vogliono aggiungere altre proprietà (o relazioni, componenti, dati di telemetria o comandi) alle interfacce nell'ontologia del settore.

In questa sezione verranno visualizzati due esempi: 
* Se si sta creando una soluzione che visualizza disegni 3D di spazi già presenti in un sistema esistente, è possibile associare ogni gemello digitale al relativo disegno 3D (in base all'ID) in modo che, quando la soluzione visualizza informazioni sullo spazio, possa anche recuperare il disegno 3D dal sistema esistente. 
* Se la soluzione deve tenere traccia dello stato online/offline delle sale riunioni, è possibile tenere traccia dello stato della sala riunioni da usare nella visualizzazione o nelle query. 

Entrambi gli esempi possono essere implementati con nuove proprietà: una proprietà che associa il disegno 3D al gemello digitale e una proprietà "online" che indica se la sala riunioni è `drawingId` online o meno. 

In genere, non si vuole modificare direttamente l'ontologia del settore perché si vuole poter incorporare gli aggiornamenti nella soluzione in futuro (sovrascrivendo così le aggiunte). Al contrario, questi tipi di aggiunte possono essere effettuate nella propria gerarchia di interfaccia che si estende dall'ontologia RealEstateCore basata su DTDL. Ogni interfaccia creata usa più ereditarietà dell'interfaccia per estendere l'interfaccia RealEstateCore padre e la relativa interfaccia padre dalla gerarchia dell'interfaccia estesa. Questo approccio consente di usare l'ontologia del settore e le aggiunte insieme. 

Per estendere l'ontologia del settore, si creano interfacce proprie che si estendono dalle interfacce nell'ontologia del settore e si aggiungono le nuove funzionalità alle interfacce estese. Per ogni interfaccia che si vuole estendere, si crea una nuova interfaccia. Le interfacce estese sono scritte in DTDL (vedere la sezione DTDL per le interfacce estese più avanti in questo documento). 

Dopo aver esteso la parte della gerarchia illustrata in precedenza, la gerarchia estesa sarà simile al diagramma seguente. In questo caso l'interfaccia spazio estesa aggiunge la proprietà che conterrà un `drawingId` ID che associa il gemello digitale al disegno 3D. Inoltre, l'interfaccia ConferenceRoom aggiunge una proprietà "online" che conterrà lo stato online della sala conferenze. Tramite l'ereditarietà, l'interfaccia ConferenceRoom contiene tutte le funzionalità dell'interfaccia ConferenceRoom RealEstateCore, nonché tutte le funzionalità dell'interfaccia space estesa. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Diagramma di flusso che illustra la gerarchia estesa dello spazio RealEstateCore precedente, con altre nuove aggiunte. Room ora condivide il proprio livello con un elemento Space, che si connette con una freccia &quot;estende&quot; verso il basso un livello a un nuovo elemento Room accanto a ConferenceRoom e Office.  I nuovi elementi sono connessi all'ontologia esistente con più relazioni &quot;extends&quot;."::: 

## <a name="using-the-extended-space-hierarchy"></a>Uso della gerarchia di spazi estesi 

Quando si creano gemelli digitali usando la gerarchia spaziale estesa, il modello di ogni gemello digitale sarà uno della gerarchia spaziale estesa (non l'ontologia originale del settore) e includerà tutte le funzionalità dell'ontologia del settore e delle interfacce estese anche se l'ereditarietà dell'interfaccia.

Il modello di ogni gemello digitale sarà un'interfaccia della gerarchia estesa, illustrata nel diagramma seguente. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Estratto della gerarchia estesa dello spazio RealEstateCore, tra cui Space (livello superiore), Room (livello intermedio) e ConferenceRoom, Office e FocusRoom (livello inferiore). I nomi dei modelli sono connessi a ogni elemento (ad esempio, Room è connesso a un modello denominato Room101)."::: 

Quando si esegue una query per i gemelli digitali usando l'ID modello (l'operatore), è necessario usare gli ID modello della `IS_OF_MODEL` gerarchia estesa. Ad esempio: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Contribuire all'ontologia originale 

In alcuni casi, si estenderà l'ontologia del settore in modo che sia ampiamente utile per la maggior parte degli utenti dell'ontologia. In questo caso, è consigliabile contribuire le estensioni all'ontologia originale. Ogni ontologia ha un processo diverso per contribuire, quindi controllare il repository GitHub dell'ontologia per informazioni dettagliate sui contributi. 

## <a name="dtdl-for-new-interfaces"></a>DTDL per nuove interfacce 

Il linguaggio DTDL per le nuove interfacce che si estendono direttamente dall'ontologia del settore sarà simile al seguente. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL per le interfacce estese 

Il linguaggio DTDL per le interfacce estese, limitato alla parte descritta in precedenza, sarà simile al seguente. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Passaggi successivi

Continuare nel percorso per lo sviluppo di modelli basati su ontologi: [*Uso di strategie di ontologia in un percorso di sviluppo del modello*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).