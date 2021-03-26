---
title: Comportamento del pulsante e del LED audio di Azure Percept
description: Altre informazioni sul pulsante e sugli stati dei LED di Azure Percept audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609072"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Comportamento del pulsante e del LED audio di Azure Percept

Per informazioni sul pulsante e sugli stati dei LED del dispositivo audio Azure Percept, vedere le indicazioni seguenti.

## <a name="button-behavior"></a>Comportamento dei pulsanti

Usare i pulsanti per controllare il comportamento del dispositivo.

|Stato pulsante|Comportamento|
|------------|----------|
|Disattiva audio|Premere per disattivare/disattivare la matrice MIC. L'evento Button viene attivato dalla versione quando viene premuto.|
|PTT/PTS|Premere PTT per ignorare lo stato di individuazione delle parole chiave e attivare lo stato di ascolto del comando. Premere di nuovo per arrestare il dialogo attivo dell'agente e ripristinare lo stato di individuazione delle parole chiave. L'evento Button viene attivato dalla versione quando viene premuto. La funzione PTS funziona solo quando si preme il pulsante mentre l'agente sta parlando, non quando l'agente è in ascolto o nel pensiero.|

## <a name="led-behavior"></a>Comportamento del LED

Usare gli indicatori LED per comprendere lo stato in cui si trova il dispositivo.

|LED|Stato del LED|Stato SoM Ear|
|---|------------|----------------|
|L02|1x bianco, statico on|Accendere |
|L02|1x bianco, 0,5 Hz lampeggianti|Autenticazione in corso |
|L01 & L02 & L03|3x blu, statico on|In attesa di una parola chiave|
|L01 & L02 & L03|Matrice LED lampeggiante, 20fps |In ascolto o in conversazione|
|L01 & L02 & L03|LED Array Racing, 20fps|Pensare|
|L01 & L02 & L03|3x rosso, statico |Disattiva audio|

## <a name="next-steps"></a>Passaggi successivi

Per suggerimenti sulla risoluzione dei problemi relativi al dispositivo audio Azure Percept, vedere questa [Guida](./troubleshoot-audio-accessory-speech-module.md).