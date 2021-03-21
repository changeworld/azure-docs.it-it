---
title: Sostituire il dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto come ottenere una sostituzione Azure Stack dispositivo Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443472"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Sostituire il dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come sostituire il dispositivo Azure Stack Edge Pro. Un dispositivo sostitutivo è necessario quando il dispositivo esistente presenta un errore hardware o necessita di un aggiornamento. 


In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Aprire un ticket di supporto per problemi hardware
> * Creare un nuovo ordine per un dispositivo sostitutivo nel portale di Azure
> * Installare, attivare il dispositivo sostitutivo
> * Restituisce il dispositivo originale

## <a name="open-a-support-ticket"></a>Aprire un ticket di supporto

Se si verifica un errore hardware nel dispositivo esistente, aprire un ticket di supporto attenendosi alla procedura seguente:

1. Aprire un ticket di supporto con il supporto tecnico Microsoft indicando che si vuole restituire il dispositivo. Selezionare il tipo di problema **hardware Azure stack Edge Pro** e scegliere il sottotipo **problemi hardware** .  

    ![Aprire il ticket di supporto](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Un tecnico supporto tecnico Microsoft si contatterà per determinare se un'unità di sostituzione dei campi (FRU) può risolvere il problema ed è disponibile per questa istanza. Se una FRU non è disponibile o se il dispositivo necessita di un aggiornamento hardware, il supporto ti guiderà per inserire un nuovo ordine e restituire il vecchio dispositivo.

## <a name="create-a-new-order"></a>Crea un nuovo ordine

Creare una nuova risorsa per l'attivazione del dispositivo sostitutivo attenendosi alla procedura illustrata in [creare una nuova risorsa](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

> [!NOTE]
> L'attivazione di un dispositivo sostitutivo rispetto a una risorsa esistente non è supportata. La nuova risorsa viene considerata un nuovo ordine. Si inizierà a fatturare 14 giorni dopo la consegna del dispositivo.

## <a name="install-and-activate-the-replacement-device"></a>Installare e attivare il dispositivo sostitutivo

Attenersi alla seguente procedura per installare e attivare il dispositivo sostitutivo:

1. [Installare il dispositivo](azure-stack-edge-deploy-install.md).
2. [Attivare il dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) con la nuova risorsa creata in precedenza.

## <a name="return-your-existing-device"></a>Restituisce il dispositivo esistente

Seguire tutti i passaggi per restituire il dispositivo originale:

1. [Cancellare i dati contenuti nel dispositivo](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Avviare il ritorno del dispositivo](azure-stack-edge-return-device.md#initiate-device-return) per il dispositivo originale.
3. [Pianificare un ritiro](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Una volta che il dispositivo è stato ricevuto in Microsoft, è possibile [eliminare la risorsa](azure-stack-edge-return-device.md#delete-the-resource) associata al dispositivo restituito.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [restituire un dispositivo Azure stack Edge Pro](azure-stack-edge-return-device.md).
