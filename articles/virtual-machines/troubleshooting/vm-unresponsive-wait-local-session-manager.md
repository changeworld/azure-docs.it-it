---
title: La macchina virtuale non risponde durante l'attesa del servizio Local Session Manager
description: Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest è bloccato in attesa del completamento dell'elaborazione da parte di gestione sessioni locali durante l'avvio di una VM di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632547"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>La macchina virtuale non risponde durante l'attesa del servizio Local Session Manager

Questo articolo illustra la procedura per risolvere i problemi in cui il sistema operativo guest è bloccato in attesa del completamento dell'elaborazione da parte di gestione sessioni locali durante l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare una schermata dell'output della macchina virtuale, si noterà che lo screenshot Visualizza un messaggio di richiesta con un messaggio di attesa per il gestore della sessione locale.

![Screenshot che illustra il sistema operativo guest bloccato in Windows Server 2012 R2, con il messaggio "attendere il gestore della sessione locale".](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Causa

Potrebbero esserci diversi motivi per cui una macchina virtuale è bloccata in attesa di gestione sessioni locali. Se il problema persiste, è necessario raccogliere un dump della memoria per l'analisi.

## <a name="solution"></a>Soluzione

> [!TIP]
> Se si dispone di un backup recente della macchina virtuale, è possibile provare a [ripristinare la macchina virtuale dal backup](../../backup/backup-azure-arm-restore-vms.md) per correggere il problema di avvio.

In alcuni casi, il problema viene risolto semplicemente in attesa del completamento del processo. Se la macchina virtuale non risponde e rimane nella schermata di attesa per più di un'ora, è necessario raccogliere un dump della memoria e quindi contattare il supporto tecnico Microsoft.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Per preparare una macchina virtuale di ripristino, seguire i passaggi 1-3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows sul disco del sistema operativo collegato. Ad esempio, se la lettera di unità assegnata al disco del sistema operativo collegato è denominata *F*, passare a `F:\Windows` .
1. Cercare il file *Memory. dmp* , quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria allegato.
1. Se si verificano problemi durante l'individuazione del file *Memory. dmp* , seguire la guida per [generare un file di dump di arresto anomalo del sistema usando chiamate a interrupt non mascherabili (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Per ulteriori informazioni sulle chiamate a NMI, vedere la guida dell'utente sulle [chiamate a NMI in Azure serial console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere gli errori di avvio della macchina virtuale di Azure](boot-error-troubleshoot.md)