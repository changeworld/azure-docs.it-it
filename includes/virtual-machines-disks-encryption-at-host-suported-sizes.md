---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732223"
---
Tutte le dimensioni delle macchine virtuali di ultima generazione supportano la crittografia nell'host:

|Type  |Non supportato  |Supportato  |
|---------|---------|---------|
|Utilizzo generico     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4, Ddv4, Ddsv4       |
|Con ottimizzazione per il calcolo     |         | Fsv2        |
|Ottimizzate per la memoria     | Ev3        | Esv3, M, Mv2, Eav4, Easv4, Edv4, Edsv4        |
|Con ottimizzazione per l'archiviazione     |         | Ls, Lsv2 (dischi NVMe non crittografati)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (anteprima)        |
|High Performance Computing (HPC)     | H        | HB, HC, HBv2        |
|Generazioni precedenti     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
