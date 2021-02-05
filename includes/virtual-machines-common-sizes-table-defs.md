---
title: includere file
description: includere file
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580213"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano i dischi misurati in GB (1000 ^ 3 byte) con dischi misurati in GiB (1024 ^ 3), tenere presente che i numeri di capacità specificati in GiB possono apparire più piccoli. Ad esempio, 1023 GiB = 1098,4 GB.
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- I dischi dati possono operare in modalità memorizzata nella cache o non memorizzata nella cache. Per il funzionamento dei dischi dati memorizzati nella cache, la modalità di cache host è impostata su **ReadOnly** o su **ReadWrite**.  Per il funzionamento dei dischi dati non memorizzati nella cache, la modalità di cache host è impostata su **None**.
- Per informazioni su come ottenere le migliori prestazioni di archiviazione per le macchine virtuali, vedere [prestazioni delle macchine virtuali e dei dischi](../articles/virtual-machines/disks-performance.md).
- La **larghezza di banda della rete prevista** è la larghezza di banda aggregata massima allocata per ogni tipo di macchina virtuale in tutte le schede di interfaccia di rete, per tutte le destinazioni. Per altre informazioni, vedere [larghezza di banda di rete della macchina virtuale](../articles/virtual-network/virtual-machine-network-throughput.md).

  I limiti massimi non sono garantiti. I limiti offrono indicazioni per la selezione del tipo di macchina virtuale appropriato per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da diversi fattori, tra cui la congestione della rete, i carichi delle applicazioni e le impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [ottimizzare la velocità effettiva della rete per le macchine virtuali di Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Per ottenere le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la macchina virtuale. Per ulteriori informazioni, vedere la pagina relativa al [test della larghezza di banda/velocità effettiva (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



