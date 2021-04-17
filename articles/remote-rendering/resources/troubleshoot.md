---
title: Risolvere problemi
description: Informazioni sulla risoluzione dei problemi per Rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575344"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questa pagina elenca i problemi comuni che interferiscono con Rendering remoto di Azure e i metodi per risolverli.

## <a name="cant-link-storage-account-to-arr-account"></a>Non è possibile collegare l'account di archiviazione all'account ARR

In alcuni casi, durante il [collegamento di un account di archiviazione](../how-tos/create-an-account.md#link-storage-accounts), l'account di Rendering remoto non è presente nell'elenco. Per risolvere questo problema passare all'account ARR nel portale di Azure e selezionare **Identità** nel gruppo **Impostazioni** a sinistra. Verificare che **Stato** sia impostato su **Attivo**.
![Debugger fotogrammi di Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Il client non riesce a connettersi al server

Assicurarsi che i firewall (nel dispositivo, all'interno dei router e così via) non blocchino le porte indicate nei requisiti [di sistema](../overview/system-requirements.md#network-firewall).

## <a name="error-disconnected-videoformatnotavailable"></a>Errore ' `Disconnected: VideoFormatNotAvailable` '

Verificare che la GPU supporti la decodifica video hardware. Vedere [PC di sviluppo](../overview/system-requirements.md#development-pc).

Se si lavora su un computer portatile con due GPU, è possibile che la GPU su cui l'utente esegue operazioni per impostazione predefinita non fornisca funzionalità di decodifica video hardware. In tal caso, provare a forzare l'uso dell'altra GPU nell'app. Per svolgere l'operazione spesso è necessario accedere alle impostazioni del driver della GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Recupero dello stato sessione/conversione non riuscito

L'invio troppo frequente di comandi API REST causerà la limitazione del server e la restituzione dell'errore alla fine. Il codice di stato HTTP nel caso di limitazione è 429 ("troppe richieste"). Come regola generale, è necessario un ritardo di **5-10 secondi tra chiamate successive**.

Si noti che questo limite influisce non solo sulle chiamate API REST quando vengono chiamate direttamente, ma anche sulle controparti C#/C++, ad esempio `Session.GetPropertiesAsync` `Session.RenewAsync` , o `Frontend.GetAssetConversionStatusAsync` .

Se si verifica la limitazione sul lato server, modificare il codice per eseguire le chiamate meno frequentemente. Il server reimposta lo stato di limitazione ogni minuto, quindi è possibile rieseguire il codice dopo un minuto.

## <a name="h265-codec-not-available"></a>Il codec H265 non è disponibile

Esistono due motivi per cui il server potrebbe rifiutarsi di connettersi con un `codec not available` errore.

**Il codec H265 non è installato:**

Assicurarsi anzitutto di aver installato le **estensioni video HEVC** come indicato nella sezione [Software](../overview/system-requirements.md#software) dei requisiti di sistema.

Se ancora si riscontrano problemi, verificare che la scheda grafica supporti il codec H265 e che sia installato il driver grafico più recente. Per informazioni specifiche del fornitore, vedere la sezione [PC di sviluppo](../overview/system-requirements.md#development-pc) dei requisiti di sistema.

**Il codec è installato, ma non può essere usato:**

Il motivo di questo problema è un'impostazione di sicurezza non corretta per le DLL. Questo problema non viene rilevato quando si tenta di guardare video codificati con il codec H265. Reinstallare il codec non risolve il problema. In tal caso, seguire la procedura seguente:

1. Aprire una sessione di **PowerShell con diritti di amministratore** e avviare l'esecuzione

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Il comando deve restituire il parametro `InstallLocation` del codec, ad esempio:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Aprire la cartella in Esplora risorse
1. Si dovrebbero visualizzare le sottocartelle **x86** e **x64**. Fare clic con il pulsante destro del mouse su una delle cartelle e scegliere **Proprietà**
    1. Selezionare la scheda **Sicurezza** e fare clic sul pulsante delle impostazioni **Avanzate**
    1. Fare clic su **Modifica** per **Proprietario**
    1. Digitare **Amministratori** nel campo di testo
    1. Fare clic su **Controlla nomi** e quindi su **OK**
1. Ripetere la procedura precedente per l'altra cartella
1. Ripetere i passaggi precedenti anche in ogni file DLL all'interno di entrambe le cartelle. Dovrebbero essere presenti quattro DLL.

Per verificare che le impostazioni siano corrette, effettuare l'operazione per ognuna delle quattro DLL:

1. Fai clic su **Proprietà > Sicurezza > Modifica**.
1. Scorrere l'elenco di tutti i **Gruppi/utenti** e assicurarsi che per ognuno sia stato impostato il diritto **Lettura ed esecuzione** (deve essere presente il segno di spunta nella colonna **Consenti**)

## <a name="low-video-quality"></a>Qualità video scarsa

La qualità del video può essere compromessa dalla qualità della rete o dall'assenza del codec video H265.

* Vedere i passaggi per [identificare i problemi di rete](#unstable-holograms).
* Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver di grafica più recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>I video registrati con MRC non riflettono la qualità dell'esperienza dal vivo

È possibile registrare un video in HoloLens [tramite Acquisizione realtà mista (MRC).](/windows/mixed-reality/mixed-reality-capture-for-developers) Il video risultante presenta tuttavia una qualità peggiore rispetto all'esperienza dal vivo per due motivi:
* La frequenza di fotogrammi del video è limitata a 30 Hz e non a 60 Hz.
* Le immagini del video saltano il passaggio di elaborazione della [riproiezione con ritardo della fase](../overview/features/late-stage-reprojection.md), pertanto il video ha un aspetto maggiormente mosso.

Entrambe sono limitazioni intrinseche della tecnica di registrazione.

## <a name="black-screen-after-successful-model-loading"></a>Schermata nera dopo il completamento del caricamento del modello

Se dopo essersi connessi al runtime di rendering e aver caricato correttamente un modello viene visualizzata una schermata nera, possono esservi diverse cause.

Si consiglia di provare le soluzioni seguenti prima di svolgere un'analisi più approfondita:

* Il codec H265 è installato? Sebbene sia previsto un fallback per il codec H264, sono stati riscontrati casi in cui tale fallback non funzionava correttamente. Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver di grafica più recente.
* Quando si lavora con un progetto di Unity, chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/creare nuovamente il progetto. In alcuni casi, i dati memorizzati nella cache hanno impedito il funzionamento corretto dell'esempio, senza nessun motivo apparente.

Se questi due passaggi non sono stati utili, è necessario verificare se i fotogrammi del video vengono ricevuti o meno dal client. La query può essere eseguita a livello di codice come indicato nel capitolo [Query sulle prestazioni lato server](../overview/features/performance-queries.md). Il parametro `FrameStatistics struct` presenta un membro che indica il numero di fotogrammi video ricevuti. Se il numero è maggiore di 0 e aumenta nel tempo, il client riceve i fotogrammi video effettivi dal server. Di conseguenza, deve trattarsi di un problema lato client.

### <a name="common-client-side-issues"></a>Problemi comuni lato client

**Il modello supera i limiti della macchina virtuale selezionata, in particolare il numero massimo di poligoni:**

Vedere limiti [specifici per le dimensioni del server.](../reference/limits.md#overall-number-of-polygons)

**Il modello non si trova all'interno del frustum della fotocamera:**

In molti casi, il modello viene visualizzato correttamente ma si trova al di fuori del frustum della fotocamera. Una causa frequente potrebbe essere legata al fatto che il modello è stato esportato con una trasformazione tramite pivot decentrata in modo che venga ritagliato dal piano di ritaglio anteriore della fotocamera. Consente di eseguire una query sul rettangolo di selezione del modello a livello di codice e di visualizzare il riquadro di Unity come riquadro della riga o di stamparne i valori nel log di debug.

Inoltre, il processo di conversione genera un [file json di output](../how-tos/conversion/get-information.md) insieme al modello convertito. Per eseguire il debug dei problemi di posizionamento del modello è utile notare la voce `boundingBox` nella [sezione outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Il rettangolo di selezione è descritto come una posizione `min` e `max` nello spazio 3D, con un valore espresso in metri. Di conseguenza, una coordinata di 1000,0 indica una distanza di 1 chilometro dall'origine.

Con questo rettangolo di selezione possono sorgere due problemi da ricondurre alla geometria invisibile:
* **Il rettangolo può essere distante dal centro**, quindi l'oggetto viene ritagliato completamente a causa del ritaglio del piano anteriore. I valori `boundingBox` in questo caso hanno un aspetto simile al seguente: `min = [-2000, -5,-5], max = [-1990, 5,5]`, usando uno spostamento consistente sull'asse x come riportato nell'esempio di seguito. Per risolvere questo tipo di problema, abilitare l'opzione `recenterToOrigin` nella [configurazione della conversione del modello](../how-tos/conversion/configure-model-conversion.md).
* **Il rettangolo può essere centrato ma gli ordini di grandezza sono troppo ampi**. Ciò significa che, benché la fotocamera si avvii al centro del modello, la relativa geometria viene ritagliata in tutte le direzioni. In genere i valori `boundingBox` in questo caso hanno un aspetto simile al seguente: `min = [-1000,-1000,-1000], max = [1000,1000,1000]`. Il motivo di questo tipo di problema è solitamente una mancata corrispondenza nel dimensionamento delle unità. Per compensare, specificare un [valore di ridimensionamento durante la conversione](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) o contrassegnare il modello di origine con le unità corrette. Il dimensionamento può essere applicato anche al nodo radice durante il caricamento del modello al runtime.

**La pipeline di rendering di Unity non include gli hook di rendering:**

Rendering remoto di Azure include gli hook alla pipeline di rendering di Unity per eseguire la composizione del fotogramma con il video ed eseguire la riproiezione. Per verificare che questi hook esistano, aprire il menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Abilitarlo e verificare la presenza di due voci per `HolographicRemotingCallbackPass` nella pipeline:

![Pipeline di rendering unity](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Il rendering del modello a scacchiera viene eseguito dopo il caricamento del modello

Se l'immagine sottoposta a rendering è simile alla seguente: Screenshot mostra una griglia di quadratini in bianco e ![ nero con un menu Strumenti.](../reference/media/checkerboard.png)
quindi il renderer raggiunge i limiti [del poligono per le dimensioni di configurazione standard.](../reference/vm-sizes.md) Per attenuare il rischio, passare alle dimensioni di configurazione **Premium** o ridurre il numero di poligoni visibili.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>L'immagine sottoposta a rendering in Unity è capovolta

Assicurarsi di seguire l'esercitazione [di Unity: Visualizzare esattamente i modelli](../tutorials/unity/view-remote-models/view-remote-models.md) remoti. Un'immagine capovolta indica che Unity è necessario per creare una destinazione di rendering fuori schermo. Questo comportamento non è attualmente supportato e crea un notevole impatto sulle prestazioni HoloLens 2.

I motivi di questo problema potrebbero essere MSAA, HDR o l'abilitazione della post-elaborazione. Assicurarsi che il profilo di bassa qualità sia selezionato e impostato come predefinito in Unity. A tale scopo, passare *a Modifica impostazioni > progetto... > Qualità.*

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Il codice Unity che usa l'API di Rendering remoto non viene compilato

### <a name="use-debug-when-compiling-for-unity-editor"></a>Eseguire il debug durante la compilazione per l'editor di Unity

Modificare il *tipo di compilazione* della soluzione Unity in **Debug**. Quando si esegue il test di ARR nell'editor di Unity, il parametro `UNITY_EDITOR` definito è disponibile solo nelle compilazioni di debug. Tenere presente che questo comportamento non è correlato al tipo di compilazione usato per le [applicazioni distribuite](../quickstarts/deploy-to-hololens.md), in cui è necessario preferire le compilazioni "Release".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Errori di compilazione quando si compilano esempi di Unity per HoloLens 2

Provando a compilare esempi di Unity (avvio rapido, ShowCaseApp...) per HoloLens 2 sono stati riscontati degli errori spuri. Visual Studio non è in grado di copiare alcuni file sebbene siano presenti. Se si rileva questo problema:
* Rimuovere tutti i file temporanei di Unity dal progetto e riprovare. Ciò significa chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/compilare nuovamente il progetto.
* Assicurarsi che i progetti si trovino in una directory su disco con un percorso ragionevolmente breve, dal momento che il passaggio di copia talvolta riscontra problemi con nomi di file troppo lunghi.
* Se la soluzione non è utile a risolvere il problema, è possibile che MS Sense interferisca con il passaggio di copia. Per configurare un'eccezione, eseguire questo comando del registro dalla riga di comando (sono necessari i diritti di amministratore):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Le compilazioni Arm64 per i progetti Unity hanno esito negativo perché AudioPluginMsHRTF.dll è mancante

Per Arm64 è stato aggiunto al pacchetto `AudioPluginMsHRTF.dll` *Windows Mixed Reality* *(com.unity.xr.windowsmr.metro)* nella versione 3.0.1. Assicurarsi di avere installato la versione 3.0.1 o successiva tramite unity Gestione pacchetti. Dalla barra dei menu di Unity passare a *Window > Gestione pacchetti* e cercare il *pacchetto Windows Mixed Reality.*

## <a name="native-c-based-application-does-not-compile"></a>L'applicazione nativa basata su C++ non viene compilata

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Errore "Libreria non trovata" per l'applicazione UWP o la DLL

All'interno del pacchetto NuGet C++ è presente un `microsoft.azure.remoterendering.Cpp.targets` file che definisce quale versione binaria usare. Per identificare `UWP` , le condizioni nel file verificano la presenza di `ApplicationType == 'Windows Store'` . È quindi necessario assicurarsi che questo tipo sia impostato nel progetto. Ciò dovrebbe verificarsi quando si crea un'applicazione UWP o una DLL Visual Studio creazione guidata del progetto.

## <a name="unstable-holograms"></a>Ologrammi instabili

Se gli oggetti di cui è stato eseguito il rendering sembrano sportarsi con i movimenti della testa, potrebbe trattarsi di problemi con la *Riproiezione con ritardo della fase*. Per istruzioni su come affrontare una situazione di questo tipo, fare riferimento alla sezione in [Riproiezione con ritardo della fase](../overview/features/late-stage-reprojection.md).

Gli ologrammi instabili (oscillazione, distorsione, tremolio o salto degli ologrammi) possono derivare anche da una connettività di rete insufficiente, in particolare da una larghezza di banda di rete insufficiente o una latenza troppo elevata. Un buon indicatore per la qualità della connessione di rete è il valore sulle [statistiche delle prestazioni](../overview/features/performance-queries.md)`ServiceStatistics.VideoFramesReused`. I fotogrammi riusati indicano situazioni in cui è necessario riusare un fotogramma video precedente sul lato client perché non è disponibile alcun nuovo fotogramma video, ad esempio a causa di una perdita di pacchetti o di variazioni nella latenza di rete. Se il valore `ServiceStatistics.VideoFramesReused` è spesso superiore a 0, significa che si è riscontrato un problema di rete.

Un altro valore da esaminare è `ServiceStatistics.LatencyPoseToReceiveAvg`. Deve essere sempre inferiore a 100 ms. La visualizzazione di valori più elevati potrebbe indicare che si è connessi a un data center troppo lontano.

Per un elenco delle possibili mitigazioni, vedere le [Linee guida per la connettività di rete](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-fighting

Mentre ARR offre [la funzionalità di mitigazione z-fighting,](../overview/features/z-fighting-mitigation.md)z-fighting può comunque essere visualizzato nella scena. Questa guida ha lo scopo di risolvere questi problemi rimanenti.

### <a name="recommended-steps"></a>Procedure consigliate

Usare il flusso di lavoro seguente per attenuare z-fighting:

1. Testare la scena con le impostazioni predefinite di ARR (z-fighting mitigation on)

1. Disabilitare la mitigazione z-fighting tramite la relativa [API](../overview/features/z-fighting-mitigation.md) 

1. Cambiare il piano vicino e lontano della fotocamera in un intervallo più vicino

1. Risolvere i problemi relativi alla scena tramite la sezione successiva

### <a name="investigating-remaining-z-fighting"></a>Analisi dello z-fighting rimanente

Se i passaggi precedenti sono stati esauriti e il rimanente z-fighting è inammissibile, è necessario indagare sulla causa sottostante dello z-fighting. Come indicato nella pagina della funzionalità di mitigazione [z-fighting,](../overview/features/z-fighting-mitigation.md)esistono due motivi principali per la lotta con z: la perdita di precisione della profondità all'estremità più lontana dell'intervallo di profondità e le superfici che si intersecano mentre sono coplanari. La perdita di precisione della profondità è un'eventualità matematica e può essere mitigata solo seguendo il passaggio 3 precedente. Le superfici copianere indicano un difetto dell'asset di origine e sono meglio fisse nei dati di origine.

ARR include una funzionalità per determinare se le superfici possono essere evidenziate con z-combat: [Checkerboard che evidenzia](../overview/features/z-fighting-mitigation.md). È anche possibile determinare visivamente cosa causa lo z-fighting. La prima animazione seguente mostra un esempio di perdita di precisione della profondità in distanza e la seconda mostra un esempio di superfici quasi coplanari:

![L'animazione mostra un esempio di perdita di precisione della profondità nella distanza.](./media/depth-precision-z-fighting.gif)  ![L'animazione mostra un esempio di superfici quasi coplanari.](./media/coplanar-z-fighting.gif)

Confrontare questi esempi con lo z-fighting per determinare la causa o, facoltativamente, seguire questo flusso di lavoro procedura dettagliata:

1. Posizionare la fotocamera sopra le superfici di attacco z per osservare direttamente la superficie.
1. Spostare lentamente la fotocamera all'indietro, lontano dalle superfici.
1. Se lo z-fighting è sempre visibile, le superfici sono perfettamente coplanari. 
1. Se lo z-fighting è visibile nella maggior parte dei casi, le superfici sono quasi coplanari.
1. Se lo z-fighting è visibile solo da lontano, la causa è la mancanza di precisione della profondità.

Le superfici copianere possono avere diverse cause:

* Un oggetto è stato duplicato dall'applicazione di esportazione a causa di un errore o di approcci diversi del flusso di lavoro.

    Verificare questi problemi con il supporto dell'applicazione e dell'applicazione corrispondente.

* Le superfici vengono duplicate e capovolte per apparire su due lati nei renderer che usano l'culling front-face o back-face.

    L'importazione [tramite la conversione](../how-tos/conversion/model-conversion.md) del modello determina il lato principale del modello. Come impostazione predefinita si presuppone la doppia sidedness. Il rendering della superficie verrà eseguito come una superficie sottile con illuminazione fisicamente corretta da entrambi i lati. Il lato singolo può essere implicito da flag nell'asset di origine o forzato in modo esplicito durante la [conversione del modello.](../how-tos/conversion/model-conversion.md) Inoltre, ma facoltativamente, la [modalità a lato singolo](../overview/features/single-sided-rendering.md) può essere impostata su "normale".

* Gli oggetti si intersecano negli asset di origine.

     Anche gli oggetti trasformati in modo che alcune superfici si sovrappongano creino z-fighting. Anche la trasformazione di parti dell'albero della scena nella scena importata in ARR può creare questo problema.

* Le superfici vengono appositamente scritte per il tocco, ad esempio decalcomanie o testo sulle pareti.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Artefatti grafici che usano il rendering stereo a più passi nelle app C++ native

In alcuni casi, le app C++ native personalizzate che usano una modalità di rendering stereo a più passaggi per il contenuto locale (rendering a sinistra e a destra in passaggi separati) dopo la chiamata a [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) possono attivare un bug del driver. Il bug causa anomalie di rasterizzazione non deterministiche, causando la scomparsa casuale di singoli triangoli o parti di triangoli del contenuto locale. Per motivi di prestazioni, è comunque consigliabile eseguire il rendering del contenuto locale con una tecnica di rendering stereo a singolo passaggio più moderna, ad esempio usando **SV_RenderTargetArrayIndex**.

## <a name="conversion-file-download-errors"></a>Errori di download del file di conversione

Il servizio di conversione potrebbe riscontrare errori durante il download dei file dall'archivio BLOB a causa dei limiti di lunghezza del percorso imposti da Windows e dal servizio. I percorsi e i nomi file nell'archivio BLOB non devono superare i 178 caratteri. Ad esempio, dato `blobPrefix` un di cui è di `models/Assets` 13 caratteri:

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Il servizio di conversione scarica tutti i file specificati in `blobPrefix` , non solo i file usati nella conversione. I file o le cartelle che causano problemi possono essere meno evidenti in questi casi, quindi è importante controllare tutti gli elementi contenuti nell'account di archiviazione in `blobPrefix` . Per informazioni su ciò che viene scaricato, vedere gli input di esempio seguenti.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Passaggi successivi

* [Requisiti di sistema](../overview/system-requirements.md)
* [Requisiti di rete](../reference/network-requirements.md)