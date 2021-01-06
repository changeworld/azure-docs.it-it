---
title: Rimuovere VM da Gestione aggiornamenti di Automazione di Azure
description: Questo articolo descrive come rimuovere i computer gestiti con Gestione aggiornamenti.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913193"
---
# <a name="remove-vms-from-update-management"></a>Rimuovere macchine virtuali da Gestione aggiornamenti

Al termine della gestione degli aggiornamenti nelle VM nell'ambiente in uso, è possibile arrestare la gestione delle VM con la funzionalità [Gestione aggiornamenti](overview.md) . Per arrestarne la gestione, modificare la query di ricerca salvata `MicrosoftDefaultComputerGroup` nell'area di lavoro log Analytics collegata all'account di automazione.

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Per rimuovere le macchine virtuali

1. Nel portale di Azure avviare **cloud Shell** dalla parte superiore della portale di Azure. Se non si ha familiarità con Azure Cloud Shell, vedere [Panoramica di Azure cloud Shell](../../cloud-shell/overview.md).

2. Usare il comando seguente per identificare l'UUID di un computer che si vuole rimuovere dalla gestione.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Nella portale di Azure passare a **log Analytics aree di lavoro**. Selezionare l'area di lavoro dall'elenco.

4. Nell'area di lavoro Log Analytics selezionare **Impostazioni avanzate** , quindi scegliere **gruppi di computer** dal menu a sinistra.

5. Da **gruppi di computer** nel riquadro di destra selezionare **gruppi salvati**.

6. Dalla tabella, per gli aggiornamenti della query di ricerca salvata **: MicrosoftDefaultComputerGroup**, fare clic sull'icona **Visualizza membri** per eseguire e visualizzare i relativi membri.

7. Nell'editor di query esaminare la query e trovare l'UUID per la macchina virtuale. Rimuovere l'UUID per la macchina virtuale e ripetere i passaggi per qualsiasi altra VM che si vuole rimuovere.

8. Salvare la ricerca salvata al termine della modifica selezionando **Salva** nella barra superiore. Quando richiesto, specificare quanto segue:

    * **Nome**: MicrosoftDefaultComputerGroup
    * **Salva con nome**: funzione
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Categoria**: aggiornamenti

>[!NOTE]
>Le macchine virtuali vengono comunque visualizzate dopo aver annullato la registrazione, perché si segnalano tutti i computer valutati nelle ultime 24 ore. Dopo aver rimosso il computer, è necessario attendere 24 ore prima che non vengano più elencate.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare di nuovo la gestione della macchina virtuale, vedere [abilitare Gestione aggiornamenti esplorando il portale di Azure](enable-from-portal.md) o [abilitare Gestione aggiornamenti da una macchina virtuale di Azure](enable-from-vm.md).