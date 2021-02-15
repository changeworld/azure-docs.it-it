---
title: Prerequisiti di sistema
description: Informazioni sui prerequisiti di sistema necessari per eseguire Azure Defender per IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523210"
---
# <a name="system-prerequisites"></a>Prerequisiti di sistema
Questo articolo elenca i prerequisiti di sistema per l'esecuzione di Azure Defender per IoT.

## <a name="minimum-requirements"></a>Requisiti minimi

- Commutatori di rete che supportano il monitoraggio del traffico tramite la porta SPAN.
- Appliance hardware per i sensori di analisi del traffico di rete.
- Ruolo Collaboratore della sottoscrizione di Azure. È necessario solo durante l'onboarding per la definizione dei dispositivi impegnati e la connessione ad Azure Sentinel.
- Ruolo **Collaboratore** dell'hub IoT di Azure (livello Gratuito o Standard) per la gestione connessa al cloud. Verificare che la funzionalità **Azure Defender per IoT** sia abilitata.
- Per il supporto dei moduli di sicurezza a livello di dispositivo, gli agenti di Defender per IoT supportano un elenco in continua crescita di dispositivi e piattaforme. Vedere l'[elenco delle piattaforme supportate](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Aree del servizio supportate

Defender per IoT indirizza tutto il traffico da tutte le aree europee al data center dell'area Europa occidentale. Indirizza il traffico di tutte le altre aree al data center dell'area Stati Uniti centrali.

Per altre informazioni, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Passaggi successivi

[Identificare le appliance obbligatorie](how-to-identify-required-appliances.md) 
 [Informazioni su Azure Defender per la configurazione della rete Internet](how-to-set-up-your-network.md)
