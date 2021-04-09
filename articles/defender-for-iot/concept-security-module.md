---
title: Defender-molto-micro-Agent e dispositivi gemelli
description: Informazioni sul concetto di gemelli Defender-be-micro-Agent e sul modo in cui vengono usati in Defender per l'it.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779172"
---
# <a name="defender-iot-micro-agent"></a>Defender-molto-micro-Agent

Questo articolo illustra come Defender for Internet usa dispositivi gemelli e moduli.

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Defender per IoT offre l'integrazione completa con la piattaforma di gestione di dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di fare uso delle funzionalità di controllo dispositivo esistenti. Per ottenere l'integrazione, è possibile usare il meccanismo gemello dell'hub Internet.

Altre informazioni sul concetto di [dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md) nell'hub Azure.

## <a name="defender-iot-micro-agent-twins"></a>Defender-molto-micro-Agent gemelli

Defender for Internet gestisce un dispositivo gemello Defender-are-micro-Agent per ogni dispositivo nel servizio.
Defender-Internet-micro-Agent gemello include tutte le informazioni rilevanti per la sicurezza dei dispositivi per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza del dispositivo vengono mantenute in un dispositivo Defender-Internet-micro-Agent dedicato per una comunicazione più sicura e per l'abilitazione degli aggiornamenti e della manutenzione che richiedono un minor numero di risorse.

Per informazioni su come creare, personalizzare e configurare il dispositivo gemello, vedere creare un dispositivo [gemello Defender-](quickstart-create-security-twin.md) Internet e [configurare gli agenti di sicurezza](how-to-agent-configuration.md) . Vedere [informazioni sui moduli gemelli](../iot-hub/iot-hub-devguide-module-twins.md) per altre informazioni sul concetto di moduli gemelli nell'hub Internet.

## <a name="see-also"></a>Vedi anche

- [Panoramica di Defender for Internet](overview.md)
- [Distribuire gli agenti di sicurezza](how-to-deploy-agent.md)
- [Metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)