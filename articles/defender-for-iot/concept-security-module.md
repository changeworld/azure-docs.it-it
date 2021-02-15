---
title: Modulo di sicurezza e dispositivi gemelli
description: Informazioni sul concetto di gemelli dei moduli di sicurezza e sul modo in cui vengono usati in Defender per l'it.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: feb84a1261e37600c68d8e372162033d4021f564
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522921"
---
# <a name="security-module"></a>Modulo di sicurezza

Questo articolo illustra come Defender for Internet usa dispositivi gemelli e moduli.

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Defender per IoT offre l'integrazione completa con la piattaforma di gestione di dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di fare uso delle funzionalità di controllo dispositivo esistenti. Per ottenere l'integrazione, è possibile usare il meccanismo gemello dell'hub Internet.

Altre informazioni sul concetto di [dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md) nell'hub Azure.

## <a name="security-module-twins"></a>Moduli di sicurezza gemelli

Defender for Internet gestisce un modulo di sicurezza gemello per ogni dispositivo nel servizio.
Il gemello del modulo di sicurezza include tutte le informazioni rilevanti per la sicurezza dei dispositivi per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza del dispositivo vengono mantenute in un modulo di protezione dedicato gemello per una comunicazione più sicura e per abilitare gli aggiornamenti e la manutenzione che richiedono meno risorse.

Per informazioni su come creare, personalizzare e configurare il dispositivo gemello, vedere [creare un modulo di sicurezza gemello](quickstart-create-security-twin.md) e [configurare gli agenti di sicurezza](how-to-agent-configuration.md) . Vedere [informazioni sui moduli gemelli](../iot-hub/iot-hub-devguide-module-twins.md) per altre informazioni sul concetto di moduli gemelli nell'hub Internet.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Defender for Internet](overview.md)
- [Distribuire gli agenti di sicurezza](how-to-deploy-agent.md)
- [Metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)