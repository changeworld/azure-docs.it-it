---
title: Uso dell'editor di Azure Cloud Shell | Microsoft Docs
description: Panoramica su come usare l'editor di Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832336"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Uso dell'editor di Azure Cloud Shell

Azure Cloud Shell include un editor di file integrato, creato in base allo strumento open source [Monaco Editor](https://github.com/Microsoft/monaco-editor). L'editor Cloud Shell supporta funzionalità quali l'evidenziazione del linguaggio, il riquadro comandi e uno strumento per l'esplorazione dei file.

![Editor di Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Apertura dell'editor

Per la creazione e la modifica di file semplici, avviare l'editor eseguendo `code .` nel terminale di Cloud Shell. Questa azione consente di aprire l'editor con la directory di lavoro attiva impostata nel terminale.

Per aprire direttamente un file per la modifica rapida, eseguire `code <filename>` per aprire l'editor senza lo strumento per l'esplorazione dei file.

Per aprire l'editor tramite un pulsante dell'interfaccia utente, fare clic sull'icona dell'editor `{}` nella barra degli strumenti. L'editor verrà aperto e nello strumento di esplorazione dei file sarà attiva per impostazione predefinita la directory `/home/<user>`.

## <a name="closing-the-editor"></a>Chiusura dell'editor

Per chiudere l'editor, aprire il pannello delle azioni `...` in alto a destra e scegliere `Close editor`.

![Chiudere l'editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Riquadro comandi

Per avviare il riquadro comandi, usare il tasto `F1` quando l'editor si trova in uno stato attivo. Il riquadro comandi può essere aperto anche tramite il pannello delle azioni.

![Riquadro comandi](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Collaborazione al progetto di Monaco Editor

Il supporto per l'evidenziazione del linguaggio nell'editor di Cloud Shell è consentito dalla funzionalità upstream di [Monaco Editor](https://github.com/Microsoft/monaco-editor) relativa all'uso delle definizioni di sintassi Monarch. Per informazioni sulle opportunità di collaborazione, leggere la [guida per i collaboratori di Monaco](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Passaggi successivi

- [Prova la Guida introduttiva per bash in Cloud Shell](quickstart.md)
- [Visualizzare l'elenco completo degli strumenti di Cloud Shell integrati](features.md)
