---
title: Configurare la soluzione di sicurezza per connettere i dati CEF ad Anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come configurare la soluzione di sicurezza per connettere i dati CEF ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: fec3f25c4b401ff7c3bc73d249b716b9c12e6529
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548546"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>PASSAGGIO 2: configurare la soluzione di sicurezza per l'invio di messaggi CEF

In questo passaggio si eseguiranno le modifiche di configurazione necessarie per la soluzione di sicurezza stessa per inviare i log all'agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurare una soluzione con un connettore

Se la soluzione di sicurezza ha già un connettore esistente, usare le istruzioni specifiche del connettore come indicato di seguito:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Prodotti Forcepoint](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Configurare qualsiasi altra soluzione

Se non esiste un connettore per la soluzione di sicurezza specifica, usare le istruzioni generiche seguenti per l'invio dei log all'agente CEF.

1. Per istruzioni su come configurare la soluzione per l'invio di messaggi CEF, vedere l'articolo di configurazione specifico. Se la soluzione non è elencata, nell'appliance è necessario impostare questi valori in modo che l'appliance invii i registri necessari nel formato necessario all'agente syslog di Azure Sentinel, in base all'agente Log Analytics. È possibile modificare questi parametri nel dispositivo, purché vengano modificati anche nel daemon syslog nell'agente Sentinel di Azure.
    - Protocollo = TCP
    - Porta = 514
    - Formato = CEF
    - Indirizzo IP: assicurarsi di inviare i messaggi CEF all'indirizzo IP della macchina virtuale dedicata a questo scopo.

   Questa soluzione supporta syslog RFC 3164 o RFC 5424.

1. Per cercare gli eventi CEF in Log Analytics, immettere `CommonSecurityLog` nella finestra query.

1. Continuare con il passaggio 3: [convalidare la connettività](connect-cef-verify.md).

> [!NOTE]
> **Modifica dell'origine del campo TimeGenerated**
>
> - Per impostazione predefinita, l'agente di Log Analytics popola il campo *TimeGenerated* nello schema con il momento in cui l'agente ha ricevuto l'evento dal daemon syslog. Di conseguenza, l'ora in cui l'evento è stato generato nel sistema di origine non viene registrato in Sentinel di Azure.
>
> - È tuttavia possibile eseguire il comando seguente, che consente di scaricare ed eseguire lo `TimeGenerated.py` script. Questo script consente di configurare l'agente di Log Analytics per popolare il campo *TimeGenerated* con l'ora originale dell'evento nel sistema di origine, invece del momento in cui è stato ricevuto dall'agente.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).