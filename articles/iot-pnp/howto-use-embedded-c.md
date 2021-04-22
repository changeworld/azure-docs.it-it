---
title: Usare IoT Plug and Play nei dispositivi vincolati | Microsoft Docs
description: Informazioni su come implementare IoT Plug and Play nei dispositivi vincolati usando l'SDK per Embedded C o Azure RTOS.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864138"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementare IoT Plug and Play nei dispositivi vincolati

Se si sviluppa per dispositivi vincolati, è possibile usare IoT Plug and Play con le librerie client Di [Azure SDK per Embedded C IoT](https://aka.ms/embeddedcsdk) o con Azure RTOS [.](/azure/rtos/overview-rtos) Questo articolo include collegamenti e risorse per questi scenari vincolati.

## <a name="use-the-sdk-for-embedded-c"></a>Usare l'SDK per Embedded C

L'SDK per Embedded C offre una soluzione leggera per connettere dispositivi vincolati Azure IoT servizi, incluso l'uso delle convenzioni IoT Plug and Play [.](concepts-convention.md) I collegamenti seguenti includono esempi per dispositivi reali e per scopi didattici e di debug.

### <a name="use-a-real-device"></a>Usare un dispositivo reale

Per un'esercitazione end-to-end completa che usa l'SDK per Embedded C, il servizio Device Provisioning e IoT Plug and Play in un dispositivo reale, vedere [Repurpose PIC-IoT Wx Development Board to Connect to Azure through IoT Hub Device Provisioning Service (Repurpose PIC-IoT Wx Development Board to Connect to Azure through IoT Hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)).

### <a name="introductory-samples"></a>Esempi introduttivi

Il repository SDK per Embedded C contiene [diversi esempi che](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) illustrano come usare IoT Plug and Play:

> [!NOTE]
> Questi esempi vengono mostrati in esecuzione in Windows e Linux per scopi didattici e di debug. In uno scenario di produzione, gli esempi sono destinati solo ai dispositivi vincolati.

- [Esempio di termostato con SDK per Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Esempio di Controller temperatura con l'SDK per Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Uso di Azure RTOS

Azure RTOS include un livello leggero che aggiunge connettività nativa ai Azure IoT cloud. Questo livello offre un meccanismo semplice per connettere i dispositivi vincolati Azure IoT usando le funzionalità avanzate di Azure RTOS. Per altre informazioni, vedere [Informazioni Microsoft Azure RTOS.](/azure/rtos/overview-rtos)

### <a name="toolchains"></a>Toolchain

Gli Azure RTOS sono forniti con diversi tipi di combinazioni di IDE e toolchain, ad esempio:

- IAR: IDE Di IAR [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/)
- GCC/CMake: si basa sul sistema open source di compilazione [CMake](https://cmake.org/) e sulla [toolchain incorporata GNU Arm.](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm)
- MCUExpresso: [IDE MCUXpresso](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) di NXP
- STM32Cube: [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html) di STMicroelectronic
- MPLAB: [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) di Microchip

### <a name="samples"></a>Esempi

Per esempi che illustrano come iniziare a usare dispositivi diversi con Azure RTOS e IoT Plug and Play, vedere la tabella seguente:

Produttore | Dispositivo | Esempi |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| Nxp | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| Stmicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| Stmicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| Stmicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le opzioni per l'implementazione di IoT Plug and Play nei dispositivi vincolati, un passaggio successivo consigliato consiste nell'ottenere informazioni sulle IoT Plug and Play [convenzioni](concepts-convention.md).