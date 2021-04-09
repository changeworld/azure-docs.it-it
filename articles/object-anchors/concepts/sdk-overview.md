---
title: Panoramica di Runtime SDK
description: Acquisire familiarità con l'SDK di runtime di Object Anchors.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 551374824610c0257aaf52c45768d31849026524
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047542"
---
# <a name="runtime-sdk-overview"></a>Panoramica di Runtime SDK

In questa sezione viene fornita una panoramica di alto livello di Object Anchors Runtime SDK, che consente di rilevare oggetti utilizzando un modello di ancoraggio di oggetti. Si otterrà una conoscenza della modalità di rappresentazione di un oggetto e dei diversi componenti utilizzati per.

Tutti i tipi descritti di seguito sono reperibili nello spazio dei nomi **Microsoft. MixedReality. ObjectAnchors** .

## <a name="types"></a>Tipi

### <a name="objectmodel"></a>ObjectModel

Un [ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) rappresenta la geometria di un oggetto fisico e codifica i parametri necessari per il rilevamento e la stima della posizione. È necessario crearlo utilizzando il [servizio ancoraggi oggetti](../quickstarts/get-started-model-conversion.md). Un'applicazione può quindi caricare il file di modello generato usando l'API Anchors dell'oggetto ed eseguire una query sul mesh incorporato nel modello per la visualizzazione.

### <a name="objectsearcharea"></a>ObjectSearchArea

Un [ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) specifica lo spazio in cui cercare uno o più oggetti. Viene definito da un ID nodo del grafico spaziale e da limiti spaziali nel sistema di coordinate rappresentato dall'ID del nodo del grafico spaziale. Object Anchors Runtime SDK supporta quattro tipi di limiti, ovvero **campo di visualizzazione**, rettangolo di **delimitazione**, **sfera** e **posizione**.

### <a name="objectquery"></a>ObjectQuery

Un oggetto [ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) indica a un **oggetto Observer** come trovare gli oggetti di un determinato modello. Fornisce i seguenti parametri ottimizzabili, i cui valori predefiniti possono essere recuperati da un modello a oggetti.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

La proprietà [MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) indica il valore da considerare come un'istanza di rilevata.

Per ogni candidato oggetto, un **osservatore** calcola il rapporto tra le superfici sovrapposte tra il modello a oggetti trasformato e la scena, quindi segnala tale candidato all'applicazione solo quando il rapporto di code coverage supera una determinata soglia.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

La proprietà [IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) indica se è previsto che l'oggetto di destinazione si trovi sul piano di base.

Un piano terra è il piano orizzontale più basso nell'area di ricerca. Fornisce un valido vincolo sulle possibili pose degli oggetti. L'attivazione di questo flag guiderà l' **osservatore** per stimare la funzione in uno spazio limitato e potrebbe migliorare la precisione. Questo parametro verrà ignorato se il modello non deve basarsi sul piano di base.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

La proprietà [ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) indica l'angolo massimo previsto in gradi tra la direzione di un'istanza dell'oggetto e la gravità.

Questo parametro fornisce un altro vincolo sulla direzione di una condizione stimata. Se, ad esempio, un oggetto è attivo, questo parametro può essere 0. Gli ancoraggi degli oggetti non devono rilevare oggetti diversi dal modello. Se un modello è attivo, non verrà rilevata un'istanza disposta dal lato inferiore. Verrà usato un nuovo modello per il layout affiancato. La stessa regola si applica per l'articolazione.

#### <a name="maxscalechange"></a>MaxScaleChange

La proprietà [MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) indica la modifica della scalabilità di oggetti massima (entro 0 ~ 1) rispetto al mapping spaziale. La scala stimata viene applicata ai vertici degli oggetti trasformati centrati all'origine e allineati all'asse. Le scale stimate potrebbero non essere la scala effettiva tra un modello CAD e la relativa rappresentazione fisica, ma alcuni valori che consentono all'app di eseguire il rendering di un modello a oggetti vicino al mapping spaziale nell'oggetto fisico.

#### <a name="searchareas"></a>SearchAreas

La proprietà [SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) indica una matrice di limiti spaziali in cui trovare uno o più oggetti.

L' **Observer** cercherà gli oggetti nello spazio di Unione di tutte le aree di ricerca specificate in una query. In questa versione, verrà restituito al massimo un oggetto con la massima confidenza per ridurre la latenza.

### <a name="objectinstance"></a>ObjectInstance

Un [ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) rappresenta una posizione ipotetica in cui un'istanza di un determinato modello potrebbe trovarsi nel sistema di coordinate HoloLens. Ogni istanza viene fornita con una `SurfaceCoverage` proprietà per indicare la qualità della condizione stimata.

Un'istanza viene creata chiamando `ObjectObserver.DetectAsync` il metodo, quindi viene aggiornata automaticamente in background quando è attiva. Un'applicazione può restare in ascolto dell'evento di modifica dello stato in una particolare istanza o modificare la modalità di rilevamento per sospendere o riprendere l'aggiornamento. Un'istanza verrà automaticamente rimossa dall' **Observer** quando il rilevamento viene perso.

### <a name="objectobserver"></a>ObjectObserver

Un [ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) carica i modelli a oggetti, ne rileva le istanze e segnala 6-DOF pose di ogni istanza nel sistema di coordinate HoloLens.

Sebbene qualsiasi modello a oggetti o istanza venga creato da un **osservatore**, le loro durate sono indipendenti. Un'applicazione può eliminare un Observer e continuare a utilizzare il modello a oggetti o l'istanza.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

Il [ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) registra la diagnostica e scrive i dati in un archivio.

Un archivio di diagnostica include il cloud di punti della scena, lo stato dell'osservatore e le informazioni sui modelli. Queste informazioni sono utili per identificare i possibili problemi di Runtime. Per altre informazioni, vedere la sezione [Domande frequenti](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Utilizzo e dettagli dell'SDK di runtime

In questa sezione vengono fornite le nozioni di base su come utilizzare Runtime SDK. Dovrebbe fornire un contesto sufficiente per esplorare le applicazioni di esempio per vedere come vengono usati in modo olistico gli ancoraggi degli oggetti.

### <a name="initialization"></a>Inizializzazione

Le applicazioni devono chiamare l' `ObjectObserver.IsSupported()` API per determinare se gli ancoraggi degli oggetti sono supportati nel dispositivo prima di usarlo. Se l' `ObjectObserver.IsSupported()` API restituisce `false` , verificare che l'applicazione abbia abilitato l'aggiornamento della funzionalità **spatialPerception** E\o al sistema operativo HoloLens più recente.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Successivamente, l'applicazione crea un Observer di oggetti e carica i modelli necessari generati dal [servizio di conversione del modello di ancoraggio degli oggetti](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

L'applicazione crea una query per rilevare le istanze del modello all'interno di uno spazio.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Per impostazione predefinita, ogni istanza rilevata verrà rilevata automaticamente dall' **Observer**. Facoltativamente, è possibile modificare queste istanze modificando la modalità di rilevamento o ascoltando il relativo evento di modifica dello stato.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

Nell'evento di modifica dello stato, è possibile eseguire una query sullo stato più recente o eliminare un'istanza in caso di perdita del rilevamento.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Inoltre, un'applicazione può registrare facoltativamente una o più sessioni di diagnostica per il debug offline.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Infine, vengono rilasciate le risorse eliminando tutti gli oggetti.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```