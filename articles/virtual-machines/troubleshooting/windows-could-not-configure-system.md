---
title: Risolvere il problema che impedisce a Windows di completare la configurazione del sistema
titlesuffix: Azure Virtual Machines
description: Questo articolo illustra la procedura per risolvere i problemi in cui il processo Sysprep impedisce l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629590"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Risolvere il problema che impedisce a Windows di completare la configurazione del sistema

Questo articolo illustra la procedura per risolvere i problemi in cui il processo Sysprep impedisce l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che nella schermata viene visualizzato un errore di installazione di Windows durante l'avvio di servizi di installazione di Windows. L'errore visualizzerà il messaggio:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![Nella figura 1 viene visualizzato un errore di installazione di Windows con il messaggio: "Impossibile completare la configurazione del sistema da parte di Windows. Per tentare di riprendere la configurazione, riavviare il computer. Avvio dei servizi in corso...](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Causa

Questo errore si verifica quando il sistema operativo non è in grado di completare il [processo Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Questo errore si verifica quando si tenta un avvio iniziale di una macchina virtuale generalizzata. Se si verifica questo problema, ricreare l'immagine generalizzata, poiché l'immagine si trova in uno stato non distribuibile e non può essere ripristinata.

## <a name="solution"></a>Soluzione

> [!TIP]
> Se si dispone di un backup recente della macchina virtuale, è possibile provare a [ripristinare la macchina virtuale dal backup](../../backup/backup-azure-arm-restore-vms.md) per correggere il problema di avvio.

Per risolvere questo problema, seguire le [istruzioni di Azure per preparare/acquisire un'immagine](../windows/upload-generalized-managed.md) e preparare una nuova immagine generalizzata.