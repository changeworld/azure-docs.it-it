---
title: Gestione dell'accesso alla rete pubblica per l'hub Azure
description: Documentazione su come disabilitare e abilitare l'accesso alla rete pubblica per l'hub Internet
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b225ad7e12a95b4dcbb8656f54f8a9e9562b2ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604696"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gestione dell'accesso alla rete pubblica per l'hub Internet delle cose

Per limitare l'accesso solo all' [endpoint privato per l'hub Internet delle cose nel VNet](virtual-network-support.md), disabilitare l'accesso alla rete pubblica. A tale scopo, usare portale di Azure o l' `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Disabilitare l'accesso alla rete pubblica con portale di Azure

1. Visita [portale di Azure](https://portal.azure.com)
2. Passare all'hub IoT.
3. Selezionare **rete** dal menu a sinistra.
4. In "Consenti accesso alla rete pubblica a" selezionare **disabilitato**
5. Selezionare **Salva**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Immagine che Mostra portale di Azure dove disattivare l'accesso alla rete pubblica" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Per abilitare l'accesso alla rete pubblica, selezionare **tutte le reti**, quindi **Salva**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Accesso all'hub delle cose dopo la disabilitazione dell'accesso alla rete pubblica

Quando l'accesso alla rete pubblica è disabilitato, l'hub Internet è accessibile solo tramite [il relativo endpoint privato VNet usando il collegamento privato di Azure](virtual-network-support.md).

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Endpoint dell'hub Internet, indirizzo IP e porte dopo la disabilitazione dell'accesso alla rete pubblica

L'hub tutto è una piattaforma distribuita come servizio (PaaS) multi-tenant, quindi i clienti diversi condividono lo stesso pool di risorse hardware di calcolo, di rete e di archiviazione. I nomi host dell'hub delle cose vengono mappati a un endpoint pubblico con un indirizzo IP instradabile pubblicamente tramite Internet. Diversi clienti possono condividere questo endpoint pubblico dell'hub IoT e consentire l'accesso a tutti i dispositivi IoT su reti WAN (Wide Area Network) e su reti locali. 

La disabilitazione dell'accesso alla rete pubblica viene applicata a una risorsa dell'hub degli accessi specifici, garantendo l'isolamento. Per mantenere attivo il servizio per altre risorse dei clienti usando il percorso pubblico, l'endpoint pubblico rimane risolvibile, individuabile dagli indirizzi IP e le porte rimangono aperte. Questo non è un problema perché Microsoft integra più livelli di sicurezza per garantire un isolamento completo tra i tenant. Per altre informazioni, vedere [isolamento nel cloud pubblico di Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtro IP 

Se l'accesso alla rete pubblica è disabilitato, tutte le regole del [filtro IP](iot-hub-ip-filtering.md) verranno ignorate. Poiché tutti gli indirizzi IP della rete Internet pubblica sono bloccati. Per utilizzare il filtro IP, utilizzare l'opzione **intervalli IP selezionati** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correzione di bug con endpoint compatibile con l'hub eventi predefinito

Si è verificato un bug con l'hub Internet [, in cui l'endpoint compatibile con l'hub eventi predefinito](iot-hub-devguide-messages-read-builtin.md) continua a essere accessibile tramite Internet pubblico quando l'accesso alla rete pubblica per l'hub Internet è disabilitato. Per ulteriori informazioni e per informazioni su questo bug, vedere la pagina [relativa alla disabilitazione dell'accesso alla rete pubblica per l'hub Internet che disabilita l'accesso all'endpoint predefinito dell'hub eventi](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
