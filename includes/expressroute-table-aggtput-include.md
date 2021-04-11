---
title: includere file
description: includere file
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504694"
---
| SKU del gateway | Connessioni al secondo | Numero di flussi | Mega bit al secondo | Pacchetti al secondo | Larghezza di banda del circuito | Numero di route annunciate dal gateway (per MSEE) | Numero di route apprese dal gateway (da MSEE) | Numero di macchine virtuali nella rete virtuale |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **SKU di base (obsoleto)** | N/D | N/D | 500 | N/D | N/D | N/D | N/D | N/D |
| **SKU Standard/ErGw1AZ** | 7.000 | 400.000 | >1.000 | >100.000 | 1 Gbps |  500 | 4.000 | 2.000 (riduzione a 1.000 durante la manutenzione, ripristino in seguito). | 
| **SKU con prestazioni elevate/ErGw2AZ** | 14.000 | 840.000 | >2.000 | 250.000 | 1 Gbps | 500 | ~ 9.500 (ridurre a 4.000 se nella rete virtuale sono presenti più di 6.500 macchine virtuali). | 4\.500 |
| **SKU con prestazioni extra/ErGw3AZ** | 16.000 | 950.000 | ~ 10.000 | 1\.000.000 | 1 Gbps | 500 | ~ 9.500 | 11.000 |
