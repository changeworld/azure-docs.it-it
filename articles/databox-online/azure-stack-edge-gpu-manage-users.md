---
title: Gestire gli utenti di Azure Stack Edge Pro GPU | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire gli utenti nella GPU di Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 5aeb4f2d7152120ea92a8df76f980eb3baed6e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638547"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Usare il portale di Azure per gestire gli utenti in Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come gestire gli utenti in Azure Stack Edge Pro. È possibile gestire il Azure Stack Edge Pro tramite la portale di Azure o tramite l'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, modificare o eliminare utenti.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Aggiungere un utente
> * Modificare un utente
> * Eliminare un utente

## <a name="about-users"></a>Informazioni sugli utenti

Gli utenti possono accedere in sola lettura o con privilegi completi. Gli utenti di sola lettura possono visualizzare solo i dati della condivisione. Gli utenti con privilegi completi possono leggere i dati della condivisione, scrivere in queste condivisioni e modificare o eliminare i dati della condivisione.

 - **Utente con privilegi completi**: un utente locale con accesso completo.
 - **Utente di sola lettura**. un utente locale con accesso di sola lettura. Questi utenti sono associati alle condivisioni che consentono operazioni di sola lettura.

Le autorizzazioni dell'utente vengono prima di tutto definite durante la creazione contemporanea di utente e condivisione. Possono essere modificati usando Esplora file.


## <a name="add-a-user"></a>Aggiungere un utente

Seguire questa procedura nel portale di Azure per aggiungere un utente.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **utenti**. Selezionare **+ Aggiungi utente** sulla barra dei comandi.

    ![Selezionare Aggiungi utente](media/azure-stack-edge-gpu-manage-users/add-user-1.png)

2. Specificare nome utente e password per l'utente da aggiungere. Confermare la password e selezionare **Aggiungi**.

    ![Specificare nome utente e password](media/azure-stack-edge-gpu-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Questi utenti sono riservati dal sistema e non deve essere usati: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Dopo l'avvio e il completamento della creazione dell'utente viene visualizzata una notifica. Dopo aver creato l'utente, dalla barra dei comandi, selezionare **Aggiorna** per visualizzare l'elenco aggiornato degli utenti.


## <a name="modify-user"></a>Modificare un utente

È possibile cambiare la password associata a un utente dopo averlo creato. Selezionare dall'elenco degli utenti. Immettere e confermare la nuova password. Salvare le modifiche.

![Modificare un utente](media/azure-stack-edge-gpu-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Eliminare un utente

Eseguire i passaggi seguenti nel portale di Azure per eliminare un utente.


1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **utenti**.

    ![Selezionare l'utente da eliminare](media/azure-stack-edge-gpu-manage-users/delete-user-1.png)

2. Selezionare un utente dall'elenco e quindi fare clic su **Elimina**. Quando richiesto, confermare l'eliminazione.

    ![Selezionare l'utente per eliminare 2](media/azure-stack-edge-gpu-manage-users/delete-user-2.png)

L'elenco di utenti viene aggiornato per riflettere l'utente eliminato.

![Elenco aggiornato degli utenti](media/azure-stack-edge-gpu-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire la larghezza di banda](azure-stack-edge-gpu-manage-bandwidth-schedules.md).
