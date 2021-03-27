---
title: Gestire i criteri di sottoscrizione di Azure
description: Informazioni su come gestire i criteri di sottoscrizione di Azure per controllare lo spostamento di sottoscrizioni di Azure da e verso le directory.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631862"
---
# <a name="manage-azure-subscription-policies"></a>Gestire i criteri di sottoscrizione di Azure

>[!NOTE]
>Questa funzionalità è attualmente in fase di anteprima e verrà implementata gradualmente, quindi non tutti potranno vedere questa esperienza sul portale di Azure.

Questo articolo consente di configurare i criteri di sottoscrizione di Azure per le operazioni di sottoscrizione per controllare lo spostamento delle sottoscrizioni di Azure da e verso le directory.

## <a name="prerequisites"></a>Prerequisiti

- Solo gli [amministratori globali](../../active-directory/roles/permissions-reference.md#global-administrator) di directory possono modificare i criteri di sottoscrizione. Prima di modificare i criteri di sottoscrizione, l'amministratore globale deve [elevare l'accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](../../role-based-access-control/elevate-access-global-admin.md). Possono quindi modificare i criteri di sottoscrizione.
- Tutti gli altri utenti possono leggere solo l'impostazione dei criteri corrente.

## <a name="available-subscription-policy-settings"></a>Impostazioni dei criteri di sottoscrizione disponibili

Usare le impostazioni dei criteri seguenti per controllare lo spostamento delle sottoscrizioni di Azure da e verso le directory.

### <a name="subscriptions-leaving-aad-directory"></a>Sottoscrizioni che lasciano la directory di AAD

Il criterio consente o impedisce agli utenti di trasferire le sottoscrizioni fuori dalla directory corrente. I [proprietari delle sottoscrizioni](../../role-based-access-control/built-in-roles.md#owner) possono [modificare la directory di una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) in un'altra posizione in cui sono membri. Pone problemi di governance, quindi gli amministratori globali possono consentire o impedire agli utenti della directory di modificare la directory.

### <a name="subscriptions-entering-aad-directory"></a>Sottoscrizioni che accedono alla directory di AAD

Il criterio consente o arresta gli utenti di altre directory, che hanno accesso alla directory corrente, per spostare le sottoscrizioni nella directory corrente. I [proprietari delle sottoscrizioni](../../role-based-access-control/built-in-roles.md#owner) possono [modificare la directory di una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) in un'altra posizione in cui sono membri. Pone problemi di governance, quindi gli amministratori globali possono consentire o impedire agli utenti della directory di modificare la directory.

### <a name="exempted-users"></a>Utenti esentati

Per motivi di governance, gli amministratori globali possono bloccare tutte le directory di sottoscrizione spostate all'esterno della directory corrente. Tuttavia, potrebbe essere necessario consentire a utenti specifici di eseguire le operazioni. In entrambi i casi, è possibile configurare un elenco di utenti esentati che consente agli utenti di ignorare l'impostazione dei criteri applicabile a tutti gli altri.

## <a name="setting-subscription-policy"></a>Impostazione dei criteri di sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare a **sottoscrizioni**. I **criteri di gestione** vengono visualizzati sulla barra dei comandi.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Screenshot che mostra i criteri di gestione nelle sottoscrizioni." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Selezionare **Gestisci criteri** per visualizzare i dettagli relativi ai criteri di sottoscrizione attuali impostati per la directory. Un amministratore globale con [autorizzazioni elevate](../../role-based-access-control/elevate-access-global-admin.md) può apportare modifiche alle impostazioni, tra cui l'aggiunta o la rimozione di utenti esentati.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Screenshot che mostra le impostazioni specifiche dei criteri e gli utenti esentati." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Selezionare **Save Changes (Salva modifiche** ) nella parte inferiore per salvare le modifiche. Le modifiche sono effettive immediatamente.

## <a name="read-subscription-policy"></a>Legge i criteri di sottoscrizione

Gli amministratori non globali possono comunque accedere all'area dei criteri di sottoscrizione per visualizzare le impostazioni dei criteri della directory. Non possono apportare modifiche. Non possono visualizzare l'elenco degli utenti esentati per motivi di privacy. Possono visualizzare gli amministratori globali per inviare richieste di modifiche ai criteri, purché le impostazioni della directory le consentano.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Screenshot che mostra i criteri di gestione nelle sottoscrizioni come Reader." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Passaggi successivi

- Leggere la [documentazione relativa a gestione dei costi e fatturazione](../index.yml)