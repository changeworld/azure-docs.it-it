---
title: Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743763"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configurare gli avvisi di sicurezza per i ruoli delle risorse di Azure in Privileged Identity Management

Privileged Identity Management (PIM) genera avvisi in caso di attività sospette o non sicure nell'organizzazione di Azure Active Directory (Azure AD). Una volta attivato, un avviso viene visualizzato nella pagina Avvisi.

![Risorse di Azure-pagina avvisi che elenca l'avviso, il livello di rischio e il conteggio](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Esaminare gli avvisi

Selezionare un avviso per visualizzare un report in cui sono elencati gli utenti o i ruoli che hanno attivato l'avviso, oltre alle indicazioni per la correzione.

![Segnalazione avvisi che mostra l'ora dell'ultima analisi, la descrizione, i passaggi di mitigazione, il tipo, la gravità, l'effetto sulla sicurezza e come evitare la prossima volta](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Avvisi

| Avviso | Gravità | Trigger | Recommendation |
| --- | --- | --- | --- |
| **Troppi proprietari assegnati a una risorsa** |Medio |Troppi utenti hanno il ruolo di proprietario. |Controllare gli utenti nell'elenco e riassegnarne alcuni a ruoli con privilegi meno elevati. |
| **Troppi proprietari permanenti assegnati a una risorsa** |Medio |Troppi utenti sono assegnati in modo permanente a un ruolo. |Controllare gli utenti nell'elenco e riassegnarne alcuni per richiedere l'attivazione per l'uso dei ruoli. |
| **È stato creato un ruolo duplicato** |Medio |Più ruoli hanno gli stessi criteri. |Usare solo uno di questi ruoli. |

### <a name="severity"></a>Gravità

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri. 
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

Dalla pagina Avvisi passare a **Impostazioni**.

![Pagina avvisi con impostazioni evidenziate](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.

![Impostazione della pagina per un avviso per abilitare e configurare le impostazioni](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo delle risorse di Azure in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
