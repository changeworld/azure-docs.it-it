---
title: 'Guida introduttiva: prerequisiti di sistema'
description: In questa Guida introduttiva, ottenere i prerequisiti di sistema necessari per eseguire Azure Defender per l'it.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382947"
---
# <a name="quickstart-system-prerequisites"></a>Guida introduttiva: prerequisiti di sistema

Questo articolo elenca i prerequisiti di sistema per l'esecuzione di Azure Defender per IoT.

## <a name="prerequisites"></a>Prerequisiti

- nessuno

## <a name="minimum-requirements"></a>Requisiti minimi

- Commutatori di rete che supportano il monitoraggio del traffico tramite la porta SPAN.
- Appliance hardware per i sensori di analisi del traffico di rete.
- Ruolo Collaboratore della sottoscrizione di Azure. È necessario solo durante l'onboarding per la definizione dei dispositivi impegnati e la connessione ad Azure Sentinel.
- Ruolo **Collaboratore** dell'hub IoT di Azure (livello Gratuito o Standard) per la gestione connessa al cloud. Verificare che la funzionalità **Azure Defender per IoT** sia abilitata.
- Per il supporto a livello di dispositivo Defender-Internet-micro-Agent, Defender per gli agenti Internet supporta un elenco in continua crescita di dispositivi e piattaforme. Vedere l'[elenco delle piattaforme supportate](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Aree del servizio supportate

Defender per IoT indirizza tutto il traffico da tutte le aree europee al data center dell'area Europa occidentale. Indirizza il traffico di tutte le altre aree al data center dell'area Stati Uniti centrali.

Per altre informazioni, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Identificare le appliance necessarie](how-to-identify-required-appliances.md)
