---
title: Gestire le sottoscrizioni
description: Le sottoscrizioni sono costituite da dispositivi gestiti di cui è stato eseguito il commit e possono essere caricati o offboarded, se necessario.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387349"
---
# <a name="manage-a-subscription"></a>Gestire una sottoscrizione

Le sottoscrizioni vengono gestite su base mensile. Quando si esegue l'onboarding di una sottoscrizione, verrà addebitata la sottoscrizione fino alla fine del mese. Analogamente, se offboard una sottoscrizione, ti verrà addebitato il resto del mese in cui offboarded la sottoscrizione.

## <a name="onboard-a-subscription"></a>Onboarding di una sottoscrizione

Per iniziare a usare Azure Defender, è necessario disporre di una sottoscrizione Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un account gratuito. Se si ha già accesso a una sottoscrizione di Azure, ma non è elencato, controllare i dettagli dell'account e confermare le autorizzazioni con il proprietario della sottoscrizione.

Per eseguire l'onboarding di una sottoscrizione:

1. Passare alla pagina dei prezzi del portale di Azure. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Passare alla pagina dei prezzi del portale di Azure.":::

1. Selezionare **onboarding Subscription**.

1. Nella finestra di **sottoscrizione onboarding** selezionare la sottoscrizione e il numero di dispositivi di cui è stato eseguito il commit nei menu a discesa. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Selezionare la sottoscrizione e il numero di dispositivi di cui è stato eseguito il commit.":::

1. Selezionare **onboarding**.

## <a name="offboard-a-subscription"></a>Offboard una sottoscrizione

Le sottoscrizioni vengono gestite su base mensile. Quando offboard una sottoscrizione, ti verrà addebitata la sottoscrizione fino alla fine del mese.

Disinstallare tutti i sensori associati alla sottoscrizione prima di Offboarding la sottoscrizione. Per altre informazioni su come eliminare un sensore, vedere [eliminare un sensore](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor). 

Per offboard una sottoscrizione:

1. Passare alla pagina dei **prezzi** .
1. Selezionare la sottoscrizione e quindi selezionare l'icona **Elimina** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. Nella finestra popup di conferma selezionare la casella di controllo per confermare di avere eliminato tutti i sensori associati alla sottoscrizione.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Selezionare la casella di controllo e selezionare offboard per offboard il sensore.":::

1. Selezionare il pulsante **offboard** . 

## <a name="next-steps"></a>Passaggi successivi

[Attivare e configurare la console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md)
