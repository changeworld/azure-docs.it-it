---
title: Organizzare le sottoscrizioni in gruppi di gestione e assegnare ruoli agli utenti per il Centro sicurezza di Azure
description: Informazioni su come organizzare le sottoscrizioni di Azure in gruppi di gestione nel centro sicurezza di Azure e assegnare ruoli agli utenti dell'organizzazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099387"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>Organizzare le sottoscrizioni in gruppi di gestione e assegnare ruoli agli utenti

Questo articolo illustra come gestire il comportamento di sicurezza dell'organizzazione su larga scala applicando criteri di sicurezza a tutte le sottoscrizioni di Azure collegate al tenant di Azure Active Directory.

Per ottenere visibilità sul comportamento di sicurezza di tutte le sottoscrizioni registrate nel tenant di Azure AD, è necessario assegnare un ruolo di Azure con autorizzazioni di lettura sufficienti al gruppo di gestione radice.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organizzare le sottoscrizioni in gruppi di gestione

### <a name="introduction-to-management-groups"></a>Introduzione ai gruppi di gestione

I gruppi di gestione di Azure consentono di gestire in modo efficiente non solo l'accesso, i criteri e la creazione di report sui gruppi di sottoscrizioni, ma anche l'intero spazio di Azure, eseguendo azioni sul gruppo di gestione radice. È possibile organizzare le sottoscrizioni in gruppi di gestione a cui vengono applicati i criteri di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente i criteri applicati al gruppo di gestione. 

A ogni tenant di Azure AD viene assegnato un singolo gruppo di gestione di primo livello denominato **gruppo di gestione radice**. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Questo gruppo consente di applicare i criteri globali e le assegnazioni di ruolo di Azure a livello di directory. 

Il gruppo di gestione radice viene creato automaticamente quando si esegue una delle azioni seguenti: 
- Aprire **gruppi di gestione** nel [portale di Azure](https://portal.azure.com).
- Creare un gruppo di gestione con una chiamata API.
- Si crea un gruppo di gestione con PowerShell. Per istruzioni su PowerShell, vedere [creare gruppi di gestione per la gestione delle risorse e delle organizzazioni](../governance/management-groups/create-management-group-portal.md).

I gruppi di gestione non sono necessari per l'onboarding del Centro sicurezza, ma è consigliabile crearne almeno uno in modo che venga creato il gruppo di gestione radice. Dopo la creazione del gruppo, vi saranno collegate tutte le sottoscrizioni nel tenant di Azure AD. 


Per una panoramica dettagliata dei gruppi di gestione, vedere l'articolo [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Visualizzare e creare gruppi di gestione nell'portale di Azure

1. Dalla [portale di Azure](https://portal.azure.com)utilizzare la casella di ricerca nella barra superiore per trovare e aprire **gruppi di gestione**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Accesso ai gruppi di gestione":::

    Verrà visualizzato l'elenco dei gruppi di gestione.

1. Per creare un gruppo di gestione, selezionare **Aggiungi gruppo di gestione**, immettere i dettagli pertinenti e selezionare **Salva**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Aggiunta di un gruppo di gestione ad Azure":::

    - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. 
    - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.  


### <a name="add-subscriptions-to-a-management-group"></a>Aggiungere sottoscrizioni a un gruppo di gestione
È possibile aggiungere le sottoscrizioni al gruppo di gestione creato.

1. In **gruppi di gestione** selezionare il gruppo di gestione per la sottoscrizione.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Selezionare un gruppo di gestione per la sottoscrizione":::

1. Quando viene visualizzata la pagina del gruppo, selezionare **Details (dettagli** )

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Apertura della pagina dei dettagli di un gruppo di gestione":::

1. Nella pagina dei dettagli del gruppo selezionare **Aggiungi sottoscrizione**, quindi selezionare le sottoscrizioni e selezionare **Salva**. Ripetere fino a quando non sono state aggiunte tutte le sottoscrizioni nell'ambito.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Aggiunta di una sottoscrizione a un gruppo di gestione":::
   > [!IMPORTANT]
   > I gruppi di gestione possono contenere sia sottoscrizioni che gruppi di gestione figlio. Quando si assegna un utente a un ruolo di Azure al gruppo di gestione padre, l'accesso viene ereditato dalle sottoscrizioni del gruppo di gestione figlio. Anche i criteri impostati nel gruppo di gestione padre vengono ereditati dagli elementi figlio. 



## <a name="assign-azure-roles-to-other-users"></a>Assegnare i ruoli di Azure ad altri utenti

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Assegnare i ruoli di Azure agli utenti tramite la portale di Azure: 
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Per visualizzare i gruppi di gestione, selezionare **Tutti i servizi** nel menu principale di Azure, quindi selezionare **Gruppi di gestione**.
1.  Selezionare un gruppo di gestione e fare clic su **Dettagli**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Schermata dei dettagli dei Gruppi di gestione":::

1. Selezionare **controllo di accesso (IAM)** e quindi **assegnazioni di ruolo**.
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. Selezionare il ruolo da assegnare e l'utente, quindi selezionare **Salva**.  
   
   ![Aggiungere la schermata del Ruolo con autorizzazioni di lettura per la sicurezza](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Assegnare i ruoli di Azure agli utenti con PowerShell: 
1. Installare [Azure PowerShell](/powershell/azure/install-az-ps).
2. Eseguire i comandi seguenti: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Quando richiesto, accedere con le credenziali di amministratore globale. 

    ![Screenshot del prompt di accesso](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Concedere le autorizzazioni del ruolo Lettore eseguendo il comando seguente:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Per rimuovere il ruolo, usare il comando seguente: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>Rimuovere l'accesso con privilegi elevati 

Una volta assegnati i ruoli di Azure agli utenti, l'amministratore tenant deve rimuovere se stesso dal ruolo amministratore accesso utenti.

1. Accedere al [portale di Azure](https://portal.azure.com) o all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com).

2. Nell'elenco di navigazione selezionare **Azure Active Directory** e quindi selezionare **Proprietà**.

3. In **gestione accessi per le risorse di Azure** impostare l'opzione su **No**.

4. Per salvare l'impostazione, selezionare **Salva**.



## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come organizzare le sottoscrizioni in gruppi di gestione e assegnare i ruoli agli utenti. Per informazioni correlate, vedere:

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)