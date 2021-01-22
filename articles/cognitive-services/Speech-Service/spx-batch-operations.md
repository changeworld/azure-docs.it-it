---
title: Operazioni in batch nell'interfaccia della riga di comando di Voce - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire operazioni in batch di riconoscimento vocale e sintesi vocale con l'interfaccia della riga di comando di Voce.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540230"
---
# <a name="speech-cli-batch-operations"></a>Operazioni in batch nell'interfaccia della riga di comando di Voce

Per operazioni in batch si intendono le attività comuni eseguite quando si usano i servizi Voce di Azure. Questo articolo illustra come eseguire operazioni in batch di riconoscimento vocale e sintesi vocale con l'interfaccia della riga di comando di Voce. In particolare, verrà illustrato come:

* Eseguire il riconoscimento vocale in batch in una directory di file audio
* Eseguire la sintesi vocale in batch tramite l'iterazione su un file `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Riconoscimento vocale in batch

Il servizio Voce viene spesso usato per il riconoscimento vocale in file audio. Questo esempio illustra come eseguire l'iterazione su una directory usando l'interfaccia della riga di comando di Voce per acquisire l'output di riconoscimento per ogni file `.wav`. Per puntare alla directory in cui sono archiviati i file audio, si usa il flag `--files`, mentre per indicare all'interfaccia della riga di comando di Voce di eseguire il riconoscimento su ogni file con estensione `.wav`, si usa il carattere jolly `*.wav`. L'output relativo a ogni file di riconoscimento viene scritto come valore separato da tabulazioni in `speech_output.tsv`.

> [!NOTE]
> L'argomento `--threads` può essere usato anche nella sezione successiva per i comandi `spx synthesize` e i thread disponibili dipendono dalla CPU e dalla percentuale di carico corrente.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Di seguito è riportato un esempio della struttura dei file di output.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Sintesi vocale in batch

Il modo più semplice per eseguire la sintesi vocale in batch consiste nel creare un nuovo file `.tsv` (con valori delimitati da tabulazioni) e nell'usare il comando `--foreach` nell'interfaccia della riga di comando di Voce. È possibile creare un file `.tsv` usando l'editor di testo preferito. Per questo esempio verrà creato il file `text_synthesis.tsv`:

>[!IMPORTANT]
> Quando si copia il contenuto di questo file di testo, assicurarsi che il percorso del file sia separato dal testo da un carattere di **tabulazione** e non da spazi. In alcuni casi, quando si copia il contenuto di questo esempio, le tabulazioni vengono convertite in spazi, impedendo la corretta esecuzione del comando `spx`.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Eseguire quindi un comando per puntare a `text_synthesis.tsv`, eseguire la sintesi su ogni campo `text` e scrivere il risultato nel percorso `audio.output` corrispondente come file `.wav`.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Questo comando equivale a eseguire `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **per ogni** record nel file `.tsv`.

Un paio di aspetti da considerare:

* Le intestazioni di colonna `audio.output` e `text` corrispondono rispettivamente agli argomenti della riga di comando `--audio output` e `--text`. Gli argomenti della riga di comando in più parti, come `--audio output`, devono essere formattati nel file senza alcuno spazio, alcun trattino iniziale e alcun punto di separazione delle stringhe, ad esempio `audio.output`. Qualsiasi altro argomento della riga di comando esistente può essere aggiunto al file come colonna aggiuntiva tramite questo modello.
* Quando il file viene formattato in questo modo, non è necessario passare argomenti aggiuntivi a `--foreach`.
* Verificare di separare ogni valore nel file `.tsv` con una **tabulazione**.

Può tuttavia essere presente un file `.tsv`, come nell'esempio seguente, che include intestazioni di colonna **non corrispondenti** agli argomenti della riga di comando:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

In tal caso è possibile eseguire l'override di questi nomi di campo negli argomenti corretti usando la sintassi seguente nella chiamata a `--foreach`. Si tratta della stessa chiamata descritta in precedenza.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'interfaccia della riga di comando per Voce](./spx-overview.md)
* [Guida di avvio rapido sull'interfaccia della riga di comando di Voce](./spx-basics.md)
