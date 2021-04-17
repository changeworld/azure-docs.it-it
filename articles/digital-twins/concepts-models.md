---
title: Modelli DTDL
titleSuffix: Azure Digital Twins
description: Comprendere come Gemelli digitali di Azure modelli personalizzati per descrivere le entità nell'ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3f0dd599f982e19fee7febc3b85d46f91a55b35
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589296"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Informazioni sui modelli gemelli in Gemelli digitali di Azure

Una caratteristica chiave di Gemelli digitali di Azure è la possibilità di definire il proprio vocabolario e creare il grafico gemello nei termini autodefiniti dell'azienda. Questa funzionalità viene fornita tramite modelli forniti **dall'utente.** È possibile pensare ai modelli come ai sostantivi in una descrizione del mondo. 

Un modello è simile a una **classe** in un linguaggio di programmazione orientato a oggetti, definendo una forma di dati per un concetto specifico nell'ambiente di lavoro reale. I modelli hanno nomi (ad esempio *Room* o *TemperatureSensor)* e contengono elementi quali proprietà, dati di telemetria/eventi e comandi che descrivono le attività che possono eseguire questo tipo di entità nell'ambiente. Successivamente, questi modelli verranno utilizzati per creare [**gemelli digitali**](concepts-twins-graph.md) che rappresentano entità specifiche che soddisfano questa descrizione del tipo.

Gemelli digitali di Azure modelli sono rappresentati nel linguaggio **DTDL (Digital Twin Definition Language)** basato su JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>DTDL (Digital Twin Definition Language) per i modelli

I modelli Gemelli digitali di Azure vengono definiti usando il linguaggio DTDL (Digital Twins Definition Language). 

È possibile visualizzare le specifiche del linguaggio completo per DTDL in GitHub: [**DTDL (Digital Twins Definition Language) - Versione 2.**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)

DTDL è basato su JSON-LD ed è indipendente dal linguaggio di programmazione. DTDL non è esclusivo Gemelli digitali di Azure, ma viene usato anche per rappresentare i dati dei dispositivi in altri servizi IoT, ad esempio [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Gemelli digitali di Azure usa DTDL **versione 2** (l'uso di DTDL versione 1 con Gemelli digitali di Azure è stato deprecato). 

Il resto di questo articolo riepiloga come viene usata la lingua in Gemelli digitali di Azure.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Gemelli digitali di Azure specifiche dell'implementazione DTDL

Non tutti i servizi che usano DTDL implementano esattamente le stesse funzionalità di DTDL. Ad esempio, IoT Plug and Play non usa le funzionalità DTDL per i grafi, mentre Gemelli digitali di Azure non implementa attualmente i comandi DTDL. 

Perché un modello DTDL sia compatibile con Gemelli digitali di Azure, deve soddisfare i requisiti seguenti:

* Tutti gli elementi DTDL di primo livello in un modello devono essere di tipo *interfaccia*. Questo perché Gemelli digitali di Azure API del modello possono ricevere oggetti JSON che rappresentano un'interfaccia o una matrice di interfacce. Di conseguenza, non sono consentiti altri tipi di elementi DTDL al livello superiore.
* DTDL per Gemelli digitali di Azure non deve definire alcun *comando*.
* Gemelli digitali di Azure consente solo un singolo livello di annidamento dei componenti. Ciò significa che un'interfaccia usata come componente non può avere componenti. 
* Le interfacce non possono essere definite inline all'interno di altre interfacce DTDL. Devono essere definite come entità di primo livello separate con i propri ID. Quindi, quando un'altra interfaccia vuole includere tale interfaccia come componente o tramite ereditarietà, può fare riferimento al relativo ID.

Gemelli digitali di Azure inoltre non osserva `writable` l'attributo su proprietà o relazioni. Anche se può essere impostato in base alle specifiche DTDL, il valore non viene usato da Gemelli digitali di Azure. Al contrario, questi vengono sempre considerati scrivibili da client esterni che dispongono di autorizzazioni di scrittura generali per il Gemelli digitali di Azure servizio.

## <a name="elements-of-a-model"></a>Elementi di un modello

All'interno di una definizione di modello, l'elemento di codice di primo livello è **un'interfaccia**. che incapsula l'intero modello, mentre il resto del modello viene definito all'interno dell'interfaccia. 

Un'interfaccia del modello DTDL può contenere zero, uno o molti dei campi seguenti:
* **Proprietà:** le proprietà sono campi dati che rappresentano lo stato di un'entità, come le proprietà in molti linguaggi di programmazione orientati a oggetti. Le proprietà dispongono di spazio di archiviazione di backup e possono essere lette in qualsiasi momento.
* **Telemetria:** i campi di telemetria rappresentano misurazioni o eventi e vengono spesso usati per descrivere le letture dei sensori dei dispositivi. A differenza delle proprietà, i dati di telemetria non vengono archiviati in un gemello digitale. è una serie di eventi dati associati a tempo che devono essere gestiti non appena si verificano. Per altre informazioni sulle differenze tra proprietà e telemetria, vedere la sezione Confronto tra proprietà e [*telemetria*](#properties-vs-telemetry) di seguito.
* **Componente:** i componenti consentono di compilare l'interfaccia del modello come assembly di altre interfacce, se necessario. Un esempio di componente è un'interfaccia *frontCamera* (e un'altra interfaccia del componente *backCamera*) che vengono usate nella definizione di un modello per un *telefono.* È prima necessario definire un'interfaccia *per frontCamera* come se fosse un proprio modello e quindi è possibile fare riferimento a essa quando si definisce *Phone*.

    Usare un componente per descrivere un elemento che è parte integrante della soluzione, ma non richiede un'identità separata e non deve essere creato, eliminato o riorganizzato nel grafico gemello in modo indipendente. Se si vuole che le entità presentino esistenze indipendenti nel grafo gemello, rappresentarle come gemelli digitali separati di modelli diversi, connessi da relazioni *(vedere* il punto successivo).
    
    >[!TIP] 
    >I componenti possono essere usati anche per l'organizzazione, per raggruppare set di proprietà correlate all'interno di un'interfaccia del modello. In questo caso, è possibile pensare a ogni componente come a uno spazio dei nomi o a una "cartella" all'interno dell'interfaccia.
* **Relazione:** le relazioni consentono di rappresentare il modo in cui un gemello digitale può essere coinvolto con altri gemelli digitali. Le relazioni possono rappresentare significati semantici diversi, ad esempio *contains* ("floor contains room"), *cools* ("hvac cools room"), *isBilledTo* ("il refrigeratore viene fatturato all'utente") e così via. Le relazioni consentono alla soluzione di fornire un grafo di entità correlate. Le relazioni possono anche [avere proprietà](#properties-of-relationships) proprie.

> [!NOTE]
> La [specifica per DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definisce anche **i** comandi , che sono metodi che possono essere eseguiti su un gemello digitale ,ad esempio un comando di reimpostazione o un comando per attivare o disattivare una ventola. Tuttavia, *i comandi non sono attualmente supportati in Gemelli digitali di Azure.*

### <a name="properties-vs-telemetry"></a>Proprietà e telemetria

Ecco alcune indicazioni aggiuntive sulla distinzione  tra la proprietà DTDL e i campi **di** telemetria in Gemelli digitali di Azure.

La differenza tra proprietà e dati di telemetria per Gemelli digitali di Azure modelli è la seguente:
* **Si** prevede che le proprietà presentino spazio di archiviazione di backup. Ciò significa che è possibile leggere una proprietà in qualsiasi momento e recuperarne il valore. Se la proprietà è scrivibile, è anche possibile archiviare un valore nella proprietà .  
* **La telemetria** è più simile a un flusso di eventi. è un set di messaggi di dati con una durata breve. Se non si configura l'ascolto dell'evento e delle azioni da intraprendere quando si verifica, non è disponibile alcuna traccia dell'evento in un secondo momento. Non è possibile tornare a questa pagina e leggerla in un secondo momento. 
  - In termini di C#, la telemetria è simile a un evento C#. 
  - In termini IoT, i dati di telemetria sono in genere una singola misura inviata da un dispositivo.

**I** dati di telemetria vengono spesso usati con i dispositivi IoT, perché molti dispositivi non sono in grado di archiviare o di cui sono interessati i valori di misurazione generati. Li inviano semplicemente come flusso di eventi di "telemetria". In questo caso, non è possibile chiedere al dispositivo in qualsiasi momento il valore più recente del campo di telemetria. Sarà invece necessario restare in ascolto dei messaggi dal dispositivo ed eseguire azioni all'arrivo dei messaggi. 

Di conseguenza, quando si progetta un modello in  Gemelli digitali di Azure, nella maggior parte dei casi si useranno le proprietà per modellare i gemelli. In questo modo è possibile avere l'archiviazione di backup e la possibilità di leggere ed eseguire query sui campi dati.

I dati di telemetria e le proprietà spesso funzionano insieme per gestire l'ingresso dei dati dai dispositivi. Poiché tutti i dati in ingresso Gemelli digitali di Azure tramite le API [,](how-to-use-apis-sdks.md)in genere si usa la funzione in ingresso per leggere i dati di telemetria o gli eventi delle proprietà dai dispositivi e impostare una proprietà in Gemelli digitali di Azure in risposta. 

È anche possibile pubblicare un evento di telemetria dall'API Gemelli digitali di Azure dati. Come per altri dati di telemetria, si tratta di un evento di breve durata che richiede la gestione di un listener.

#### <a name="properties-of-relationships"></a>Proprietà delle relazioni

DTDL consente anche **alle relazioni** di avere proprietà proprie. Quando si definisce una relazione all'interno di un modello DTDL, la relazione può avere un proprio campo in cui è possibile definire proprietà personalizzate per descrivere lo `properties` stato specifico della relazione.

## <a name="model-inheritance"></a>Ereditarietà del modello

In alcuni casi può essere necessario specializzare ulteriormente un modello. Ad esempio, potrebbe essere utile avere un modello generico *Room* e varianti specializzate *ConferenceRoom* e *Gym*. Per esprimere la specializzazione, DTDL supporta l'ereditarietà: le interfacce possono ereditare da una o più altre interfacce. 

L'esempio seguente ri-immagina il modello *Planet* dell'esempio DTDL precedente come sottotipo di un modello *CelestialBody più* grande. Il modello "padre" viene definito per primo e quindi il modello "figlio" si basa su di esso usando il campo `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

In questo *esempio, CelestialBody* contribuisce con un nome, una massa e una temperatura a *Planet*. La sezione è un nome di interfaccia o una matrice di nomi di interfaccia ,consentendo all'interfaccia di estensione di ereditare `extends` da più modelli padre, se lo si desidera.

Dopo l'applicazione dell'ereditarietà, l'interfaccia che estende espone tutte le proprietà dell'intera catena di ereditarietà.

L'interfaccia di estensione non può modificare le definizioni delle interfacce padre. può essere aggiunto solo ad essi. Non può inoltre ridefinire una funzionalità già definita in nessuna delle interfacce padre (anche se le funzionalità sono definite come uguali). Ad esempio, se un'interfaccia padre definisce una massa di proprietà , l'interfaccia che estende non può contenere una dichiarazione di massa , anche se `double` è anche un oggetto   `double` .

## <a name="model-code"></a>Codice del modello

I modelli di tipo gemello possono essere scritti in qualsiasi editor di testo. Il linguaggio DTDL segue la sintassi JSON, pertanto è consigliabile archiviare i modelli con estensione *json*. L'uso dell'estensione JSON consentirà a molti editor di testo di programmazione di fornire il controllo della sintassi di base e l'evidenziazione per i documenti DTDL. È disponibile anche [un'estensione DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) [per](https://code.visualstudio.com/)Visual Studio Code .

### <a name="possible-schemas"></a>Schemi possibili

In base a DTDL, lo schema per gli attributi *Property* e *Telemetry* può essere di tipi primitivi standard, , , e di altri `integer` `double` `string` `Boolean` tipi, ad `DateTime` esempio e `Duration` . 

Oltre ai tipi primitivi, i *campi Proprietà* e *Telemetria* possono avere questi tipi complessi:
* `Object`
* `Map`
* `Enum`

*I campi* di telemetria supportano anche `Array` .

### <a name="example-model"></a>Modello di esempio

Questa sezione contiene un esempio di un modello tipico, scritto come interfaccia DTDL. Il modello descrive i **pianeta,** ognuno con un nome, una massa e una temperatura.
 
Si consideri che i satelliti possono anche interagire con **le** lune che sono i loro satelliti e possono contenere **i 10.** Nell'esempio seguente il modello esprime le connessioni a queste altre entità facendo riferimento `Planet` a due modelli esterni, e `Moon` `Crater` . Questi modelli sono definiti anche nel codice di esempio seguente, ma vengono mantenuti molto semplici per non tralzare dall'esempio `Planet` principale.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

I campi del modello sono:

| Campo | Descrizione |
| --- | --- |
| `@id` | Identificatore per il modello. Deve essere nel formato `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifica il tipo di informazioni descritte. Per un'interfaccia, il tipo è *Interface*. |
| `@context` | Imposta il [contesto](https://niem.github.io/json/reference/json-ld/context/) per il documento JSON. I modelli devono usare `dtmi:dtdl:context;2` . |
| `displayName` | [facoltativo] Consente di assegnare al modello un nome descrittivo, se necessario. |
| `contents` | Tutti i dati di interfaccia rimanenti vengono inseriti qui, come matrice di definizioni di attributi. Ogni attributo deve fornire ( Proprietà , Telemetria , Comando , Relazione o Componente ) per identificare il tipo di informazioni sull'interfaccia che descrive e quindi un set di proprietà che definiscono l'attributo effettivo `@type` (ad esempio,  e per definire una  `name` `schema` *proprietà*). |

> [!NOTE]
> Si noti che l'interfaccia del componente ( In questo esempio *, In* questo esempio) è definita nella stessa matrice dell'interfaccia che la usa (*Planet*). I componenti devono essere definiti in questo modo nelle chiamate API per trovare l'interfaccia.

## <a name="best-practices-for-designing-models"></a>Procedure consigliate per la progettazione di modelli

Durante la progettazione di modelli per riflettere le entità nell'ambiente, può essere utile guardare avanti e considerare le implicazioni delle [query](concepts-query-language.md) della progettazione. È possibile progettare le proprietà in modo da evitare set di risultati di grandi dimensioni dall'attraversamento del grafico. È anche possibile modellare le relazioni a cui si dovrà rispondere in una singola query come relazioni a livello singolo.

### <a name="validating-models"></a>Convalida dei modelli

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Strumenti per i modelli 

Sono disponibili diversi esempi per semplificare la gestione di modelli e ontologi. Si trovano in questo repository: [Tools for Digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-tools)

Questa sezione descrive il set corrente di esempi in modo più dettagliato.

### <a name="model-uploader"></a>Caricatore di modelli 

_**Per il caricamento di modelli in Gemelli digitali di Azure**_

Al termine della creazione, dell'estensione o della selezione dei modelli, è possibile caricarli nell'istanza Gemelli digitali di Azure per renderli disponibili per l'uso nella soluzione. Questa operazione viene eseguita [usando le API Gemelli digitali di Azure](how-to-use-apis-sdks.md), come descritto in [*Procedura: Gestire i modelli DTDL.*](how-to-manage-model.md#upload-models)

Tuttavia, se sono disponibili molti modelli da caricare o se hanno molte interdipendenze che renderebbero complicato l'ordinamento dei singoli caricamenti, è possibile usare questo esempio per caricare molti modelli [**contemporaneamente: Gemelli digitali di Azure Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Seguire le istruzioni fornite con l'esempio per configurare e usare questo progetto per caricare i modelli nella propria istanza.

### <a name="model-visualizer"></a>Visualizzatore di modelli 

_**Per visualizzare i modelli**_

Dopo aver caricato i modelli nell'istanza di Gemelli digitali di Azure, è possibile visualizzare i modelli nell'istanza di Gemelli digitali di Azure, incluse eventuali relazioni di ereditarietà e modello, usando Gemelli digitali di Azure [**Model Visualizer**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Questo esempio è attualmente in uno stato bozza. Si consiglia alla community di sviluppo di gemelli digitali di estendere e contribuire all'esempio. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla creazione di modelli basati su ontologie standard del settore: [ *Concetti: Che cos'è un'ontologia?*](concepts-ontologies.md)

* Approfondimento sulla gestione dei modelli con le operazioni API: [ *Procedura: Gestire i modelli DTDL*](how-to-manage-model.md)

* Informazioni su come vengono usati i modelli per creare gemelli digitali: [ *Concetti: Gemelli digitali e grafo dei gemelli*](concepts-twins-graph.md)

