---
title: Disabilitare l Automazione di Azure'identità gestita dell'account utente (anteprima)
description: Questo articolo illustra come disabilitare e rimuovere un'identità gestita per un account Automazione di Azure servizio.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519273"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Disabilitare l Automazione di Azure'identità gestita dell'account utente (anteprima)

Esistono due modi per disabilitare un'identità assegnata dal sistema in Automazione di Azure. È possibile completare questa attività dal portale di Azure o usando un modello di Azure Resource Manager (ARM).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Disabilitare l'identità gestita nel portale di Azure

È possibile disabilitare l'identità gestita dal portale di Azure indipendentemente dalla configurazione originale dell'identità gestita.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'account di Automazione e selezionare **Identità in** **Impostazioni account.**

1. Impostare **l'opzione Assegnata** dal sistema **su Disattivato** e premere **Salva**. Quando viene richiesto di confermare, fare clic su **Sì.**

L'identità gestita viene rimossa e non ha più accesso alla risorsa di destinazione.

## <a name="disable-using-azure-resource-manager-template"></a>Disabilitare l'Azure Resource Manager modello

Se è stata creata l'identità gestita per l'account di Automazione usando un modello Azure Resource Manager, è possibile disabilitare l'identità gestita riutilizzando il modello e modificandone le impostazioni. Impostare il tipo della proprietà figlio dell'oggetto identità su **Nessuno,** come illustrato nell'esempio seguente, quindi eseguire nuovamente il modello.

```json
"identity": { 
   "type": "None" 
} 
```

La rimozione di un'identità assegnata dal sistema tramite questo metodo la elimina anche Azure AD. Anche le identità assegnate dal sistema vengono rimosse automaticamente Azure AD quando viene eliminata la risorsa dell'app a cui sono assegnate.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'abilitazione dell'identità gestita in Automazione di Azure, vedere Abilitare e usare [l'identità gestita per Automazione (anteprima).](enable-managed-identity-for-automation.md)

- Per una panoramica della sicurezza degli account di Automazione, vedere Panoramica [dell'autenticazione degli account di Automazione.](automation-security-overview.md)
