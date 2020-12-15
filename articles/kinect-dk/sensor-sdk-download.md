---
title: Download di Azure Kinect Sensor SDK
description: Informazioni su come scaricare e installare Azure Kinect Sensor SDK in Windows e Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, download dell'aggiornamento, più recente, disponibile, installazione
ms.openlocfilehash: 2fd14781c42192c713d826729f8fab6c698d6321
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505478"
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

A questo punto, è possibile installare i pacchetti necessari. Il pacchetto `k4a-tools` include il [visualizzatore di Azure Kinect](azure-kinect-viewer.md), il [registratore di Azure Kinect](record-sensor-streams-file.md) e [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Per installarlo, eseguire

 `sudo apt install k4a-tools`

 Il pacchetto `libk4a<major>.<minor>-dev` contiene le intestazioni e i file CMake da compilare con `libk4a`.
Il pacchetto `libk4a<major>.<minor>` contiene gli oggetti condivisi necessari per eseguire i file eseguibili che dipendono da `libk4a`.

 Per le esercitazioni di base è richiesto il pacchetto `libk4a<major>.<minor>-dev`. Per installarlo, eseguire

 `sudo apt install libk4a1.1-dev`

Se il comando riesce, l'SDK è pronto per l'uso.

## <a name="change-log-and-older-versions"></a>Log delle modifiche e versioni precedenti

Il log delle modifiche per Azure Kinect Sensor SDK è disponibile [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Le versioni precedenti di Azure Kinect Sensor SDK sono disponibili [qui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Passaggi successivi

[Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)