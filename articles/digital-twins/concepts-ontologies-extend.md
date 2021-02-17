---
title: Estensione di ontologie
titleSuffix: Azure Digital Twins
description: Informazioni sui motivi e sulle strategie di estensione di un'ontologia
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561542"
---
# <a name="extending-ontologies"></a>Estensione di ontologie 

Una [ontologia](concepts-ontologies.md)standard del settore, ad esempio l' [ontologia RealEstateCore basata su DTDL per gli edifici intelligenti](https://github.com/Azure/opendigitaltwins-building), è un ottimo modo per iniziare a creare la tua soluzione Internet delle cose. Le ontologie del settore forniscono un set completo di interfacce di base progettate per il dominio e progettate per funzionare in modo predefinito nei servizi di Azure, ad esempio i dispositivi gemelli digitali di Azure. 

Tuttavia, è possibile che la soluzione possa avere esigenze specifiche non coperte dall'ontologia del settore. Ad esempio, potrebbe essere necessario collegare i dispositivi gemelli digitali ai modelli 3D archiviati in un sistema distinto. In questo caso, è possibile estendere una di queste ontologie per aggiungere funzionalità personalizzate mantenendo tutti i vantaggi dell'ontologia originale.

Questo articolo usa l'ontologia [RealEstateCore](https://www.realestatecore.io/) basata su DTDL come base per esempi di estensione delle ontologie con le nuove proprietà DTDL. Le tecniche descritte di seguito sono tuttavia generali e possono essere applicate a qualsiasi parte di un'ontologia basata su DTDL con qualsiasi funzionalità di DTDL (telemetria, proprietà, relazione, componente o comando). 

## <a name="realestatecore-space-hierarchy"></a>Gerarchia dello spazio RealEstateCore 

Nell'ontologia RealEstateCore basata su DTDL, la gerarchia di spazio viene usata per definire vari tipi di spazi: stanze, edifici, zone e così via. La gerarchia si estende da ognuno di questi modelli per definire vari tipi di stanze, edifici e zone. 

Una parte della gerarchia ha un aspetto simile al diagramma seguente. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Diagramma di flusso che illustra parte della gerarchia di spazio RealEstateCore. Al livello principale, c'è un elemento denominato Space; è connesso da una freccia &quot;extends&quot; verso il basso di un livello in room; La chat room è connessa con due frecce ' extends ' verso il basso di un livello verso ConferenceRoom e Office."::: 

Per altre informazioni sull'ontologia RealEstateCore, vedere [*concetti: adozione di ontologie standard del settore*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Estensione della gerarchia dello spazio RealEstateCore 

A volte la soluzione ha esigenze specifiche che non sono coperte dall'ontologia del settore. In questo caso, l'estensione della gerarchia consente di continuare a usare l'ontologia del settore e di personalizzarla in base alle proprie esigenze. 

In questo articolo vengono illustrati due casi diversi in cui l'estensione della gerarchia di ontologia è utile: 

* Aggiunta di nuove interfacce per i concetti non presenti nell'ontologia del settore. 
* Aggiunta di proprietà aggiuntive (o relazioni, componenti, telemetria o comandi) a interfacce esistenti.

### <a name="add-new-interfaces-for-new-concepts"></a>Aggiungi nuove interfacce per i nuovi concetti 

In questo caso, si desidera aggiungere interfacce per i concetti necessari per la soluzione ma che non sono presenti nell'ontologia del settore. Ad esempio, se la soluzione ha altri tipi di chat che non sono rappresentati nell'ontologia RealEstateCore basata su DTDL, è possibile aggiungerli estendendo direttamente dalle interfacce RealEstateCore. 

Nell'esempio seguente viene presentata una soluzione che deve rappresentare "chat room", che non sono presenti nell'ontologia RealEstateCore. Una stanza concentrata è uno spazio ridotto progettato per gli utenti per concentrarsi su un'attività per un paio di ore alla volta. 

Per estendere l'ontologia del settore con questo nuovo concetto, creare una nuova interfaccia che si [estenda dalle](concepts-models.md#model-inheritance) interfacce nell'ontologia del settore. 

Dopo l'aggiunta dell'interfaccia dello stato attivo, nella gerarchia estesa viene visualizzato il nuovo tipo di chat room. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Diagramma di flusso che illustra la gerarchia dello spazio RealEstateCore precedente, con una nuova aggiunta. Al livello inferiore con ConferenceRoom e Office, è disponibile un nuovo elemento denominato FocusRoom (connesso anche tramite una freccia &quot;extends&quot; dalla chat room)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Aggiungere funzionalità aggiuntive alle interfacce esistenti 

In questo caso, è necessario aggiungere altre proprietà (o relazioni, componenti, telemetria o comandi) a interfacce che si trovano nell'ontologia del settore.

In questa sezione verranno visualizzati due esempi: 
* Se si compila una soluzione che Visualizza i disegni 3D di spazi già presenti in un sistema esistente, potrebbe essere necessario associare ogni dispositivo gemello digitale al disegno 3D (in base all'ID), in modo che quando la soluzione Visualizza informazioni sullo spazio, può anche recuperare il disegno 3D dal sistema esistente. 
* Se la soluzione deve tenere traccia dello stato online/offline delle sale riunioni, potrebbe essere necessario tenere traccia dello stato della sala riunioni per l'uso nella visualizzazione o nelle query. 

Entrambi gli esempi possono essere implementati con nuove proprietà: una `drawingId` proprietà che associa il disegno 3D al dispositivo gemello digitale e una proprietà "online" che indica se la sala riunioni è online o meno. 

In genere, non si vuole modificare direttamente l'ontologia del settore perché si vuole poter incorporare gli aggiornamenti nella soluzione in futuro (che sovrascriverà le aggiunte). Al contrario, questi tipi di aggiunte possono essere apportate nella gerarchia dell'interfaccia che si estende dall'ontologia RealEstateCore basata su DTDL. Ogni interfaccia creata usa più ereditarietà dell'interfaccia per estendere l'interfaccia RealEstateCore padre e la relativa interfaccia padre dalla gerarchia di interfacce estese. Questo approccio consente di usare l'ontologia del settore e le aggiunte insieme. 

Per estendere l'ontologia del settore, è possibile creare interfacce personalizzate che si estendono dalle interfacce nell'ontologia del settore e aggiungono nuove funzionalità alle interfacce estese. Per ogni interfaccia che si desidera estendere, è possibile creare una nuova interfaccia. Le interfacce estese sono scritte in DTDL (vedere la sezione DTDL per le interfacce estese più avanti in questo documento). 

Dopo l'estensione della parte della gerarchia illustrata in precedenza, la gerarchia estesa ha un aspetto simile al diagramma seguente. Nell'interfaccia di spazio esteso viene aggiunta la `drawingId` proprietà che conterrà un ID che associa il gemello digitale al disegno 3D. Inoltre, l'interfaccia ConferenceRoom aggiunge una proprietà "online" che conterrà lo stato online della sala riunioni. Tramite l'ereditarietà, l'interfaccia ConferenceRoom contiene tutte le funzionalità dell'interfaccia RealEstateCore ConferenceRoom, oltre a tutte le funzionalità dell'interfaccia dello spazio esteso. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Diagramma di flusso che illustra la gerarchia dello spazio RealEstateCore esteso precedente, con nuove aggiunte. Room ora condivide il livello con un elemento Space, che si connette con una freccia &quot;extends&quot; verso il basso di un livello a un nuovo elemento room accanto a ConferenceRoom e Office.  I nuovi elementi sono connessi all'ontologia esistente con più relazioni ' extends '."::: 

## <a name="using-the-extended-space-hierarchy"></a>Utilizzo della gerarchia di spazio esteso 

Quando si creano i dispositivi gemelli digitali usando la gerarchia di spazi estesi, il modello di ogni dispositivo digitale gemello sarà uno dalla gerarchia dello spazio esteso (non l'ontologia del settore originale) e includerà tutte le funzionalità dell'ontologia del settore e le interfacce estese, sebbene l'ereditarietà dell'interfaccia.

Ogni modello del gemello digitale sarà un'interfaccia dalla gerarchia estesa, illustrato nel diagramma seguente. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Estratto dalla gerarchia dello spazio RealEstateCore esteso, incluso lo spazio (livello superiore), una stanza (livello intermedio) e ConferenceRoom, Office e FocusRoom (livello inferiore). I nomi dei modelli sono connessi a ogni elemento (ad esempio, la chat room è connessa a un modello denominato room101)."::: 

Quando si eseguono query per i dispositivi gemelli digitali usando l'ID modello ( `IS_OF_MODEL` operatore), è necessario usare gli ID modello della gerarchia estesa. Ad esempio: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Contribuire all'ontologia originale 

In alcuni casi, si estenderà l'ontologia del settore in modo che sia ampiamente utile per la maggior parte degli utenti dell'ontologia. In questo caso, è necessario valutare l'opportunità di contribuire alle estensioni all'ontologia originale. Ogni ontologia ha un processo diverso per contribuire, quindi controllare il repository GitHub di ontologia per informazioni sui contributi. 

## <a name="dtdl-for-new-interfaces"></a>DTDL per le nuove interfacce 

Il DTDL per le nuove interfacce che si estendono direttamente dall'ontologia del settore dovrebbe essere simile al seguente. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL per le interfacce estese 

Il DTDL per le interfacce estese, limitato alla parte sopra descritta, avrà un aspetto simile al seguente. 

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

Continuare con il percorso per lo sviluppo di modelli basati su ontologie: [*uso delle strategie di ontologia in un percorso di sviluppo del modello*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).