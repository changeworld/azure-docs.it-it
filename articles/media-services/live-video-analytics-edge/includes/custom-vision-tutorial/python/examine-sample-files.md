---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358240"
---
1. In Visual Studio Code passare a src/edge. Verranno visualizzati il file con estensione env creato e alcuni file del modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale con alcuni valori segnaposto. Il file con estensione env include i valori per queste variabili.
1. Passare quindi alla cartella src/cloud-to-device-console-app. Verrà visualizzato il file appsettings.json creato insieme ad altri file:

   * operations.json: file che elenca le diverse operazioni che il programma deve eseguire.
   * main.py: codice del programma di esempio che esegue le operazioni seguenti:
    
        * Carica le impostazioni dell'app.
        * Richiama Analisi video live sui metodi diretti del modulo IoT Edge per creare la topologia, creare un'istanza del grafo e attivare il grafo.
        * Sospende l'esecuzione per consentire di esaminare l'output del grafo nella finestra **TERMINALE** e gli eventi inviati all'hub IoT nella finestra **OUTPUT**.
        * Disattiva ed elimina l'istanza del grafo, quindi elimina la topologia del grafo.
