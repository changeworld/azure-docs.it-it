---
title: Invitare utenti interni a collaborazione B2B-Azure AD
description: Se si dispone di account utente interni per partner, distributori, fornitori, fornitori e altri Guest, è possibile passare a Azure AD collaborazione B2B invitandoli ad accedere con le proprie credenziali esterne o con l'account di accesso. Usare PowerShell o l'API di invito Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576431"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Invitare gli utenti interni alla collaborazione B2B

Prima della disponibilità di Azure AD collaborazione B2B, le organizzazioni potevano collaborare con i distributori, i fornitori, i fornitori e gli altri utenti Guest impostando le relative credenziali interne. Se sono presenti utenti Guest interni come questi, è possibile invitare loro a usare la collaborazione B2B. Questi utenti Guest B2B saranno in grado di usare le proprie identità e credenziali per l'accesso e non sarà necessario gestire le password o gestire i cicli di vita dell'account.

L'invio di un invito a un account interno esistente consente di mantenere l'ID oggetto, l'UPN, l'appartenenza ai gruppi e le assegnazioni di app dell'utente. Non è necessario eliminare manualmente e invitare di nuovo l'utente o riassegnare le risorse. Per invitare l'utente, usare l'API di invito per passare l'oggetto utente interno e l'indirizzo di posta elettronica dell'utente Guest insieme all'invito. Quando l'utente accetta l'invito, il servizio B2B imposta l'oggetto utente interno esistente su un utente B2B. In futuro, l'utente deve accedere ai servizi risorse cloud usando le credenziali B2B.

## <a name="things-to-consider"></a>Aspetti da considerare

- **Accesso alle risorse locali**: dopo che l'utente è stato invitato per la collaborazione B2B, può comunque usare le credenziali interne per accedere alle risorse locali. Per evitare questo problema, è possibile reimpostare o modificare la password per l'account interno. L'eccezione è l' [autenticazione del codice di posta elettronica](one-time-passcode.md)monouso. Se il metodo di autenticazione dell'utente viene modificato in una sola volta, non sarà più possibile usare le proprie credenziali interne.

- **Fatturazione**: questa funzionalità non modifica la UserType per l'utente, quindi non passa automaticamente il modello di fatturazione dell'utente a [identità esterne prezzi utente attivo (Mau)](external-identities-pricing.md). Per attivare i prezzi di MAU per l'utente, impostare UserType per l'utente su `guest` . Si noti anche che il tenant di Azure AD deve essere [collegato a una sottoscrizione di Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) per attivare la fatturazione di Mau.

- L' **invito è unidirezionale**: è possibile invitare gli utenti interni a usare la collaborazione B2B, ma non è possibile rimuovere le credenziali B2B dopo che sono state aggiunte. Per ripristinare un utente solo interno, è necessario eliminare l'oggetto utente e crearne uno nuovo.

- **Teams**: quando l'utente accede ai team con le proprie credenziali esterne, il tenant non sarà disponibile inizialmente nella selezione tenant del team. L'utente può accedere ai team usando un URL che contiene il contesto del tenant, ad esempio: `https://team.microsoft.com/?tenantId=<TenantId>` . Successivamente, il tenant diventerà disponibile nella selezione tenant del team.

- **Utenti sincronizzati locali**: per gli account utente sincronizzati tra l'ambiente locale e il cloud, la directory locale rimane l'origine dell'autorità dopo che questi sono stati invitati a usare la collaborazione B2B. Qualsiasi modifica apportata all'account locale verrà sincronizzata con l'account cloud, inclusa la disabilitazione o l'eliminazione dell'account. Pertanto, non è possibile impedire all'utente di accedere al proprio account locale mantenendo il proprio account cloud semplicemente eliminando l'account locale. È invece possibile impostare la password dell'account locale su un GUID casuale o un altro valore sconosciuto.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Come invitare gli utenti interni alla collaborazione B2B

È possibile usare PowerShell o l'API di invito per inviare un invito B2B all'utente interno. Verificare che l'indirizzo di posta elettronica che si vuole usare per l'invito sia impostato come indirizzo di posta elettronica esterno per l'oggetto utente interno.

- È necessario usare l'indirizzo di posta elettronica nella proprietà User. mail per l'invito.
- Il dominio nella proprietà della posta dell'utente deve corrispondere all'account usato per l'accesso. In caso contrario, alcuni servizi, ad esempio i team, non saranno in grado di autenticare l'utente.

Per impostazione predefinita, l'invito invierà all'utente un messaggio di posta elettronica che informa che è stato invitato, ma è possibile eliminarlo e inviare il proprio messaggio.

> [!NOTE]
> Per inviare un messaggio di posta elettronica o un'altra comunicazione, è possibile usare `New-AzureADMSInvitation` con `-SendInvitationMessage:$false` per invitare gli utenti in modalità invisibile all'utente e quindi inviare un messaggio di posta elettronica all'utente convertito. Vedere [Azure ad API di collaborazione B2B e personalizzazione](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Usare PowerShell per inviare un invito B2B

È necessario Azure AD modulo PowerShell versione 2.0.2.130 o successiva. Usare il comando seguente per eseguire l'aggiornamento al modulo AzureAD di PowerShell più recente e invitare l'utente interno a collaborare B2B:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Usare l'API di invito per inviare un invito B2B

Nell'esempio seguente viene illustrato come chiamare l'API di invito per invitare un utente interno come utente B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

La risposta all'API è la stessa che si ottiene quando si invita un nuovo utente guest nella directory.
## <a name="next-steps"></a>Passaggi successivi

- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
