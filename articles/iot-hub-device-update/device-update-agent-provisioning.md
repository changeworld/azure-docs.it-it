---
title: Provisioning dell'aggiornamento del dispositivo per l'agente Hub Azure Internet | Microsoft Docs
description: Provisioning dell'aggiornamento del dispositivo per l'agente Hub Azure
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 79bac3f057412f3973121f48cd735f72d0a97d04
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679502"
---
# <a name="device-update-agent"></a>Agente di aggiornamento dispositivo

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti. 

* Gli aggiornamenti delle immagini forniscono un livello di confidenza maggiore nello stato finale del dispositivo. È in genere più semplice replicare i risultati di un aggiornamento di immagine tra un ambiente di pre-produzione e un ambiente di produzione, poiché non comporta le stesse esigenze dei pacchetti e delle relative dipendenze. A causa della natura atomica, è anche possibile adottare facilmente un modello di failover A/B. 
* Gli aggiornamenti basati su pacchetti sono aggiornamenti mirati che modificano solo un componente o un'applicazione specifica nel dispositivo. In questo modo, è possibile ridurre il consumo della larghezza di banda e ridurre il tempo necessario per scaricare e installare l'aggiornamento. Gli aggiornamenti dei pacchetti consentono in genere un minor tempo di inattività per i dispositivi quando si applica un aggiornamento ed evitano il sovraccarico della creazione di immagini. 

Seguire i collegamenti seguenti per la compilazione, l'esecuzione e la modifica dell'agente di aggiornamento del dispositivo.

## <a name="build-the-device-update-agent"></a>Compilare l'agente di aggiornamento del dispositivo

Seguire le istruzioni per [creare](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) l'agente di aggiornamento del dispositivo dall'origine.

## <a name="run-the-device-update-agent"></a>Eseguire l'agente di aggiornamento del dispositivo

Una volta che l'agente è stato compilato correttamente, è il momento di [eseguire](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) l'agente.

## <a name="modifying-the-device-update-agent"></a>Modifica dell'agente di aggiornamento del dispositivo

A questo punto, apportare le modifiche necessarie per incorporare l'agente nell'immagine.  Per informazioni aggiuntive, vedere come [modificare](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.m) l'agente di aggiornamento del dispositivo.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Se si verificano problemi, vedere la [Guida alla risoluzione dei](troubleshoot-device-update.md) problemi dell'hub di gestione delle cose per sbloccare i possibili problemi e raccogliere le informazioni necessarie per fornire a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Usare le immagini e i file binari predefiniti per una dimostrazione semplice dell'aggiornamento del dispositivo per l'hub Internet.  

[Aggiornamento immagine: Introduzione con Raspberry Pi 3 B + immagine Yocto di riferimento](device-update-raspberry-pi.md)

[Aggiornamento immagine: Introduzione con l'agente di riferimento del simulatore Ubuntu (18,04 x64)](device-update-simulator.md)

[Aggiornamento del pacchetto: Introduzione con l'agente di pacchetti di Ubuntu server 18,04 x64](device-update-ubuntu-agent.md)

