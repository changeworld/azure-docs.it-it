---
title: Avvio rapido dell'interfaccia della riga di comando-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Inizia a usare l'interfaccia della riga di comando di Azure. È possibile interagire con servizi di sintesi vocale, ad esempio sintesi vocale, sintesi vocale e traduzione vocale senza scrivere codice.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 4a6c7b36665c7a38534ce8e470bc8b327c274d95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "99095182"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Introduzione all'interfaccia della riga di comando di Azure Speech

In questo articolo si apprenderà come usare l'interfaccia della riga di comando vocale, un'interfaccia della riga di comando, per accedere ai servizi di riconoscimento vocale, ad esempio sintesi vocale, sintesi vocale e traduzione vocale senza scrivere codice. L'interfaccia della riga di comando per Voce è pronta per la produzione e può essere usata per automatizzare flussi di lavoro semplici nel servizio Voce, usando `.bat` o script della shell.

Questo articolo presuppone che l'utente abbia familiarità con il prompt dei comandi, il terminale o PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Uso di base

Questa sezione illustra alcuni comandi SPX di base spesso utili per eseguire i primi test e la prima sperimentazione. Per iniziare, visualizzare la Guida incorporata nello strumento eseguendo il comando seguente.

```console
spx
```

È possibile cercare gli argomenti della Guida per parola chiave. Ad esempio, immettere il comando seguente per visualizzare un elenco di esempi di utilizzo dell'interfaccia della riga di comando di Voce:

```console
spx help find --topics "examples"
```

Immettere il comando seguente per visualizzare le opzioni del comando recognize:

```console
spx help recognize
```

Comandi aggiuntivi della Guida elencati nella colonna a destra. È possibile immettere questi comandi per ottenere informazioni dettagliate sulla guida sui sottocomandi.

## <a name="speech-to-text-speech-recognition"></a>Riconoscimento vocale

Si userà l'interfaccia della riga di comando vocale per convertire la voce in testo (riconoscimento vocale) usando il microfono predefinito del sistema. Dopo l'immissione del comando, SPX inizierà a essere in ascolto dell'audio sul dispositivo di input attivo corrente e si arresterà quando si preme **invio**. Le parole registrate vengono quindi riconosciute e convertite in testo nell'output della console.

>[!IMPORTANT]
> Se si usa un contenitore Docker, `--microphone` non funzionerà.

Eseguire questo comando:

```console
spx recognize --microphone
```

Con l'interfaccia della riga di comando vocale, è anche possibile riconoscere il riconoscimento vocale da un file audio.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Se si esegue il riconoscimento vocale da un file audio in un contenitore Docker, assicurarsi che tale file si trovi nella directory montata nel passaggio precedente.

Se ci si blocca o si desidera ottenere altre informazioni sulle opzioni di riconoscimento dell'interfaccia della riga di comando vocale, digitare quanto segue:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Sintesi vocale

L'esecuzione del comando seguente prenderà il testo come input e restituirà la voce sintetizzata al dispositivo di output attivo corrente (ad esempio, gli speaker del computer).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

È anche possibile salvare l'output sintetizzato in un file. In questo esempio verrà creato un file denominato `my-sample.wav` nella directory in cui viene eseguito il comando.

```console
spx synthesize --text "We hope that you enjoy using the Speech CLI." --audio output my-sample.wav
```

Questi esempi presuppongono che si stiano testando in inglese. Tuttavia, la sintesi vocale è supportata in diversi linguaggi. È possibile visualizzare un elenco completo di voci con questo comando o visitando la pagina del [supporto](./language-support.md)per le lingue.

```console
spx synthesize --voices
```

Ecco come usare una delle voci individuate.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Se ci si blocca o si desidera ottenere altre informazioni sulle opzioni di sintesi dell'interfaccia della riga di comando vocale, digitare quanto segue:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Traduzione da riconoscimento vocale a testo

Con l'interfaccia della riga di comando vocale, è anche possibile eseguire la traduzione vocale. Eseguire questo comando per acquisire l'audio dal microfono predefinito e restituire la traduzione come testo. Tenere presente che è necessario fornire la `source` `target` lingua e con il `translate` comando.

```console
spx translate --microphone --source en-US --target ru-RU
```

Quando si esegue la conversione in più lingue, è necessario separare i codici della lingua con `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Se si desidera salvare l'output della traduzione, utilizzare il `--output` flag. In questo esempio verrà letto anche da un file.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Per un elenco di tutte le lingue supportate con i rispettivi codici delle impostazioni locali, vedere l'[articolo relativo alla lingua e alle impostazioni locali](language-support.md).

Se ci si blocca o si desidera ottenere altre informazioni sulle opzioni di traduzione dell'interfaccia della riga di comando vocale, digitare:

```console
spx help translate
```

## <a name="next-steps"></a>Passaggi successivi

* [Opzioni di configurazione dell'interfaccia della riga di comando di Voce](./spx-data-store-configuration.md)
* [Operazioni in batch con l'interfaccia della riga di comando di Voce](./spx-batch-operations.md)
