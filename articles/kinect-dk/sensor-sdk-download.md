---
title: Download di Azure Kinect Sensor SDK
description: Informazioni su come scaricare e installare Azure Kinect Sensor SDK in Windows e Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, download dell'aggiornamento, più recente, disponibile, installazione
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179630"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download di Azure Kinect Sensor SDK

Questa pagina include i collegamenti di download per ogni versione di Azure Kinect Sensor SDK. Il programma di installazione include tutti i file necessari per sviluppare per Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Contenuto di Azure Kinect Sensor SDK

- Intestazioni e librerie per creare un'applicazione con Azure Kinect DK.
- DLL ridistribuibili necessarie per le applicazioni che usano Azure Kinect DK.
- [Visualizzatore di Azure Kinect](azure-kinect-viewer.md).
- [Registratore di Azure Kinect](azure-kinect-recorder.md).
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Istruzioni per l'installazione di Windows

È possibile trovare i dettagli di installazione per le versioni più recenti e precedenti di Azure Kinect Sensor SDK e firmware [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

Il codice di origine è disponibile [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).

> [!NOTE]
> Quando si installa l'SDK, ricordare il percorso in cui si esegue l'installazione, ad esempio"C:\Programmi\Azure Kinect SDK 1.2". In questo percorso sono disponibili gli strumenti a cui si fa riferimento negli articoli.

## <a name="linux-installation-instructions"></a>Istruzioni per l'installazione in Linux

Attualmente, l'unica distribuzione supportata è Ubuntu 18.04. Per richiedere il supporto per altre distribuzioni, vedere [questa pagina](https://aka.ms/azurekinectfeedback).

Prima di tutto, è necessario configurare il [repository pacchetti di Microsoft](https://packages.microsoft.com/), seguendo le istruzioni disponibili [qui](/windows-server/administration/linux-package-repository-for-microsoft-software).

A questo punto, è possibile installare i pacchetti necessari. Il pacchetto `k4a-tools` include il [visualizzatore di Azure Kinect](azure-kinect-viewer.md), il [registratore di Azure Kinect](record-sensor-streams-file.md) e [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Per installare il pacchetto, eseguire:

`sudo apt install k4a-tools`
 
Questo comando installa i pacchetti di dipendenza necessari per il corretto funzionamento degli strumenti, inclusa la versione più recente di `libk4a<major>.<minor>` . È necessario aggiungere le regole udev per accedere ad Azure Kinect DK senza essere l'utente root. Per istruzioni, vedere [configurazione del dispositivo Linux](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). In alternativa, è possibile avviare applicazioni che usano il dispositivo come radice.

Il `libk4a<major>.<minor>-dev` pacchetto contiene le intestazioni e i file CMake per la compilazione di applicazioni/file eseguibili `libk4a` .

Il `libk4a<major>.<minor>` pacchetto contiene gli oggetti condivisi necessari per eseguire applicazioni/eseguibili che dipendono da `libk4a` .

Per le esercitazioni di base è richiesto il pacchetto `libk4a<major>.<minor>-dev`. Per installare il pacchetto, eseguire:

`sudo apt install libk4a<major>.<minor>-dev` 

Se il comando riesce, l'SDK è pronto per l'uso.

Assicurarsi di installare la versione corrispondente di `libk4a<major>.<minor>` con `libk4a<major>.<minor>-dev` . Se, ad esempio, si installa il `libk4a4.1-dev` pacchetto, installare il `libk4a4.1` pacchetto corrispondente che contiene la versione corrispondente dei file oggetto condivisi. Per la versione più recente di `libk4a` , vedere i collegamenti nella sezione successiva.

## <a name="change-log-and-older-versions"></a>Log delle modifiche e versioni precedenti

Il log delle modifiche per Azure Kinect Sensor SDK è disponibile [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Le versioni precedenti di Azure Kinect Sensor SDK sono disponibili [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Passaggi successivi

[Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)
