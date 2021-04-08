---
title: Eliminare un account RunAs di automazione di Azure
description: Questo articolo descrive come eliminare un account RunAs con PowerShell o dalla portale di Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055894"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Eliminare un account RunAs di automazione di Azure

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse nel modello di distribuzione Azure Resource Manager o di Azure classico usando manuali operativi di automazione e altre funzionalità di automazione. Questo articolo descrive come eliminare un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs, è possibile ricrearlo nel portale di Azure o con lo script di PowerShell fornito.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

3. Nella pagina delle proprietà dell'account RunAs selezionare l'account RunAs o l'account RunAs classico che si desidera eliminare.

4. Nel riquadro Proprietà per l'account selezionato fare quindi clic su **Elimina**.

   ![Eliminare un account RunAs](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="next-steps"></a>Passaggi successivi

Per ricreare l'account RunAs o l'account RunAs classico, vedere [creare account RunAs](create-run-as-account.md).