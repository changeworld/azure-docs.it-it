---
title: Protezione delle identità gestite in Azure Active Directory
description: Spiegazione di come trovare, valutare e aumentare la sicurezza delle identità gestite.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ba630d5e2f8e3e581f394af2ee687a971d3a7a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693161"
---
# <a name="securing-managed-identities"></a>Protezione delle identità gestite

Gli sviluppatori spesso sono impegnati nella gestione dei segreti e delle credenziali usati per proteggere le comunicazioni tra servizi diversi. Le identità gestite sono identità sicure di Azure Active Directory (Azure AD) create per fornire identità per le risorse di Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Vantaggi dell'uso delle identità gestite per le risorse di Azure

Di seguito sono riportati i vantaggi derivanti dall'utilizzo delle identità gestite:

* Non è necessario gestire le credenziali. Con le identità gestite, le credenziali sono completamente gestite, ruotate e protette da Azure. Le identità vengono fornite ed eliminate automaticamente con le risorse di Azure. Le identità gestite consentono alle risorse di Azure di comunicare con tutti i servizi che supportano l'autenticazione Azure AD.

* Nessuno (incluso un amministratore globale) ha accesso alle credenziali, quindi non può essere accidentalmente divulgato da, ad esempio, per essere incluso nel codice.

## <a name="when-to-use-managed-identities"></a>Quando usare le identità gestite?

Le identità gestite sono particolarmente utilizzate per le comunicazioni tra i servizi che supportano l'autenticazione Azure AD. 

Un sistema di origine richiede l'accesso a un servizio di destinazione. Qualsiasi risorsa di Azure può essere un sistema di origine. Ad esempio, una macchina virtuale di Azure, un'istanza di funzione di Azure e le istanze dei servizi app Azure supportano le identità gestite.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Funzionamento dell'autenticazione e dell'autorizzazione

Con le identità gestite, il sistema di origine può ottenere un token da Azure AD senza che il proprietario dell'origine debba gestire le credenziali. Azure gestisce le credenziali. Il token ottenuto dal sistema di origine viene presentato al sistema di destinazione per l'autenticazione. 

Il sistema di destinazione deve autenticare (identificare) e autorizzare il sistema di origine prima di consentire l'accesso. Quando il servizio di destinazione supporta l'autenticazione basata su Azure AD, accetta un token di accesso emesso da Azure AD. 

Azure dispone di un piano di controllo e di un piano dati. Nel piano di controllo è possibile creare le risorse e nel piano dati a cui si accede. Ad esempio, è possibile creare un database Cosmos nel piano di controllo, ma eseguirne una query nel piano dati.

Quando il sistema di destinazione accetta il token per l'autenticazione, può supportare meccanismi diversi per l'autorizzazione per il piano di controllo e il piano dati.

Tutte le operazioni del piano di controllo di Azure sono gestite da [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) e usano il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview). Nel piano dati, ogni sistema di destinazione ha un proprio meccanismo di autorizzazione. Archiviazione di Azure supporta RBAC di Azure nel piano dati. Ad esempio, le applicazioni che usano i servizi app Azure possono leggere i dati da archiviazione di Azure e le applicazioni che usano il servizio Azure Kubernetes possono leggere i segreti archiviati in Azure Key Vault.

Per altre informazioni sul controllo e sui piani dati, vedere [operazioni del piano di controllo e del piano dati-Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane).

Tutti i servizi di Azure supporteranno infine le identità gestite. Per altre informazioni, vedere [servizi che supportano identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

##  

## <a name="types-of-managed-identities"></a>Tipi di identità gestite

Esistono due tipi di identità gestite: assegnati dal sistema e assegnati dall'utente.

L'identità gestita assegnata dal sistema presenta le proprietà seguenti:

* Hanno 1:1 relazione con la risorsa di Azure. Ad esempio, è presente un'identità gestita univoca associata a ogni macchina virtuale.

* Sono collegati al ciclo di vita delle risorse di Azure. Quando la risorsa viene eliminata, l'identità gestita associata viene eliminata automaticamente, eliminando il rischio associato agli account orfani. 

Le identità gestite assegnate dall'utente hanno le proprietà seguenti:

* Il ciclo di vita di queste identità è indipendente da una risorsa di Azure ed è necessario gestire il ciclo di vita. Quando si elimina la risorsa di Azure, l'identità gestita assegnata dall'utente assegnata non viene automaticamente eliminata.

* Una singola identità gestita assegnata dall'utente può essere assegnata a zero o più risorse di Azure.

* È possibile crearli in anticipo e quindi assegnarli a una risorsa.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Trovare entità servizio gestite di identità in Azure AD

Esistono diversi modi in cui è possibile trovare identità gestite:

* Uso della pagina applicazioni aziendali nel portale di Azure

* Utilizzo di Microsoft Graph

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

1. In Azure AD selezionare applicazione aziendale.

2. Selezionare il filtro per "identità gestite" 

   ![Immagine della schermata tutte le applicazioni con l'elenco a discesa tipo di applicazione che evidenzia "identità gestite".](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Utilizzo di Microsoft Graph

È possibile ottenere un elenco di tutte le identità gestite nel tenant con la richiesta GET seguente per Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

È possibile filtrare queste richieste. Per ulteriori informazioni, vedere la documentazione relativa a Graph per [Get servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-get?view=graph-rest-1.0&tabs=http).

## <a name="assess-the-security-of-managed-identities"></a>Valutare la sicurezza delle identità gestite 

È possibile valutare la sicurezza delle identità gestite nei modi seguenti:

* Esaminare i privilegi e verificare che sia selezionato il modello con privilegi minimi. Usare il cmdlet di PowerShell seguente per ottenere le autorizzazioni assegnate alle identità gestite.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Verificare che l'identità gestita non faccia parte di alcun gruppo con privilegi, ad esempio un gruppo Administrators.  
A tale scopo, è possibile enumerare i membri dei gruppi con privilegi elevati con PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Assicurarsi di conoscere le risorse a cui l'identità gestita accede](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell).

## <a name="move-to-managed-identities"></a>Passa a identità gestite

Se si usa un'entità servizio o un account utente Azure AD, valutare se è possibile usare un oggetto gestito per eliminare la necessità di proteggere, ruotare e gestire le credenziali. 

## <a name="next-steps"></a>Passaggi successivi

**Per informazioni sulla creazione di identità gestite, vedere:** 

[Creare un'identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). 

[Abilitare un'identità gestita assegnata dal sistema durante la creazione della risorsa](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[Abilitare l'identità gestita assegnata dal sistema su una risorsa esistente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Per ulteriori informazioni sugli account del servizio, vedere:**

[Introduzione agli account del servizio Azure Active Directory](service-accounts-introduction-azure.md)

[Protezione delle entità servizio](service-accounts-principal.md)

[Gestione degli account del servizio di Azure](service-accounts-governing-azure.md)

[Introduzione agli account del servizio locali](service-accounts-on-poremises.md)

 

 

 
