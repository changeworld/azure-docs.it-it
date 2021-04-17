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
ms.openlocfilehash: e8ec7faf1562381288aeef630bf2076ce413017a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531514"
---
Tutte le dimensioni delle macchine virtuali di ultima generazione supportano la crittografia nell'host:

|Tipo  |Non supportato  |Supportato  |
|---------|---------|---------|
|Utilizzo generico     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4        |
|Con ottimizzazione per il calcolo     |         | Fsv2        |
|Ottimizzate per la memoria     | Ev3        | DSv2, Esv3, M, Mv2, Eav4, Easv4        |
|Ottimizzate per l'archiviazione     |         | Ls, Lsv2 (dischi NVMe non crittografati)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (anteprima)        |
|High Performance Computing (HPC)     | H        | HB, HC, HBv2        |
|Generazioni precedenti     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
