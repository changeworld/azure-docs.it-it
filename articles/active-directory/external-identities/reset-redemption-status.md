---
title: Reimposta lo stato di riscatto di un utente Guest-Azure AD
description: Informazioni su come reimpostare lo stato di riscatto dell'invito per un Azure Active Directory utenti Guest B2B in Azure AD identità esterne.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea13444a6bd18bd67f05d93a38af70b3b7a2368
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556316"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Reimposta lo stato di riscatto per un utente Guest

Dopo che un utente Guest ha riscattato l'invito per la collaborazione B2B, potrebbe essere necessario aggiornare le informazioni di accesso, ad esempio nei casi seguenti:

- L'utente vuole accedere usando un indirizzo di posta elettronica e un provider di identità diversi
- L'account per l'utente nel tenant principale è stato eliminato e ricreato
- L'utente è stato spostato in un'altra società, ma necessita comunque dello stesso accesso alle risorse
- Le responsabilità dell'utente sono state passate a un altro utente

Per gestire questi scenari in precedenza, era necessario eliminare manualmente l'account dell'utente guest dalla directory e reinvitare l'utente. A questo punto è possibile usare PowerShell o l'API di invito Microsoft Graph per reimpostare lo stato di riscatto dell'utente e reinvitare l'utente conservando l'ID oggetto dell'utente, le appartenenze ai gruppi e le assegnazioni di app. Quando l'utente riscatta il nuovo invito, l'UPN dell'utente non cambia, ma il nome di accesso dell'utente viene modificato nel nuovo messaggio di posta elettronica. L'utente può successivamente accedere usando il nuovo messaggio di posta elettronica o un messaggio di posta elettronica aggiunto alla `otherMails` proprietà dell'oggetto utente.

## <a name="use-powershell-to-reset-redemption-status"></a>Usare PowerShell per reimpostare lo stato di riscatto

Installare il modulo AzureADPreview PowerShell più recente e creare un nuovo invito con `InvitedUserEMailAddress` impostato sul nuovo indirizzo di posta elettronica e `ResetRedemption` impostare su `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Usare Microsoft Graph API per reimpostare lo stato di riscatto

Usando l' [API di invito Microsoft Graph](/graph/api/resources/invitation), impostare la `resetRedemption` proprietà su `true` e specificare il nuovo indirizzo di posta elettronica nella `invitedUserEmailAddress` Proprietà.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Proprietà di un utente Guest B2B Azure AD](user-properties.md)
