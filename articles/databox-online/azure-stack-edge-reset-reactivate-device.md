---
title: Reimpostare e riattivare il dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Informazioni su come eliminare i dati da e quindi riattivare il dispositivo Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745843"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Reimpostare e riattivare il dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come reimpostare, riconfigurare e riattivare un dispositivo Azure Stack Edge Pro se si verificano problemi con il dispositivo o se è necessario avviare una nuova operazione per altri motivi.

Dopo aver reimpostato il dispositivo per rimuovere i dati, sarà necessario riattivare il dispositivo come una nuova risorsa. La reimpostazione di un dispositivo rimuove la configurazione del dispositivo, quindi è necessario riconfigurare il dispositivo tramite l'interfaccia utente Web locale.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Cancellare i dati dai dischi dati del dispositivo
> * Riattivare il dispositivo mediante la creazione di un nuovo ordine, la riconfigurazione del dispositivo e l'attivazione

## <a name="reset-data-from-the-device"></a>Reimposta i dati dal dispositivo

Per cancellare i dati dai dischi dati del dispositivo, è necessario reimpostare il dispositivo. 

Prima di procedere con la reimpostazione, creare una copia dei dati locali del dispositivo, se necessario. È possibile copiare i dati del dispositivo in un contenitore di Archiviazione di Azure.

>[!IMPORTANT]
> Se si ripristina il dispositivo, tutti i dati e i carichi di lavoro locali verranno cancellati dal dispositivo e non sarà possibile annullarlo. Ripristinare il dispositivo solo se si vuole avviare un nuovo tentativo con il dispositivo.

È possibile reimpostare il dispositivo nell'interfaccia utente Web locale o in PowerShell. Per istruzioni su PowerShell, vedere [reimpostare il dispositivo](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[! INCLUDi] [Reimposta i dati dal dispositivo](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Riattivare il dispositivo

Dopo aver reimpostato il dispositivo, sarà necessario riattivare il dispositivo come una nuova risorsa. Dopo aver inserito un nuovo ordine, sarà necessario riconfigurare e riattivare la nuova risorsa.

Per riattivare il dispositivo esistente, attenersi alla procedura seguente:

1. Per creare un nuovo ordine per il dispositivo esistente, seguire la procedura descritta in [creare una nuova risorsa](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource). Nella scheda **indirizzo di spedizione** selezionare **ho già un dispositivo**.

   ![Specificare nessun nuovo dispositivo nell'indirizzo di spedizione](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Ottenere la chiave di attivazione](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [Connettersi al dispositivo](azure-stack-edge-gpu-deploy-connect.md).

1. [Configurare la rete per il dispositivo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Configurare le impostazioni del dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Configurare i certificati](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Attivare il dispositivo](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [connettersi a un dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md).
