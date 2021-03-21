---
title: Cmdlet di PowerShell gMSA per l'agente di provisioning di Azure AD Connect Cloud
description: Informazioni su come usare i cmdlet di PowerShell di gMSA per l'agente di provisioning di Azure AD Connect Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653797"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Cmdlet di PowerShell gMSA per l'agente di provisioning di Azure AD Connect Cloud

Lo scopo di questo documento è descrivere i cmdlet di PowerShell per l'agente di provisioning di Azure AD Connect gMSA. Questi cmdlet consentono di ottenere una maggiore granularità sulle autorizzazioni applicate all'account del servizio (gMSA). Per impostazione predefinita, Azure AD Connect Cloud Sync applica tutte le autorizzazioni simili a Azure AD Connect sul gMSA predefinito o su un gMSA personalizzato. 

In questo documento vengono descritti i cmdlet seguenti:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Come usare i cmdlet:  

Per utilizzare questi cmdlet, sono necessari i prerequisiti seguenti.

1. Installare l'agente di provisioning. 
2. Importare il modulo PS del provisioning dell'agente in una sessione di PowerShell. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Rimuovere le autorizzazioni esistenti.  Per rimuovere tutte le autorizzazioni esistenti per l'account del servizio, ad eccezione di SELF use: `Set-AADCloudSyncRestrictedPermission` .  

    Questo cmdlet richiede un parametro denominato `Credential` che può essere passato oppure verrà richiesto se viene chiamato senza di esso.

    Per creare una variabile USE  

   `$credential = Get-Credential` 

   Verrà richiesto all'utente di immettere nome utente e password. Le credenziali devono essere un amministratore di dominio minimo (del dominio in cui è installato Agent), potrebbe essere anche l'amministratore dell'organizzazione. 

4.  È quindi possibile chiamare il cmdlet per rimuovere le autorizzazioni aggiuntive: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. In alternativa, è possibile chiamare semplicemente 

   `Set-AADCloudSyncRestrictedPermissions` che richiederà le credenziali. 

 6.  Aggiungere un tipo di autorizzazione specifico.  Le autorizzazioni aggiunte sono le stesse Azure AD Connect.  Per esempi sull'impostazione delle autorizzazioni, vedere [uso di set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) di seguito.

## <a name="using-set-aadcloudsyncpermissions"></a>Utilizzo di Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` supporta i seguenti tipi di autorizzazioni identici alle autorizzazioni utilizzate da Azure AD Connect. Sono supportati i seguenti tipi di autorizzazione: 

|Tipo di autorizzazione|Descrizione|
|-----|-----|
|BasicRead| Vedere autorizzazioni [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) per Azure ad Connect|
|PasswordHashSync|Vedere autorizzazioni [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) per Azure ad Connect|
|PasswordWriteBack|Vedere autorizzazioni [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) per Azure ad Connect|
|HybridExchangePermissions|Vedere autorizzazioni [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) per Azure ad Connect| 
|ExchangeMailPublicFolderPermissions| Vedere autorizzazioni [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) per Azure ad Connect| 
|CloudHR| Applica "controllo completo" su "oggetti utente discendente" e "Crea/Elimina oggetti utente" in "questo oggetto e tutti i discendenti"| 
|Tutti|aggiunge tutte le autorizzazioni sopra indicate.| 



È possibile usare AADCloudSyncPermissions in uno dei due modi seguenti:
- [Concedere una certa autorizzazione a tutti i domini configurati](#grant-a-certain-permission-to-all-configured-domains) 
- [Concedere una certa autorizzazione a un dominio specifico](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Concedere una certa autorizzazione a tutti i domini configurati 
Per concedere determinate autorizzazioni a tutti i domini configurati, è necessario usare un account amministratore dell'organizzazione.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Concedere una certa autorizzazione a un dominio specifico 
Per concedere determinate autorizzazioni a un dominio specifico, sarà necessario utilizzare almeno un account amministratore di dominio del dominio che si sta tentando di aggiungere.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Nota: per 1. Le credenziali devono essere un amministratore dell'organizzazione minimo. 

Per 2. Le credenziali possono essere Domain Admins o Enterprise Admin. 

  

