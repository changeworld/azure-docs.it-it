---
title: Download di Azure Kinect Body Tracking SDK
description: Informazioni su come scaricare ogni versione di Azure Kinect Sensor SDK in Windows o Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, download aggiornamento, più recente, disponibile, installazione, corpo, monitoraggio
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654493"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Scaricare Azure Kinect Body Tracking SDK

Questo documento contiene i collegamenti per installare ogni versione di Azure Kinect Body Tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Contenuto dell'SDK del body tracking di Azure Kinect

- Intestazioni e librerie per la compilazione di un'applicazione di rilevamento del corpo con il DK di Azure.
- DLL ridistribuibili necessarie per il body tracking delle applicazioni con Azure Kinect DK.
- Applicazioni di rilevamento del corpo di esempio.

## <a name="windows-download-links"></a>Collegamenti per il download di Windows

Versione       | Scarica
--------------|----------
1.1.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1) MSI
1.0.0 | [](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0) MSI

## <a name="linux-installation-instructions"></a>Istruzioni per l'installazione in Linux

Attualmente, l'unica distribuzione supportata è Ubuntu 18,04 e 20,04. Per richiedere il supporto per altre distribuzioni, vedere [questa pagina](https://aka.ms/azurekinectfeedback).

Prima di tutto, è necessario configurare il [repository pacchetti di Microsoft](https://packages.microsoft.com/), seguendo le istruzioni disponibili [qui](/windows-server/administration/linux-package-repository-for-microsoft-software).

Il pacchetto `libk4abt<major>.<minor>-dev` contiene le intestazioni e i file CMake da compilare con `libk4abt`.
Il `libk4abt<major>.<minor>` pacchetto contiene gli oggetti condivisi necessari per eseguire i file eseguibili che dipendono da e `libk4abt` il Visualizzatore di esempio.

Per le esercitazioni di base è richiesto il pacchetto `libk4abt<major>.<minor>-dev`. Per installarlo, eseguire

`sudo apt install libk4abt<major>.<minor>-dev`

Se il comando riesce, l'SDK è pronto per l'uso.

> [!NOTE]
> Quando si installa l'SDK, ricordare il percorso in cui si esegue l'installazione, Ad esempio, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Gli esempi a cui viene fatto riferimento negli articoli di questo percorso sono disponibili.
> Gli esempi di rilevamento del corpo si trovano nella cartella [body-tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) nel repository Azure-Kinect-Samples. Gli esempi a cui viene fatto riferimento negli articoli sono disponibili qui.

## <a name="change-log"></a>Registro delle modifiche

### <a name="v110"></a>v 1.1.0
* Funzionalità Aggiungere il supporto per DirectML (solo Windows) e l'esecuzione TensorRT del modello di stima della posa. Vedere le domande frequenti sui nuovi ambienti di esecuzione.
* Funzionalità Aggiungi `model_path` a `k4abt_tracker_configuration_t` struct. Consente agli utenti di specificare il percorso per il modello di stima della posa. Per impostazione predefinita `dnn_model_2_0_op11.onnx` , il modello di stima della posizione standard si trova nella directory corrente.
* Funzionalità Includere il `dnn_model_2_0_lite_op11.onnx` modello di stima Lite post. Questo modello comporta un aumento delle prestazioni di ~ 2x per una riduzione della precisione del 5%.
* Funzionalità Gli esempi verificati compilano con Visual Studio 2019 e aggiornano gli esempi per l'uso di questa versione.
* [Modifica di rilievo] Aggiornamento a ONNX Runtime 1,6 con supporto per CPU, CUDA 11,1, DirectML (solo Windows) e ambienti di esecuzione TensorRT 7.2.1. Richiede l'aggiornamento del driver NVIDIA a R455 o superiore.
* [Modifica di rilievo] Nessuna installazione di NuGet.
* [Correzione di bug] Aggiungere il supporto per il [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) GPU NVIDIA RTX serie 30xx
* [Correzione di bug] Aggiungere il supporto per il [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) AMD e Intel Integrated GPU (solo Windows)
* [Correzione di bug] Aggiornare al [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) CUDA 11,1
* [Correzione di bug] [Collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248) aggiornamento a Sensor SDK 1.4.1

### <a name="v101"></a>v 1.0.1
* [Correzione di bug] Correzione del problema per cui l'SDK si arresta in modo anomalo se si carica onnxruntime.dll dal percorso in Windows build 19025 o versione successiva: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Funzionalità Aggiungere il wrapper C# al programma di installazione MSI.
* [Correzione di bug] Correzione del problema per cui non è possibile rilevare correttamente la rotazione Head: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Correzione di bug] Correzione del problema per cui l'utilizzo della CPU passa al 100% nel computer Linux: [collegamento](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Campioni Aggiungere due campioni al repository di esempio. Nell'esempio 1 viene illustrato come trasformare i risultati del rilevamento del corpo dallo spazio di profondità al [collegamento](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)dello spazio dei colori; Nell'esempio 2 viene illustrato come rilevare il [collegamento](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) del piano piano

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurare il rilevamento del corpo Kinect di Azure](body-sdk-setup.md)