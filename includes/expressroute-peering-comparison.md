---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751127"
---
|  | **Peering privato** | **Peering Microsoft** |  **Peering pubblico** (deprecato per i circuiti nuovi) |
| --- | --- | --- | --- |
| **Numero massimo di prefissi supportati per peering** |4000 per impostazione predefinita, 10.000 con ExpressRoute Premium |200 |200 |
| **Intervalli di indirizzi IP supportati** |Qualsiasi indirizzo IP valido entro la rete WAN. |Indirizzi IP pubblici di proprietà dell'utente o del provider di connettività. |Indirizzi IP pubblici di proprietà dell'utente o del provider di connettività. |
| **Requisiti del numero AS** |Numeri AS pubblici e privati. È necessario possedere un numero AS pubblico se si sceglie di usarne uno. |Numeri AS pubblici e privati. È tuttavia necessario dimostrare la proprietà degli indirizzi IP pubblici. |Numeri AS pubblici e privati. È tuttavia necessario dimostrare la proprietà degli indirizzi IP pubblici. |
| **Protocolli IP supportati**| IPv4, IPv6 (anteprima) |  IPv4, IPv6 | IPv4 |
| **Indirizzi IP per l'interfaccia di routing** |Indirizzi IP pubblici e RFC1918 |Indirizzi IP pubblici registrati a nome dell'utente nei registri di routing. |Indirizzi IP pubblici registrati a nome dell'utente nei registri di routing. |
| **Supporto per Hash MD5** |Sì |Sì |Sì |