---
title: includere file
description: includere file
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504693"
---
Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Quando si seleziona uno SKU superiore, al gateway vengono allocati un maggior numero di CPU e una larghezza di banda superiore, di conseguenza il gateway può supportare una velocità effettiva di rete più elevata per la rete virtuale. 

I gateway di rete virtuale ExpressRoute possono usare i seguenti SKU:

|     | Coesistenza gateway VPN ed ExpressRoute | FastPath | Numero massimo di connessioni del circuito |
| --- | --- | --- | --- |
| **Standard** | Sì | No | 4 |
| **HighPerformance** | Sì | No | 4 |
| **UltraPerformance** | Sì | Sì | 16 |