---
title: Posizione in cui salvare & file dell'esperimento
titleSuffix: Azure Machine Learning
description: Informazioni su dove salvare i file di input e di output per evitare errori di limitazione dell'archiviazione e la latenza dell'esperimento.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387888"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Dove salvare e scrivere i file per Azure Machine Learning esperimenti


In questo articolo si apprenderà dove salvare i file di input e dove scrivere i file di output dagli esperimenti per evitare errori di limite di archiviazione e la latenza dell'esperimento.

Quando si avviano esecuzioni di training in [una destinazione di calcolo,](concept-compute-target.md)sono isolate dagli ambienti esterni. Lo scopo di questa progettazione è garantire la riproducibilità e la portabilità dell'esperimento. Se si esegue lo stesso script due volte, nella stessa destinazione di calcolo o in un'altra destinazione di calcolo, si ricevono gli stessi risultati. Con questa progettazione, è possibile considerare le destinazioni di calcolo come risorse di calcolo senza stato, ognuna senza affinità con i processi in esecuzione al termine.

## <a name="where-to-save-input-files"></a>Posizione in cui salvare i file di input

Prima di poter avviare un esperimento in una destinazione di calcolo o nel computer locale, è necessario assicurarsi che i file necessari siano disponibili per tale destinazione di calcolo, ad esempio i file di dipendenza e i file di dati che il codice deve eseguire.

Azure Machine Learning gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si vuole caricare, usare un [file con](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) estensione ignore o non includerli nella directory di origine. Accedere invece ai dati usando un [archivio dati](/python/api/azureml-core/azureml.data).

Il limite di archiviazione di snapshot dell'esperimento è 300 MB e/o 2000 file.

Per questo motivo, è consigliabile:

* **Archiviazione dei file in un set Azure Machine Learning [set di dati](/python/api/azureml-core/azureml.data).** Ciò impedisce problemi di latenza degli esperimenti e offre i vantaggi dell'accesso ai dati da una destinazione di calcolo remota, ovvero l'autenticazione e il montaggio vengono gestiti da Azure Machine Learning. Altre informazioni su come specificare un set di dati come origine dati di input nello script di training [con Eseguire il training con i set di dati](how-to-train-with-datasets.md).

* **Se sono necessari solo un paio** di file di dati e script di dipendenza e non è possibile usare un archivio dati, inserire i file nella stessa directory di cartelle dello script di training. Specificare questa cartella come direttamente nello script di training o `source_directory` nel codice che chiama lo script di training.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limiti di archiviazione degli snapshot dell'esperimento

Per gli esperimenti, Azure Machine Learning automaticamente uno snapshot dell'esperimento del codice in base alla directory suggerita quando si configura l'esecuzione. Il limite totale è di 300 MB e/o 2000 file. Se si supera questo limite, verrà visualizzato l'errore seguente:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Per risolvere questo errore, archiviare i file dell'esperimento in un archivio dati. Se non è possibile usare un archivio dati, la tabella seguente offre possibili soluzioni alternative.

Descrizione &nbsp; dell'esperimento|Soluzione limite di archiviazione
---|---
Non è possibile usare un archivio & meno di 2000 file| Sostituire il limite di dimensioni dello snapshot con <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Questa operazione può richiedere alcuni minuti a seconda del numero e delle dimensioni dei file.
Deve usare una directory di script specifica| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pipeline|Usare una sottodirectory diversa per ogni passaggio
Notebook di Jupyter| Creare un `.amlignore` file o spostare il notebook in una nuova sottodirectory vuota ed eseguire di nuovo il codice.

## <a name="where-to-write-files"></a>Dove scrivere i file

A causa dell'isolamento degli esperimenti di training, le modifiche ai file che si verificano durante le esecuzioni non vengono necessariamente mantenute all'esterno dell'ambiente. Se lo script modifica i file locali per il calcolo, le modifiche non vengono rese persistenti per l'esecuzione successiva dell'esperimento e non vengono propagate automaticamente al computer client. Di conseguenza, le modifiche apportate durante la prima esecuzione dell'esperimento non influiscono e non dovrebbero influire su quelle nel secondo.

Quando si scrivono modifiche, è consigliabile scrivere file nell'archiviazione tramite Azure Machine Learning set di dati con un [oggetto OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Vedere [come creare un oggetto OutputFileDatasetConfig.](how-to-train-with-datasets.md#where-to-write-training-output)

In caso contrario, scrivere i file `./outputs` nella cartella `./logs` e/o .

>[!Important]
> Due cartelle, *output e* *log,* ricevono un trattamento speciale Azure Machine Learning. Durante il training, quando si scrivono file in e cartelle, i file verranno caricati automaticamente nella cronologia di esecuzione, in modo che sia possibile accedervi al termine `./outputs` `./logs` dell'esecuzione.

* **Per l'output, ad** esempio messaggi di stato o risultati di assegnazione dei punteggi, scrivere i file nella cartella, in modo che siano `./outputs` persistenti come elementi nella cronologia di esecuzione. Tenere presente il numero e le dimensioni dei file scritti in questa cartella, poiché la latenza può verificarsi quando il contenuto viene caricato per la cronologia di esecuzione. Se la latenza è un problema, è consigliabile scrivere file in un archivio dati.

* **Per salvare il file scritto come log nella cronologia di esecuzione,** scrivere i file nella `./logs` cartella . I log vengono caricati in tempo reale, quindi questo metodo è adatto per lo streaming di aggiornamenti live da un'esecuzione remota.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni [sull'accesso ai dati dall'archiviazione](how-to-access-data.md).

* Altre informazioni sulla creazione [di destinazioni di calcolo per il training e la distribuzione dei modelli](how-to-create-attach-compute-studio.md)