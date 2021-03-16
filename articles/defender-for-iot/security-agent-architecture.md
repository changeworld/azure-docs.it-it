---
title: 'Guida introduttiva: Panoramica degli agenti di sicurezza'
description: In questa Guida introduttiva si apprenderà come comprendere l'architettura dell'agente di sicurezza per gli agenti usati in Azure Defender per il servizio Internet delle cose.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: de8c52372b2ef92dcf2abe357a40ef8947439a8a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493963"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Guida introduttiva: architettura di riferimento dell'agente di sicurezza

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

## <a name="prerequisites"></a>Prerequisiti

nessuno

## <a name="agent-supported-platforms"></a>Piattaforme supportate dagli agenti

Defender for Internet è dotato di diversi agenti di installazione per Windows a 32 bit e a 64 bit e lo stesso vale per 32 bit e 64 bit Linux. Verificare che sia installato il programma di installazione dell'agente corretto per ogni dispositivo in base alla tabella seguente:

| Architettura | Linux | Windows | Dettagli |
|--|--|--|--|
| 32 bit | C | C# |  |
| 64 bit | C# o C | C# | È consigliabile usare l'agente C per i dispositivi con risorse del dispositivo più limitate o minime. |


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata illustrata una panoramica di alto livello su Defender per l'architettura di Internet delle cose Defender-molto-micro-Agent e dei programmi di installazione disponibili.

Per continuare a usare Defender per la distribuzione di Internet delle cose, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)
