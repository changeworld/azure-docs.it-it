---
title: Limiti della soluzione VMware di Azure
description: Limitazioni della soluzione VMware di Azure.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582858"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

La tabella seguente descrive i limiti massimi per la soluzione VMware di Azure.

| **Risorsa** | **Limite** |
| :-- | :-- |
| Cluster per cloud privato | 12 |
| Numero minimo di nodi per cluster | 3 |
| Numero massimo di nodi per cluster | 16 |
| Nodi per cloud privato | 96 |
| vCenter per cloud privato | 1  |
| Associazioni di siti HCX | 3 con Advanced Edition, 10 con Enterprise Edition |
| AVS ExpressRoute max collegato SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />Il gateway di rete virtuale usato determina la larghezza di banda effettiva. Per informazioni dettagliate, vedere [informazioni sui gateway di rete virtuale ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| Indirizzi IP pubblici esposti tramite vWAN | 100 |
| limiti di capacità di rete VSAN | 75% del totale utilizzabile (mantenimento del 25% disponibile per il contratto di contratto)  |

Per altri limiti specifici di VMware, usare lo [strumento di configurazione massima di VMware](https://configmax.vmware.com/).
