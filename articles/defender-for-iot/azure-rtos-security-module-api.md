---
title: Defender-molto-micro-Agent per l'API RTO di Azure
description: API di riferimento per Defender-RTO-micro-Agent per Azure.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779393"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-molto-micro-Agent per l'API RTO di Azure (anteprima)

Questa API è destinata all'uso solo con Defender-RTO-micro-Agent solo per Azure. Per altre risorse, vedere la [risorsa di GitHub Defender-Internet-micro-Agent per Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Abilitare Defender-RTO-micro-Agent per Azure

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototipo

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrizione

Questa routine Abilita il sottosistema Azure Internet per le cose Defender-Internet. Una macchina a stati interna gestisce la raccolta di eventi di sicurezza e li invia all'hub di Azure. È necessaria una sola istanza di NX_AZURE_IOT_SECURITY_MODULE e necessaria per gestire la raccolta dei dati.

### <a name="parameters"></a>Parametri

| Nome | Descrizione |
|---------|---------|
| nx_azure_iot_ptr [in]    | Puntatore a un oggetto `NX_AZURE_IOT`.  |

### <a name="return-values"></a>Valori restituiti

|Valori restituiti  |Descrizione |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Il modulo di sicurezza Azure Internet è stato abilitato.     |
|NX_AZURE_IOT_FAILURE   |  Non è stato possibile abilitare il modulo di sicurezza Azure per le cose a causa di un errore interno.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Il modulo di sicurezza richiede un'istanza di #NX_AZURE_IOT valida.      |

### <a name="allowed-from"></a>Consentito da

Thread

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Disabilitare Azure le cose Defender-Internet

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototipo

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrizione

Questa routine Disabilita il sottosistema Azure Internet delle cose Defender-Internet.

### <a name="parameters"></a>Parametri

| Nome | Descrizione |
|---------|---------|
| nx_azure_iot_ptr [in]    | Puntatore a `NX_AZURE_IOT`. Se NULL, l'istanza singleton è disabilitata. |

### <a name="return-values"></a>Valori restituiti

|Valori restituiti  |Descrizione |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Operazione riuscita quando il modulo di sicurezza Azure Internet è stato disabilitato.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  L'istanza dell'hub Internet Azure è diversa dall'istanza composta singleton.       |
|NX_AZURE_IOT_FAILURE    |  Non è stato possibile disabilitare il modulo di sicurezza Azure per le cose a causa di un errore interno.       |

### <a name="allowed-from"></a>Consentito da

Thread


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare a usare Azure RTO Defender--micro-Agent, vedere gli articoli seguenti:

- Vedere la pagina relativa alla [Panoramica](iot-security-azure-rtos.md)di Defender per gli elementi RTO Defender--micro-Agent.
