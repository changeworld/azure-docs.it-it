---
title: Opzioni della riga di comando Azure Storage Explorer | Microsoft Docs
description: Documentazione di Azure Storage Explorer opzioni della riga di comando di avvio
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745410"
---
# <a name="azure-storage-explorer-command-line-options"></a>Opzioni della riga di comando Azure Storage Explorer

Microsoft Azure Storage Explorer dispone di un set di opzioni della riga di comando che è possibile aggiungere all'avvio dell'applicazione. La maggior parte delle opzioni della riga di comando è a scopo di debug o di risoluzione dei problemi.

## <a name="command-line-options"></a>Opzioni della riga di comando
Opzione  | Descrizione
:------- | :-----------
`--debug`/`--prod`  | Avviare l'applicazione in modalità di debug o di produzione. In modalità di debug i dati degli allegati locali verranno archiviati nella risorsa di archiviazione locale dell'applicazione e non verranno crittografati. Le proprietà nascoste verranno visualizzate nel pannello Proprietà per i nodi di risorse selezionati. Il livello di dettaglio del log verrà impostato per stampare i messaggi di debug che rivelano la logica di configurazione interna del Storage Explorer. Il valore predefinito è `--prod`.
`--lang`  | Avviare l'applicazione con una lingua specifica. Ad esempio: `--lang="zh-Hans"`.
`--disable-gpu` | Avviare l'applicazione senza accelerazione GPU.
`--auto-open-dev-tools` | Consentire all'applicazione di aprire la finestra strumenti di sviluppo non appena viene visualizzata la finestra del browser. Questa opzione è utile quando si desidera raggiungere un punto di rottura in una riga del codice di avvio della finestra del browser.
`--verbosity` | Imposta il livello di dettaglio della registrazione Storage Explorer. I livelli di dettaglio supportati sono `debug` ,, `verbose` `info` , `warn` , `error` e `silent` . Ad esempio: `--verbosity=verbose`. Quando è in esecuzione in modalità di produzione, il livello di dettaglio predefinito è `info` . Quando è in esecuzione in modalità di debug, il livello di dettaglio del log sarà sempre `debug` .
`--log-dir` | Impostare la directory per salvare i file di log. Ad esempio: `--log-dir=path_to_a_directory`.

Esempio di avvio Storage Explorer con opzioni della riga di comando personalizzate

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Queste opzioni della riga di comando possono cambiare nelle nuove versioni del Storage Explorer.

## <a name="when-to-use-command-line-options"></a>Quando usare le opzioni della riga di comando

È possibile utilizzare alcune opzioni della riga di comando per personalizzare Storage Explorer. Per le opzioni con impostazioni utente corrispondenti, ad esempio `--lang` . È consigliabile usare le impostazioni utente invece di usare l'opzione della riga di comando. 

Le altre opzioni della riga di comando possono essere utili per il debug e la risoluzione dei problemi. Se si verifica un problema in Storage Explorer, la riproduzione del problema in modalità di debug può essere utile per ottenere informazioni più dettagliate da analizzare.