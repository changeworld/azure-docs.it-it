---
title: Offuscare visi nell'API di servizi multimediali di Azure V3 | Microsoft Docs
description: Servizi multimediali di Azure V3 fornisce un set di impostazioni per il rilevamento e la modifica del viso che consente di inviare un file video, rilevare visi e applicarne la sfocatura in un singolo passaggio combinato oppure tramite un'operazione a due fasi che consente la modifica. Questo articolo illustra come offuscare i visi con il set di impostazioni del rilevamento viso nell'API V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630492"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>Offuscare facce con il set di impostazioni del rilevamento viso

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

L'API di servizi multimediali di Azure v3 include un set di impostazioni per il rilevamento dei visi che offre scalabilità e rilevamento del volto scalabile (sfocatura) nel cloud. L'offuscamento dei volti consente di modificare un video per sfocare i volti di persone selezionate. Può essere opportuno usare tale servizio in scenari di pubblica sicurezza e notizie giornalistiche. Pochi minuti di metraggio che contengono più visi possono richiedere ore per offuscare manualmente, ma con questo set di impostazioni il processo di redazione dei volti richiederà solo pochi semplici passaggi.

Questo articolo fornisce informazioni dettagliate sul **set di impostazioni del rilevamento del viso** e Mostra come usarlo con Azure Media Services SDK per .NET.

## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza
 
Come promemoria importante, è necessario rispettare tutte le leggi applicabili nell'uso dell'analisi in servizi multimediali di Azure. Non è necessario usare servizi multimediali di Azure o altri servizi di Azure in modo da violare i diritti di altri utenti. Prima di caricare i video, inclusi i dati biometrici, nel servizio servizi multimediali di Azure per l'elaborazione e l'archiviazione, è necessario disporre di tutti i diritti appropriati, inclusi tutti i consensi appropriati, dagli utenti del video. Per informazioni su conformità, privacy e sicurezza in servizi multimediali di Azure, le [condizioni per servizi cognitivi](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)di Azure. Per gli obblighi sulla privacy e la gestione dei dati di Microsoft, consultare l' [informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement)di Microsoft, le condizioni per i [servizi online](https://www.microsoft.com/licensing/product-licensing/products) (OST) e l'Addendum per l' [elaborazione dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Altre informazioni sulla privacy, tra cui la conservazione dei dati, l'eliminazione o la distruzione, sono disponibili nella OST e [qui](../video-indexer/faq.md). Con servizi multimediali di Azure, l'utente accetta di essere vincolato dalle condizioni di servizi cognitivi, da OST, DPA e dall'Informativa sulla privacy


## <a name="face-redaction-modes"></a>Modalità per l'offuscamento dei volti

La funzionalità di offuscamento dei volti rileva i volti in ogni fotogramma del video e monitora l'oggetto volto avanti e indietro nel tempo in modo da consentire la sfocatura della stessa persona anche da altre angolazioni. Il processo di redazione automatizzato è complesso e non sempre si offusca ogni superficie garantita del 100%. Per questo motivo, il set di impostazioni può essere usato come modalità a due passaggi per migliorare la qualità e l'accuratezza della sfocatura tramite una fase di modifica prima di inviare il file per il passaggio di sfocatura finale. 

Oltre a una modalità **combinata** completamente automatica, il flusso di lavoro in due passaggi consente di scegliere i visi per i quali si vuole sfocare o non la sfocatura tramite un elenco di ID Face. Per apportare regolazioni arbitrarie per fotogramma, il set di impostazioni usa un file di metadati in formato JSON come input per il secondo passaggio. Il flusso di lavoro è suddiviso nelle modalità **analisi** e **offuscamento**.

È anche possibile combinare facilmente le due modalità in un unico passaggio che esegue entrambe le attività in un unico processo. Questa modalità viene chiamata **combinata**.  In questo articolo, il codice di esempio illustra come usare la modalità **combinata** di passaggio singolo semplificato in un file di origine di esempio.

### <a name="combined-mode"></a>Modalità combinata

Viene generato un file video MP4 redatto in un unico passaggio senza che sia necessario modificare manualmente il file JSON. L'output nella cartella asset per il processo sarà costituito da un unico file MP4 che contiene facce sfocate che usano l'effetto di sfocatura selezionato. Usare la proprietà Resolution impostata su **SourceResolution** per ottenere risultati ottimali per la redazione.

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |"ignite-sample.mp4" |Video in formato WMV, MOV o MP4 |
| Configurazione set di impostazioni |Configurazione del rilevamento viso | **modalità**: FaceRedactorMode. Combined,  **blurType**: blurType. Med, **risoluzione**: AnalysisResolution. SourceResolution |
| Asset di output |"ignite-redacted.mp4 |Video con effetto sfocatura applicato ai visi |

### <a name="analyze-mode"></a>Modalità analisi

La sessione di **analisi** del flusso di lavoro in due passaggi accetta un input video e produce un file JSON con un elenco delle posizioni del viso, dell'ID del viso e delle immagini jpg di ogni volto rilevato. 

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |"ignite-sample.mp4" |Video in formato WMV, MPV o MP4 |
| Configurazione set di impostazioni |Configurazione del rilevamento viso |**modalità**: FaceRedactorMode. Analyze, **risoluzione**: AnalysisResolution. SourceResolution|
| Asset di output |ignite-sample_annotations.js |Dati di annotazione delle posizioni dei volti in formato JSON, modificabili dall'utente per modificare i rettangoli di selezione della sfocatura. Vedere l'esempio di seguito. |
| Asset di output |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |File jpg ritagliato di ogni volto rilevato, in cui il numero indica l'ID etichetta del volto |

#### <a name="output-example"></a>Esempio di output

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Modalità offuscare (Blur)

Il secondo passaggio del flusso di lavoro usa un numero superiore di input che devono essere combinati in un singolo asset.

Gli input includono un elenco di ID da sfocare, il video originale e il file JSON delle annotazioni. Questa modalità usa le annotazioni per applicare la sfocatura nel video di input.

L'output del passaggio dell'analisi non include il video originale. Il video deve essere caricato nell'asset di input per l'attività della modalità offuscamento ed essere selezionato come file primario.

| Fase | File Name | Note |
| --- | --- | --- |
| Asset di input |"ignite-sample.mp4" |Video in formato WMV, MPV o MP4. Stesso video del passaggio 1. |
| Asset di input |"ignite-sample_annotations.jssu" |file di metadati delle annotazioni dalla fase 1, con modifiche facoltative se si desidera modificare i visi sfocati. Questo deve essere modificato in un'applicazione esterna, un codice o un editor di testo. |
| Asset di input | "ignite-sample_IDList.txt" (facoltativo) | Elenco facoltativo separato da caratteri di nuova riga di ID volto da offuscare. Se viene lasciato vuoto, a tutti i visi nell'origine sarà applicata la sfocatura. È possibile usare l'elenco per scegliere in modo selettivo di non offuscare i visi specifici. |
| Set di impostazioni del rilevamento viso  |Configurazione predefinita  | **modalità**: FaceRedactorMode. offuscare, **blurType**: blurType. Med |
| Asset di output |"ignite-sample-redacted.mp4" |Video con sfocatura applicata in base alle annotazioni. |

#### <a name="example-output"></a>Output di esempio

Questo output viene ottenuto da un elenco di ID con un ID selezionato.

Esempio foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Tipi di sfocature

In modalità **combinata** o **offuscare** sono disponibili cinque diverse modalità di sfocatura tra cui è possibile scegliere tramite la configurazione di input JSON: **low**, **Med**, **High**, **Box** e **Black**. Per impostazione predefinita, viene usata **Med**.

Di seguito sono riportati alcuni esempi dei tipi di sfocature.

### <a name="example-settings-for-face-detector-preset"></a>Impostazioni di esempio per il set di impostazioni del rilevamento viso
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>Basso

![Esempio di impostazione di sfocatura a bassa risoluzione.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![Esempio di impostazione sfocatura media risoluzione.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Alto

![Esempio di impostazione di sfocatura ad alta risoluzione.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Modalità Box da utilizzare per il debug dell'output.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Black

![La modalità Black Box copre tutti i visi con le caselle nere.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementi del file di output JSON

Il processore di contenuti multimediali per l'offuscamento offre funzionalità di rilevamento della posizione e monitoraggio dei volti ad alta precisione che possono rilevare fino a 64 volti umani in un fotogramma video. Le riprese anteriori producono risultati ottimali, mentre profili e volti di piccole dimensioni (inferiori o uguali a 24x24 pixel) presentano alcune problematiche.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Codice di esempio .NET

Nel programma seguente viene illustrato come utilizzare la modalità di redazione a passaggio singolo **combinata** :

- Creare un asset e caricare un file multimediale nell'asset.
- Configurare il set di impostazioni del rilevamento del viso che usa le impostazioni mode e blurType.
- Crea una nuova trasformazione usando il set di impostazioni del rilevamento viso
- Scaricare il file video di output redatto.

## <a name="download-and-configure-the-sample"></a>Scaricare e configurare l'esempio

Clonare nel computer un repository GitHub contenente l'esempio .NET usando il comando seguente:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

L'esempio si trova nella cartella [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) Aprire il file [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) nel progetto scaricato. Sostituire i valori con le credenziali ottenute durante l'[accesso alle API](./access-api-howto.md).

**Facoltativamente** , è possibile copiare il file con estensione **[ENV di esempio](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** nella radice del repository e compilare i dettagli in questa posizione e rinominare il file in **. env** (si noti il punto in primo piano) in modo che possa essere usato in tutti i progetti di esempio nel repository.  In questo modo si elimina la necessità di disporre di un appsettings.jspopolato su file in ogni esempio e protegge anche la verifica delle impostazioni nei repository clonati dell'hub git.

#### <a name="examples"></a>Esempio

Questo codice Mostra come configurare **FaceDetectorPreset** per una sfocatura in modalità **combinata** .

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Questo esempio di codice illustra come il set di impostazioni viene passato in un oggetto Transform durante la creazione. Dopo aver creato la trasformazione, i processi possono essere inviati direttamente al processo.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

