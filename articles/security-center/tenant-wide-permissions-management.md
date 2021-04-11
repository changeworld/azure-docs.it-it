---
title: Concedere e richiedere autorizzazioni a livello di tenant nel centro sicurezza di Azure
description: Informazioni su come gestire le autorizzazioni a livello di tenant nel centro sicurezza di Azure
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617489"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Concedere e richiedere visibilità a livello di tenant

Un utente con il ruolo di Azure Active Directory (AD) dell' **amministratore globale** potrebbe avere responsabilità a livello di tenant, ma non dispone delle autorizzazioni di Azure per visualizzare le informazioni a livello di organizzazione nel centro sicurezza di Azure. L'elevazione delle autorizzazioni è necessaria perché le assegnazioni di ruolo Azure AD non concedono l'accesso alle risorse di Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Concedere autorizzazioni a livello di tenant a se stessi

Per assegnare autorizzazioni a livello di tenant:

1. Se l'organizzazione gestisce l'accesso alle risorse con [Azure ad Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)o qualsiasi altro strumento PIM, il ruolo di amministratore globale deve essere attivo per l'utente attenendosi alla procedura riportata di seguito.

1. In qualità di utente amministratore globale senza assegnazione sul gruppo di gestione radice del tenant, aprire la pagina **Panoramica** del Centro sicurezza e selezionare il collegamento **visibilità a livello di tenant** nel banner. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Abilitare le autorizzazioni a livello di tenant nel centro sicurezza di Azure":::

1. Selezionare il nuovo ruolo di Azure da assegnare. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Modulo per la definizione delle autorizzazioni a livello di tenant da assegnare all'utente":::

    > [!TIP]
    > Il ruolo di amministratore della sicurezza è generalmente necessario per applicare i criteri a livello di radice, mentre il ruolo con autorizzazioni di lettura per la sicurezza è sufficiente per garantire la visibilità a livello di tenant. Per altre informazioni sulle autorizzazioni concesse da questi ruoli, vedere la [Descrizione del ruolo predefinito di amministratore della sicurezza](../role-based-access-control/built-in-roles.md#security-admin) o la [Descrizione del ruolo con autorizzazioni di lettura per la sicurezza predefinito ](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Per le differenze tra questi ruoli specifici del Centro sicurezza, vedere la tabella in [ruoli e azioni consentite](security-center-permissions.md#roles-and-allowed-actions).

    La visualizzazione a livello di organizzazione viene eseguita concedendo i ruoli a livello del gruppo di gestione radice del tenant.  

1. Disconnettersi dal portale di Azure, quindi riconnettersi.

1. Dopo avere ottenuto l'accesso con privilegi elevati, aprire o aggiornare il Centro sicurezza di Azure per verificare di avere visibilità su tutte le sottoscrizioni nel tenant di Azure AD. 

Il processo semplice precedente esegue automaticamente una serie di operazioni:

1. Le autorizzazioni dell'utente sono temporaneamente elevate.
1. Usando le nuove autorizzazioni, l'utente viene assegnato al ruolo Azure RBAC desiderato nel gruppo di gestione radice.
1. Le autorizzazioni elevate vengono rimosse.

Per altri dettagli sul processo di elevazione dei Azure AD, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure](../role-based-access-control/elevate-access-global-admin.md).


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Richiedere autorizzazioni a livello di tenant quando il proprio non è sufficiente

Se si accede al centro sicurezza e viene visualizzato un banner che informa che la visualizzazione è limitata, è possibile fare clic per inviare una richiesta all'amministratore globale dell'organizzazione. Nella richiesta è possibile includere il ruolo che si desidera assegnare e l'amministratore globale deciderà quale ruolo concedere. 

L'amministratore globale decide se accettare o rifiutare queste richieste. 

> [!IMPORTANT]
> È possibile inviare una richiesta ogni sette giorni.

Per richiedere autorizzazioni elevate all'amministratore globale:

1. Dal portale di Azure aprire il Centro sicurezza di Azure.

1. Se viene visualizzato il banner "vengono visualizzate informazioni limitate". selezionarlo.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner che informa un utente che può richiedere autorizzazioni a livello di tenant.":::

1. Nel modulo di richiesta dettagliata selezionare il ruolo desiderato e la giustificazione per il motivo per cui sono necessarie queste autorizzazioni.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Pagina dei dettagli per la richiesta di autorizzazioni a livello di tenant da amministratore globale di Azure":::

1. Selezionare **Richiedi accesso**.

    Un messaggio di posta elettronica viene inviato all'amministratore globale. Il messaggio di posta elettronica contiene un collegamento al centro sicurezza in cui è possibile approvare o rifiutare la richiesta.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Invia un messaggio di posta elettronica all'amministratore globale per le nuove autorizzazioni":::

    Quando l'amministratore globale seleziona **Controlla la richiesta** e completa il processo, la decisione viene inviata tramite posta elettronica all'utente richiedente. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle autorizzazioni del Centro sicurezza sono disponibili nella pagina correlata seguente:

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)