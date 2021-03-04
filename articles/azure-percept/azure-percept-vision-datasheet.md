---
title: Foglio dati sulla visione di Azure Percept
description: Vedere il foglio dati della visione di Azure Percept per specifiche del dispositivo dettagliate
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 54dae3341910bf863de7e2a2cef832ae670ca09a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097568"
---
# <a name="azure-percept-vision-datasheet"></a>Foglio dati sulla visione di Azure Percept

Le specifiche elencate di seguito sono relative al dispositivo Azure Percept Vision, incluso in [Azure PERCEPT dk](./azure-percept-dk-datasheet.md).

|Specifica del prodotto           |Valore     |
|--------------------------------|---------------------|
|Settori di destinazione               |Produzione <br> Edifici intelligenti <br> Auto <br> Retail |
|Scenari di Hero                  |Analisi del cliente <br> Disponibilità su scaffale <br> Riduzione compattazione <br> Monitoraggio dell'area di lavoro|
|Dimensioni                      |42 x 42 millimetri x 40mm (assembly di Azure Percept Vision SoM con Housing) <br> 42 x 42 mm x 6mm (chip SoM visione)|
|Piano di controllo di gestione        |Aggiornamento del dispositivo Azure (ADU)          |
|Software e servizi supportati |[Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [Runtime ONNX](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Aggiornamento del dispositivo Azure |
|Accelerazione intelligenza artificiale                 |Intel Movidius miriade X (MA2085) Vision Processing Unit (VPU) with Intel fotocamera ISP Integrated, 0,7 Top |
|Sensori e indicatori visivi   |Sensore della fotocamera IMX219 Sony con obiettivo 6P<br>Soluzione: 8MP a 30 fps, distanza: 50cm-infinito<br>FoV: 120 gradi diagonale, colore: ampio intervallo dinamico, otturatore fisso a scorrimento|
|Supporto della fotocamera                  |RGB (attualmente) e IR (in futuro) <br> 2 fotocamere possono essere eseguite simultaneamente |
|Crypto-Controller di sicurezza      |ST-Micro STM32L462CE      |
|Componente di controllo delle versioni/ID       |EEPROM 64KB |
|Memoria                          |LPDDR4 2 GB     |
|Potenza                           |3,5 W     |
|Porte                           |1x USB 3,0 tipo C <br> 2x MIPI 4 Lane (fino a 1,5 Gbps per corsia)     |
|Interfacce di controllo              |2x I2C <br> 2x SPI <br> 6x PWM (GPIO: 2x clock, 2x frame sync, 2x inused) <br> 2x GPIO di riserva |
|Certificazione                   |FCC <br> IC <br> RoHS <br> RAGGIUNGERE <br> UL   |
|Temperatura operativa           |da 0 a 27 gradi C (Azure Percept Vision SoM assembly with Housing) <br> da-10 a 70 gradi C (visione del chip SoM) |
|Temperatura tocco               |<= 48 gradi C |
|Umidità relativa               |da 8% a 90%    |
