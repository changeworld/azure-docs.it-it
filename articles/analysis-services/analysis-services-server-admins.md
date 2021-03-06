---
title: Gestire gli amministratori del server in Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive come gestire gli amministratori del server per un server Azure Analysis Services usando le API portale di Azure, PowerShell o REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769194"
---
# <a name="manage-server-administrators"></a>Gestire gli amministratori del server

Gli amministratori del server devono essere un utente, un'entità servizio o un gruppo di sicurezza valido nel Azure Active Directory (Azure AD) per il tenant in cui risiede il server. È possibile usare **Amministratori di Analysis Services** per il server nel portale di Azure, in Proprietà server in SSMS, PowerShell o API REST per gestire gli amministratori del server. 

Quando si aggiunge **un gruppo di sicurezza,** usare `obj:groupid@tenantid` . Le entità servizio non sono supportate nei gruppi di sicurezza aggiunti al ruolo di amministratore del server.

Per altre informazioni sull'aggiunta di un'entità servizio al ruolo di amministratore del server, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

Se il firewall del server è abilitato, gli indirizzi IP dei computer client dell'amministratore del server devono essere inclusi in una regola del firewall. Per altre informazioni, vedere [Configurare il firewall del server.](analysis-services-qs-firewall.md)

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Per aggiungere amministratori del server usando il portale di Azure

1. Nel portale, per il server fare clic su **Amministratori di Analysis Services**.
2. In **\<servername> - Analysis Services Admins fare** clic su **Aggiungi**.
3. In **Aggiungi amministratori del server** selezionare gli account utente da Azure AD o invitare gli utenti esterni in base all'indirizzo di posta elettronica.

    ![Amministratori del server nel portale di Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Per aggiungere amministratori del server usando SSMS

1. Fare clic con il pulsante destro del mouse sul server > **Proprietà**.
2. In **Proprietà computer Analysis Server** fare clic su **Sicurezza**.
3. Fare clic su **Aggiungi** e quindi immettere l'indirizzo di posta elettronica per un utente o un gruppo in Azure AD.
   
    ![Aggiungere gli amministratori del server in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare il cmdlet [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) per specificare il parametro Administrator durante la creazione di un nuovo server. <br>
Usare il cmdlet [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) per modificare il parametro Administrator per un server esistente.

## <a name="rest-api"></a>API REST

Usare [Crea](/rest/api/analysisservices/servers/create) per specificare la proprietà asAdministrator quando si crea un nuovo server. <br>
Usare [Aggiorna](/rest/api/analysisservices/servers/update) per specificare la proprietà asAdministrator quando si modifica un server esistente. <br>



## <a name="next-steps"></a>Passaggi successivi 

[Autenticazione e autorizzazioni utente](analysis-services-manage-users.md)  
[Gestire ruoli e utenti del database](analysis-services-database-users.md)  
[Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)
