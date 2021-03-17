---
title: Limiti della soluzione VMware di Azure
description: Limitazioni della soluzione VMware di Azure.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622343"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

La tabella seguente descrive i limiti massimi per la soluzione VMware di Azure.

| **Risorsa** | **Limite** |
| :-- | :-- |
| Cluster per cloud privato | 12 |
| Numero minimo di nodi per cluster | 3 |
| Numero massimo di nodi per cluster | 16 |
| Nodi per cloud privato | 64 |
| vCenter per cloud privato | 1  |
| Associazioni di siti HCX | 3 con Advanced Edition, 10 con Enterprise Edition |
| AVS ExpressRoute max collegato SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />Il gateway di rete virtuale usato determina la larghezza di banda effettiva. Per informazioni dettagliate, vedere [informazioni sui gateway di rete virtuale ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| Indirizzi IP pubblici esposti tramite vWAN | 100 |
| limiti di capacità di rete VSAN | 75% del totale utilizzabile (mantenimento del 25% disponibile per il contratto di contratto)  |

Per altri limiti specifici di VMware, usare lo [strumento di configurazione massima di VMware](https://configmax.vmware.com/).
