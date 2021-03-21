---
title: Configurare e personalizzare Defender-RTO-micro-Agent per Azure
description: Informazioni su come configurare e personalizzare Defender-RTO-micro-Agent per Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2021
ms.author: shhazam
ms.openlocfilehash: 874a783763882a28f2fe7078e3a264d09107808a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495097"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Configurare e personalizzare Defender-RTO-micro-Agent per Azure (anteprima)

Questo articolo descrive come configurare Defender-RTO-micro-Agent per il dispositivo Azure per soddisfare i requisiti di rete, larghezza di banda e memoria.

Dalla directory è necessario selezionare un file di distribuzione di destinazione con `*.dist` estensione `netxduo/addons/azure_iot/azure_iot_security_module/configs` .  

Quando si usa un ambiente di compilazione CMake, è necessario impostare un parametro della riga di comando su `IOT_SECURITY_MODULE_DIST_TARGET` per il valore scelto. Ad esempio: `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

In una IAR o in un altro ambiente di compilazione non CMake, è necessario aggiungere il `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` percorso ai percorsi inclusi noti. Ad esempio: `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Usare il file seguente per configurare il comportamento del dispositivo.

**netxduo/addons/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

In un ambiente di compilazione CMake, è necessario modificare la configurazione predefinita modificando il `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` file. Usare il formato CMake seguente `set(ASC_XXX ON)` o il file seguente `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` per tutti gli altri ambienti. Ad esempio: `#define ASC_XXX`.

Il comportamento predefinito di ogni configurazione è disponibile nelle tabelle seguenti: 

## <a name="general"></a>Generale

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | string | Defender-molto-micro-Agent | Identificatore univoco del dispositivo.  |
| SECURITY_MODULE_VERSION_ (PRINCIPALE) (MINORE) (PATCH)  | Number | 3.2.1 | Versione. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Number  | 3 | Tempo necessario per l'invio del messaggio di sicurezza dopo un errore da parte di Defender-Internet. (in secondi) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | Tempo di attesa (in secondi) del Defender-Internet. Se viene superato il tempo, lo stato verrà modificato in Sospendi. |

## <a name="collection"></a>Raccolta

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Number  | 30  | Offset intervallo raccolta di avvio dell'agente di raccolta. Durante l'avvio, il valore verrà aggiunto alla raccolta del sistema per evitare di inviare messaggi da più dispositivi contemporaneamente.  |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | Intervallo del gruppo con priorità alta dell'agente di raccolta (in secondi). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | Intervallo del gruppo di priorità media dell'agente di raccolta (in secondi). |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145.440  | Intervallo del gruppo con priorità bassa dell'agente di raccolta (in secondi). |

#### <a name="collector-network-activity"></a>Attività di rete dell'agente di raccolta

Per personalizzare la configurazione dell'attività di rete dell'agente di raccolta, usare quanto segue:

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtra l' `TCP` attività di rete. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtra gli `UDP` eventi dell'attività di rete. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtra gli `ICMP` eventi dell'attività di rete. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Acquisisce solo i pacchetti in ingresso unicast. Quando è impostato su false, acquisisce anche broadcast e multicast. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | Invia un evento vuoto dell'agente di raccolta. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | Numero massimo di eventi di rete IPv4 da archiviare in memoria. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | Numero massimo di eventi di rete IPv6 da archiviare in memoria. |

### <a name="collectors"></a>Raccolta
| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ON | Abilita l'agente di raccolta heartbeat. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ON | Abilita l'agente di raccolta attività di rete. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ON | Abilita l'agente di raccolta informazioni di sistema.  |

Altri flag di configurazione sono avanzati e hanno funzionalità non supportate. Per modificare questo o per ulteriori informazioni, contattare il supporto tecnico.
 
## <a name="supported-security-alerts-and-recommendations"></a>Avvisi e raccomandazioni di sicurezza supportati

Defender-RTO-micro-Agent per Azure supporta avvisi e raccomandazioni di sicurezza specifici. Assicurarsi di [rivedere e personalizzare i valori di avviso e raccomandazione pertinenti](concept-rtos-security-alerts-recommendations.md) per il servizio.

## <a name="log-analytics-optional"></a>Log Analytics (facoltativo)

È possibile abilitare e configurare Log Analytics per esaminare gli eventi e le attività del dispositivo. Per altre informazioni, vedere l'articolo relativo alla configurazione e all'uso di [log Analytics con il servizio Defender for](how-to-security-data-access.md#log-analytics) Internet. 

## <a name="next-steps"></a>Passaggi successivi


- Esaminare e personalizzare Defender-RTO-micro-Agent per gli [avvisi di sicurezza e le raccomandazioni](concept-rtos-security-alerts-recommendations.md) di Azure
- Fare riferimento all' [API Defender-RTO-micro-Agent per Azure](azure-rtos-security-module-api.md) in base alle esigenze.
