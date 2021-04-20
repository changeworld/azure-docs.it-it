---
title: Modificare il tipo di autenticazione del sottodominio usando PowerShell e Graph - Azure Active Directory | Microsoft Docs
description: Modificare le impostazioni di autenticazione del sottodominio predefinite ereditate dalle impostazioni del dominio radice in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739315"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Modificare il tipo di autenticazione del sottodominio Azure Active Directory

Dopo l'aggiunta di un dominio radice a Azure Active Directory (Azure AD), tutti i sottodomini successivi aggiunti a tale radice nell'organizzazione Azure AD ereditano automaticamente l'impostazione di autenticazione dal dominio radice. Tuttavia, se si vogliono gestire le impostazioni di autenticazione del dominio indipendentemente dalle impostazioni del dominio radice, è ora possibile usare l'API Microsoft Graph radice. Ad esempio, se si dispone di un dominio radice federato, ad esempio contoso.com, questo articolo consente di verificare un sottodominio, ad esempio child.contoso.com come gestito anziché federato.

Nel portale di Azure AD, quando il dominio padre è federato e l'amministratore tenta di verificare un sottodominio gestito nella pagina Nomi di dominio personalizzati, verrà visualizzato l'errore "Impossibile aggiungere il dominio" con il motivo "Una o più proprietà contengono valori non **validi".** Se si tenta di aggiungere questo sottodominio dal interfaccia di amministrazione di Microsoft 365, si riceverà un errore simile. Per altre informazioni sull'errore, vedere Un dominio figlio non eredita le modifiche del dominio padre [in Office 365, Azure o Intune.](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes)

## <a name="how-to-verify-a-custom-subdomain"></a>Come verificare un sottodominio personalizzato

Poiché i sottodomini ereditano il tipo di autenticazione del dominio radice per impostazione predefinita, è necessario alzare di livello il sottodominio a un dominio radice in Azure AD usando il Microsoft Graph in modo da poter impostare il tipo di autenticazione sul tipo desiderato.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Aggiungere il sottodominio e visualizzarne il tipo di autenticazione

1. Usare PowerShell per aggiungere il nuovo sottodominio, che ha il tipo di autenticazione predefinito del dominio radice. I Azure AD e Microsoft 365 di amministrazione non supportano ancora questa operazione.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Usare [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) per ottenere il dominio. Poiché il dominio non è un dominio radice, eredita il tipo di autenticazione del dominio radice. Il comando e i risultati potrebbero avere un aspetto simile al seguente, usando il proprio ID tenant:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Usare Azure AD'API Graph Explorer per renderlo un dominio radice

Usare il comando seguente per alzare di livello il sottodominio:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Modificare il tipo di autenticazione del sottodominio

1. Usare il comando seguente per modificare il tipo di autenticazione del sottodominio:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Verificare tramite GET in Azure AD Graph Explorer che il tipo di autenticazione del sottodominio sia ora gestito:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere nomi di dominio personalizzati](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Gestire i nomi di dominio](domains-manage.md)
- [Eseguire l'operazione ForceDelete su un nome di dominio personalizzato con l'API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)