---
title: Limiti della soluzione VMware di Azure
description: Limitazioni della soluzione VMware di Azure.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193899"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

La tabella seguente descrive i limiti massimi per la soluzione VMware di Azure.

| **Risorsa** | **Limite** |
| :-- | :-- |
| Cluster per cloud privato | 4 |
| Numero minimo di nodi per cluster | 3 |
| Numero massimo di nodi per cluster | 16 |
| Nodi per cloud privato | 64 |
| vCenter per cloud privato | 1  |
| Associazioni di siti HCX | 3 con Advanced Edition, 10 con Enterprise Edition |
| AVS ExpressRoute max collegato SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps | 
| Indirizzi IP pubblici esposti tramite vWAN | 100 |
| limiti di capacità di rete VSAN | 75% del totale utilizzabile (mantenimento del 25% disponibile per il contratto di contratto)  |

Per altri limiti specifici di VMware, usare lo [strumento di configurazione massima di VMware](https://configmax.vmware.com/).
