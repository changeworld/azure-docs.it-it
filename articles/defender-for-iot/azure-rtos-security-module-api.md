---
title: API del modulo di sicurezza per Azure RTOS
description: API di riferimento per il modulo di sicurezza per Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120063"
---
# <a name="security-module-for-azure-rtos-api"></a>API del modulo di sicurezza per Azure RTOS 

Questa API è destinata all'uso con il modulo di sicurezza solo per Azure RTO. Per altre risorse, vedere il [modulo Security per la risorsa GitHub di Azure RTO](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Abilitare il modulo di sicurezza per Azure RTO

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Prototipo

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrizione

Questa routine Abilita il sottosistema del modulo Security di Azure. Una macchina a stati interna gestisce la raccolta di eventi di sicurezza e li invia all'hub di Azure. È necessaria una sola istanza di NX_AZURE_IOT_SECURITY_MODULE e necessaria per gestire la raccolta dei dati.

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

## <a name="disable-azure-iot-security-module"></a>Disabilitare il modulo di sicurezza Azure Internet

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Prototipo

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrizione

Questa routine Disabilita il sottosistema del modulo di sicurezza di Azure.

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

Per ulteriori informazioni su come iniziare a usare il modulo di sicurezza di Azure RTO, vedere gli articoli seguenti:

- Vedere la [Panoramica](iot-security-azure-rtos.md)di Defender per il modulo di sicurezza RTO.