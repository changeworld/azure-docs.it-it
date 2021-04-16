---
title: Abilitare l'estensione vm da portale di Azure
description: Questo articolo descrive come distribuire estensioni di macchina virtuale Azure Arc server abilitati in esecuzione in ambienti cloud ibridi dal portale di Azure.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388279"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Abilitare le estensioni vm di Azure dal portale di Azure

Questo articolo illustra come distribuire e disinstallare le estensioni di macchine virtuali di Azure, supportate dai server abilitati per Azure Arc, in un computer ibrido Linux o Windows tramite il portale di Azure.

> [!NOTE]
> L'Key Vault macchina virtuale (anteprima) non supporta la distribuzione dal portale di Azure, solo usando l'interfaccia della riga di comando di Azure, il Azure PowerShell o un modello Azure Resource Manager.

> [!NOTE]
> Azure Arc server abilitati non supporta la distribuzione e la gestione delle estensioni di macchina virtuale nelle macchine virtuali di Azure. Per le macchine virtuali di Azure, vedere l'articolo panoramica [dell'estensione vm](../../virtual-machines/extensions/overview.md) seguente.

## <a name="enable-extensions-from-the-portal"></a>Abilitare le estensioni dal portale

Le estensioni della macchina virtuale possono essere applicate al computer gestito da Arc per server tramite il portale di Azure.

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Nel portale passare a **Server - Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **Estensioni** e quindi **Aggiungi**. Scegliere l'estensione desiderata dall'elenco di quelle disponibili e quindi seguire le istruzioni della procedura guidata. In questo esempio si distribuirà l'estensione della macchina virtuale Log Analytics.

    ![Selezionare l'estensione vm per il computer selezionato](./media/manage-vm-extensions/add-vm-extensions.png)

    L'esempio seguente illustra l'installazione dell'estensione di macchina virtuale Log Analytics dal portale di Azure:

    ![Installare l'estensione vm di Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Per completare l'installazione, è necessario specificare l'ID dell'area di lavoro e la chiave primaria. Se non si ha familiarità con come trovare queste informazioni, vedere Ottenere l'ID e [la chiave dell'area di lavoro.](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

4. Dopo aver confermato le informazioni necessarie fornite, selezionare **Crea**. Viene visualizzato un riepilogo della distribuzione ed è possibile esaminare lo stato della distribuzione.

>[!NOTE]
>Anche se più estensioni possono essere riunite ed elaborate in batch, vengono installate in serie. Al termine dell'installazione della prima estensione, viene tentata l'installazione dell'estensione successiva.

## <a name="list-extensions-installed"></a>Elencare le estensioni installate

È possibile ottenere un elenco delle estensioni di macchina virtuale nel server abilitato per Arc dal portale di Azure. Seguire questa procedura per visualizzarli.

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Nel portale passare a **Server - Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **Estensioni** e viene restituito l'elenco delle estensioni installate.

    ![Elencare l'estensione vm distribuita nel computer selezionato](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Disinstallare l'estensione

È possibile rimuovere una o più estensioni da un server abilitato per Arc dalla portale di Azure. Per rimuovere un'estensione, seguire questa procedura.

1. Nel browser passare al [portale di Azure](https://portal.azure.com).

2. Nel portale passare a **Server - Azure Arc** e selezionare il computer ibrido nell'elenco.

3. Scegliere **Estensioni** e quindi selezionare un'estensione dall'elenco delle estensioni installate.

4. Selezionare **Disinstalla** e, quando viene richiesto di verificare, selezionare **Sì** per continuare.

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere estensioni di macchina virtuale usando l'interfaccia della riga di comando di [Azure,](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md)o [Azure Resource Manager modelli.](manage-vm-extensions-template.md)

- Le informazioni sulla risoluzione dei problemi sono disponibili nella Guida alla risoluzione [dei problemi delle estensioni di macchina virtuale](troubleshoot-vm-extensions.md).