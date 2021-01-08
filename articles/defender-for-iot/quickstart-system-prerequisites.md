---
title: Prerequisiti di sistema
description: Informazioni sui prerequisiti di sistema necessari per eseguire Azure Defender per IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8ee3afcae69ca6c082452e590eb8370bcc122af4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844460"
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

## <a name="see-also"></a>Vedere anche

- [Identificare le appliance necessarie](how-to-identify-required-appliances.md)
- [Informazioni sulla configurazione della rete di Azure Defender per IoT](how-to-set-up-your-network.md)
