---
title: Supporto per i sistemi operativi a 32 bit nelle macchine virtuali di Azure | Microsoft Docs
description: Informazioni sui sistemi operativi supportati in macchine virtuali di Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210189"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure

Microsoft Azure consente ora agli utenti di portare i sistemi operativi Windows a 32 bit in Azure. Sono supportati solo i dischi rigidi virtuali specializzati e le immagini generalizzate non funzioneranno in Azure. Poiché alcuni di questi sistemi operativi hanno già raggiunto la scadenza del contratto di supporto, Microsoft potrebbe non offrire un supporto aggiuntivo. Il supporto non è inoltre disponibile per i sistemi operativi basati su Linux o Berkeley Software Distribution (BSD) eseguiti in una macchina virtuale Microsoft Azure (VM).

> [!NOTE]
> La piattaforma Azure presenta una limitazione dello spazio degli indirizzi di memoria imposta sulle macchine virtuali che eseguono sistemi operativi a 32 bit in cui è possibile rendere disponibili solo 1 GB di memoria per la macchina virtuale (*soprattutto su SKU client come Win7 o WIN10*) e il resto della memoria per la macchina virtuale verrà visualizzato come riservato all'interno della macchina virtuale guest. Si tratta di un problema noto e attualmente non è disponibile un ETA per una correzione. È consigliabile eseguire il passaggio a versioni del sistema operativo a 64 bit.
> 

## <a name="more-information"></a>Ulteriori informazioni

Per ulteriori informazioni sui sistemi operativi supportati in macchine virtuali di Azure, vedere gli articoli seguenti della Microsoft Knowledge Base:

* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)
* [Supporto per Linux e la tecnologia open source in Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Riferimenti

* [Scopri di più sugli aggiornamenti della sicurezza estesa gratuiti per Windows Server 2008/R2 in Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Scopri di più sul supporto per immagini specializzate di Windows Server 2008 SP2 a 32 bit in Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Altre informazioni sul supporto per la migrazione di immagini di Windows Server 2008 in Azure con Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Altre informazioni sui sistemi operativi supportati dall'estensione di Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Altre informazioni sull'esecuzione di Windows Server 2003 in Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/).

In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
