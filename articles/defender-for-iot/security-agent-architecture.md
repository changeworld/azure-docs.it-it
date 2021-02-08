---
title: Panoramica degli agenti di sicurezza
description: Comprendere l'architettura dell'agente di sicurezza per gli agenti usati nel servizio Azure Defender for Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 929b8de8a6b70519bb44e41ba5cfede9ec12f110
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808862"
---
# <a name="security-agent-reference-architecture"></a>Architettura di riferimento dell'agente di sicurezza

Azure Defender per l'it fornisce un'architettura di riferimento per gli agenti di sicurezza che registrano, elaborano, aggregano e inviano i dati di sicurezza tramite l'hub Internet.

Gli agenti di sicurezza sono progettati per funzionare in un ambiente di gestione delle risorse umane vincolato e sono altamente personalizzabili in termini di valori forniti rispetto alle risorse che utilizzano.

Gli agenti di sicurezza supportano le funzionalità seguenti:

- Eseguire l'autenticazione con l'identità del dispositivo esistente o un'identità del modulo dedicata. Per altre informazioni, vedere [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md).

- Raccogliere gli eventi di sicurezza non elaborati dal sistema operativo sottostante (Linux, Windows). Per ulteriori informazioni sugli agenti di raccolta dati di sicurezza disponibili, vedere [Defender per la configurazione dell'agente](how-to-agent-configuration.md)Internet.

- Aggregare gli eventi di sicurezza non elaborati in messaggi inviati tramite l'hub.

- Configurare in modalità remota tramite l'uso del dispositivo gemello del modulo **azureiotsecurity** . Per altre informazioni, vedere [configurare un Defender per l'agente](how-to-agent-configuration.md).

Defender for Internet Security Agents viene sviluppato come progetto open source ed è disponibile da GitHub:

- [Defender per l'agente basato su C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender per l'agente basato su C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Piattaforme supportate dagli agenti

Defender for Internet è dotato di diversi agenti di installazione per Windows a 32 bit e a 64 bit e lo stesso vale per 32 bit e 64 bit Linux. Verificare che sia installato il programma di installazione dell'agente corretto per ogni dispositivo in base alla tabella seguente:

| Architettura | Linux | Windows | Dettagli |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64 bit | C# o C | C# | È consigliabile usare l'agente C per i dispositivi con risorse del dispositivo più limitate o minime. |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata illustrata una panoramica di alto livello sull'architettura dei moduli di sicurezza per l'IT e i programmi di installazione disponibili.

Per continuare a usare Defender per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

- Informazioni sui [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)
- Selezionare e distribuire un [agente di sicurezza](how-to-deploy-agent.md)
- Esaminare il Defender per i [prerequisiti del sistema](quickstart-system-prerequisites.md) Internet delle cose
- Informazioni su come [abilitare Defender per il servizio Internet delle cose nell'hub Internet delle](quickstart-onboard-iot-hub.md) cose
- Altre informazioni sul servizio sono disponibili nella pagina [relativa alle domande frequenti su Defender](resources-frequently-asked-questions.md)
