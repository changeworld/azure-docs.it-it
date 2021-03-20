---
title: includere file
titleSuffix: Azure
description: includere file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680792"
---
Il cmdlet di PowerShell **Get-AzPeeringLocation** restituisce un elenco di percorsi di peering con il parametro obbligatorio `Kind` , che verrà usato nei passaggi successivi.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Le località di peering dirette contengono i campi seguenti:
* PeeringLocation 
* Paese
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Verificare di essere presenti nella struttura di peering desiderata facendo riferimento a [PeeringDB](https://wwww.peeringdb.com).

Questo esempio illustra come usare Seattle come posizione di peering per creare un peering diretto.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```