---
title: Procedure consigliate per RBAC di Azure
description: Procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555564"
---
# <a name="best-practices-for-azure-rbac"></a>Procedure consigliate per RBAC di Azure

Questo articolo descrive alcune procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Queste procedure consigliate derivano dall'esperienza con il controllo degli accessi in base al ruolo di Azure e dalle esperienze dei clienti.

## <a name="only-grant-the-access-users-need"></a>Concedi solo agli utenti di accesso necessità

Usando il controllo degli accessi in base al ruolo di Azure, è possibile separare le mansioni all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il loro lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni in un particolare ambito.

Quando si pianifica la strategia di controllo degli accessi, è consigliabile concedere agli utenti almeno il privilegio per completare la propria sessione di lavoro. Evitare di assegnare ruoli più ampi in ambiti più ampi anche se inizialmente sembra più pratico. Quando si creano ruoli personalizzati, includere solo le autorizzazioni necessarie agli utenti. Limitando i ruoli e gli ambiti, si limitano le risorse a rischio se l'entità di sicurezza non viene mai compromessa.

Il diagramma seguente illustra un modello consigliato per l'uso di RBAC di Azure.

![RBAC di Azure e privilegi minimi](./media/best-practices/rbac-least-privilege.png)

Per informazioni su come assegnare i ruoli, vedere [assegnare i ruoli di Azure usando il portale di Azure](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitare il numero di proprietari di sottoscrizioni

È necessario disporre di un massimo di 3 proprietari di sottoscrizioni per ridurre il rischio di violazione da parte di un proprietario compromesso. Questa raccomandazione può essere monitorata nel centro sicurezza di Azure. Per altre indicazioni relative a identità e accesso nel centro sicurezza, vedere [raccomandazioni sulla sicurezza: Guida di riferimento](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>USA Azure AD Privileged Identity Management

Per proteggere gli account con privilegi da attacchi informatici, è possibile usare Azure Active Directory Privileged Identity Management (PIM) per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità nel loro uso tramite report e avvisi. PIM aiuta a proteggere gli account con privilegi offrendo un accesso con privilegi JIT per Azure AD e risorse di Azure. L'accesso può essere associato al tempo trascorso il quale i privilegi vengono revocati automaticamente. 

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

## <a name="assign-roles-to-groups-not-users"></a>Assegnare ruoli a gruppi, non a utenti

Per rendere più gestibili le assegnazioni di ruolo, evitare di assegnare ruoli direttamente agli utenti. Assegnare invece ruoli ai gruppi. L'assegnazione di ruoli a gruppi anziché a utenti consente inoltre di ridurre al minimo il numero di assegnazioni di ruolo, che prevede un [limite di 2.000 assegnazioni di ruolo per sottoscrizione](troubleshooting.md#azure-role-assignments-limit). 

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
