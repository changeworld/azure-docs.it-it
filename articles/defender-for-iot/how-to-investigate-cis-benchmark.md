---
title: Esaminare la raccomandazione benchmark di CIS
titleSuffix: Azure Defender for IoT
description: Eseguire analisi di base e avanzate basate sulle raccomandazioni di base del sistema operativo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809835"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Esaminare la baseline del sistema operativo (in base al benchmark CIS) 

Eseguire analisi di base e avanzate basate sulle raccomandazioni di base del sistema operativo.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Analisi delle raccomandazioni sulla sicurezza di base del sistema operativo  

È possibile esaminare le raccomandazioni di base del sistema operativo passando al portale di Azure Defender per le cose, nell' **Hub** Internet delle cose. Per ulteriori informazioni, vedere come [esaminare le raccomandazioni sulla sicurezza](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Analisi della sicurezza della linea di base del sistema operativo avanzata  

Questa sezione descrive come comprendere meglio i risultati dei test di base del sistema operativo ed eseguire query sugli eventi in Azure Log Analytics.  

L'analisi delle raccomandazioni sulla sicurezza della linea di base del sistema operativo avanzata è supportata solo con log Analytics. Prima di continuare, connettere Defender per le cose a un'area di lavoro Log Analytics. Per altre informazioni sui consigli sulla sicurezza di base del sistema operativo, vedere come [configurare Azure Defender per la soluzione basata su agenti](how-to-configure-agent-based-solution.md)it.

Per eseguire query sugli eventi di sicurezza di Internet delle cose in Log Analytics per gli avvisi:

1. Passare alla pagina degli **avvisi** .

1. Selezionare **analisi consigli nell'area di lavoro log Analytics**.

Per eseguire query sugli eventi di sicurezza di Internet delle cose in Log Analytics per consigli:

1. Passare alla pagina **raccomandazioni** .

1. Selezionare **analisi consigli nell'area di lavoro log Analytics**.

1. Per visualizzare i dettagli di un dispositivo specifico, selezionare **Visualizza informazioni di base sulle regole di base del sistema operativo** nella pagina di visualizzazione rapida dei **Dettagli della raccomandazione** .

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Vedere i dettagli di un dispositivo specifico."::: 

Per eseguire direttamente una query sugli eventi di sicurezza in Log Analytics area di lavoro:

1. Passare alla pagina **log** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Selezionare logs (registri) nel riquadro sinistro.":::

1. Selezionare **esaminare gli avvisi** o, selezionare l'opzione **esaminare gli avvisi in log Analytics** da qualsiasi raccomandazione di sicurezza o avviso.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Query utili per esaminare le risorse di base del sistema operativo: 

> [!Note]
> Assicurarsi di sostituire `<device-id>` con il nome/i assegnato al dispositivo in ognuna delle query seguenti. 


### <a name="retrieve-the-latest-information"></a>Recuperare le informazioni più recenti

- **Errore della flotta del dispositivo**: eseguire la query seguente per recuperare le informazioni più recenti sui controlli non riusciti attraverso la flotta del dispositivo: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Errore specifico del dispositivo** : eseguire la query seguente per recuperare le informazioni più recenti sui controlli non riusciti in un dispositivo specifico:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Errore specifico del dispositivo** : eseguire questa query per recuperare le informazioni più recenti sui controlli che contengono un errore in un dispositivo specifico: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- Aggiornare l'elenco dei dispositivi per i dispositivi **che non hanno superato un controllo specifico** : eseguire questa query per recuperare l'elenco aggiornato di dispositivi (attraverso la flotta del dispositivo) che non hanno superato un controllo specifico:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Passaggi successivi

[Esaminare le raccomandazioni sulla sicurezza](quickstart-investigate-security-recommendations.md).
 