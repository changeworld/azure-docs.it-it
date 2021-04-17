---
title: Reimpostare lo stato di riscatto di un utente guest - Azure AD
description: Informazioni su come reimpostare lo stato di riscatto dell'invito per Azure Active Directory utenti guest B2B in Azure AD per identità esterne.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531870"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Reimpostare lo stato di riscatto per un utente guest (anteprima)

Dopo che un utente guest ha riscattato l'invito per Collaborazione B2B, in alcuni casi potrebbe essere necessario aggiornare le informazioni di accesso, ad esempio quando:

- L'utente vuole eseguire l'accesso usando un provider di identità e di posta elettronica diverso
- L'account per l'utente nel tenant principale è stato eliminato e ri-creato
- L'utente si è spostato in un'altra società, ma deve comunque avere lo stesso accesso alle risorse
- Le responsabilità dell'utente sono state passate a un altro utente

Per gestire questi scenari in precedenza, era necessario eliminare manualmente l'account dell'utente guest dalla directory e reinviare l'utente. È ora possibile usare PowerShell o l'API di invito di Microsoft Graph per reimpostare lo stato di riscatto dell'utente e reinviare l'utente mantenendo l'ID oggetto dell'utente, le appartenenze ai gruppi e le assegnazioni di app. Quando l'utente riscatta il nuovo invito, l'UPN dell'utente non cambia, ma il nome di accesso dell'utente viene modificato nel nuovo messaggio di posta elettronica. L'utente può successivamente accedere usando il nuovo messaggio di posta elettronica o un messaggio di posta elettronica aggiunto alla `otherMails` proprietà dell'oggetto utente.

## <a name="reset-the-email-address-used-for-sign-in"></a>Reimpostare l'indirizzo di posta elettronica usato per l'accesso

Se un utente vuole accedere usando un indirizzo di posta elettronica diverso:

1. Assicurarsi che il nuovo indirizzo di posta elettronica sia aggiunto alla `mail` proprietà `otherMails` o dell'oggetto utente. 
2.  Sostituire l'indirizzo di posta elettronica nella `InvitedUserEmailAddress` proprietà con il nuovo indirizzo di posta elettronica.
3. Usare uno dei metodi seguenti per reimpostare lo stato di riscatto dell'utente.

> [!NOTE]
>Durante l'anteprima pubblica sono disponibili due raccomandazioni:
>- Quando si reimposta l'indirizzo di posta elettronica dell'utente su un nuovo indirizzo, è consigliabile impostare la `mail` proprietà . In questo modo l'utente può riscattare l'invito accedendo alla directory oltre a usare il collegamento di riscatto nell'invito.
>- Quando si reimposta lo stato per un utente guest B2B, assicurarsi di eseguire questa operazione nel contesto utente. Le chiamate solo app non sono attualmente supportate.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Usare PowerShell per reimpostare lo stato di riscatto

Installare il modulo PowerShell AzureADPreview più recente e creare un nuovo invito con impostato sul nuovo indirizzo di posta elettronica `InvitedUserEmailAddress` e `ResetRedemption` impostare su `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Usare l Microsoft Graph aPI per reimpostare lo stato di riscatto

Usando [l Microsoft Graph aPI di invito,](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)impostare la proprietà su e specificare il nuovo indirizzo di posta `resetRedemption` `true` elettronica nella proprietà `invitedUserEmailAddress` .

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
- [Proprietà di un Azure AD guest B2B](user-properties.md)
