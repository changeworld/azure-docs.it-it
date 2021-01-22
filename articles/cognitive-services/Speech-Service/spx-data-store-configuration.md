---
title: Opzioni di configurazione dell'interfaccia della riga di comando di Voce - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare e gestire i file di configurazione da usare con l'interfaccia della riga di comando di Voce di Azure.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540233"
---
# <a name="speech-cli-configuration-options"></a>Opzioni di configurazione dell'interfaccia della riga di comando di Voce

Il comportamento dell'interfaccia della riga di comando di Voce può essere basato sulle impostazioni dei file di configurazione, a cui è possibile fare riferimento usando un simbolo `@`. L'interfaccia della riga di comando di Voce salva una nuova impostazione in una nuova sottodirectory `./spx/data` che viene creata nella directory di lavoro corrente per l'interfaccia della riga di comando di Voce. Per la ricerca di un valore di configurazione, l'interfaccia della riga di comando di Voce cerca nella directory di lavoro corrente, quindi nell'archivio dati nella sottodirectory `./spx/data` e infine in altri archivi dati, tra cui un archivio dati finale di sola lettura disponibile nel binario `spx`. 

Nell'avvio rapido sull'interfaccia della riga di comando di Voce è stato usato l'archivio dati per salvare i valori `@key` e `@region`, di conseguenza non è stato necessario specificarli con ogni comando `spx`. Tenere presente che è possibile usare i file di configurazione per archiviare le proprie impostazioni di configurazione o persino per passare URL o altri contenuti dinamici generati in fase di esecuzione.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Creare e gestire file di configurazione nell'archivio dati

Questa sezione illustra come usare un file di configurazione nell'archivio dati locale per archiviare e recuperare le impostazioni dei comandi usando `spx config` e archiviare l'output dell'interfaccia della riga di comando del servizio Voce usando l'opzione `--output`.

Nell'esempio seguente viene cancellato il file di configurazione `@my.defaults`, vengono aggiunte coppie chiave-valore per **chiave** e **area** nel file e viene usata la configurazione in una chiamata a `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

È anche possibile eseguire la scrittura di contenuto dinamico in un file di configurazione. Il comando seguente ad esempio crea un modello di conversione voce/testo personalizzato e archivia l'URL del nuovo modello in un file di configurazione. Il comando successivo attende fino a quando il modello in tale URL non è pronto per l'uso prima di restituire il risultato.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

L'esempio seguente esegue la scrittura di due URL nel file di configurazione `@my.datasets.txt`. In questo scenario, `--output` può includere una parola chiave **add** facoltativa per creare un file di configurazione o accodarlo a quello esistente.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Per informazioni dettagliate sui file di archivio dati, incluso l'utilizzo dei file di configurazione predefiniti (`@spx.default`, `@default.config` e `@*.default.config` per le impostazioni predefinite specifiche dei comandi), immettere questo comando:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Passaggi successivi 

* [Operazioni in batch con l'interfaccia della riga di comando di Voce](./spx-batch-operations.md)