---
title: Comportamento del pulsante e del LED audio di Azure Percept
description: Altre informazioni sul pulsante e sugli stati dei LED di Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c7ee2289680bb50fabb8e6eb2a3ea0466bd58afb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663432"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Comportamento del pulsante e del LED audio di Azure Percept

Vedere le linee guida seguenti per informazioni sul pulsante e sugli stati dei LED dell'audio di Azure Percept.

## <a name="button-behavior"></a>Comportamento dei pulsanti

È possibile usare i pulsanti per controllare il comportamento del dispositivo.

|Stato pulsante|  Comportamento|
|------------|----------|
|Disattiva audio|  Premere per disattivare/disattivare la matrice MIC. L'evento Button viene attivato dalla versione quando viene premuto.|
|PTT/PTS|   Premere PTT per ignorare lo stato di individuazione delle parole chiave e attivare lo stato di ascolto del comando. Premere di nuovo per arrestare il dialogo attivo dell'agente e ripristinare lo stato di individuazione delle parole chiave.|

## <a name="led-behavior"></a>Comportamento del LED

È possibile usare gli indicatori LED per comprendere lo stato in cui si trova il dispositivo.

|LED|   Stato del LED|  Stato SoM Ear|
|---|------------|----------------| 
|L02|   1x bianco, statico on |Accendere |
|L02|   1x bianco, 0,5 Hz lampeggianti|  Autenticazione in corso |
|L01 & L02 & L03|   3x blu, statico on|     Parola chiave rilevata|
|L01 & L02 & L03|   Matrice LED lampeggiante, 20fps | In ascolto o in conversazione|
|L01 & L02 & L03|   LED Array Racing, 20fps|    Pensare|
|L01 & L02 & L03|   3x rosso, statico | Disattiva audio|

## <a name="next-steps"></a>Passaggi successivi

Per suggerimenti sulla risoluzione dei problemi relativi al dispositivo audio Azure Percept, vedere questa [Guida](./troubleshoot-audio-accessory-speech-module.md).