---
title: Limitare le autorizzazioni di accesso degli utenti guest - Azure Active Directory | Microsoft Docs
description: Limitare le autorizzazioni di accesso degli utenti guest portale di Azure, PowerShell o Microsoft Graph in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501502"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Limitare le autorizzazioni di accesso guest (anteprima) in Azure Active Directory

Azure Active Directory (Azure AD) consente di limitare ciò che gli utenti guest esterni possono visualizzare nell'organizzazione in Azure AD. Gli utenti guest sono impostati su un livello di autorizzazione limitato per impostazione predefinita in Azure AD, mentre l'impostazione predefinita per gli utenti membri è il set completo di autorizzazioni utente predefinite. Questa è un'anteprima di un nuovo livello di autorizzazione utente guest nelle impostazioni di collaborazione esterna dell'organizzazione Azure AD per un accesso ancora più limitato, quindi le opzioni di accesso guest sono ora:

Livello di autorizzazione         | Livello di accesso | Valore
----------------         | ------------ | -----
Uguale agli utenti membri     | Gli utenti guest hanno lo stesso accesso alle risorse Azure AD utenti membri | a0b1b346-4d3e-4e8b-98f8-753987be4970
Accesso limitato (impostazione predefinita) | Gli utenti guest possono visualizzare l'appartenenza a tutti i gruppi non nascosti | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Accesso con restrizioni (nuovo)**  | **Gli utenti guest non possono visualizzare l'appartenenza ad alcun gruppo** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

Quando l'accesso guest è limitato, i guest possono visualizzare solo il proprio profilo utente. L'autorizzazione per visualizzare altri utenti non è consentita anche se il guest esegue la ricerca in base al nome dell'entità utente o all'objectId. L'accesso con restrizioni impedisce anche agli utenti guest di visualizzare l'appartenenza ai gruppi in cui si sono associati. Per altre informazioni sulle autorizzazioni utente predefinite complessive, incluse le autorizzazioni utente guest, vedere Quali sono le autorizzazioni utente predefinite [in Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

Per configurare le impostazioni di collaborazione esterna, è necessario avere il ruolo Amministratore globale. Non sono necessari requisiti di licenza aggiuntivi per limitare l'accesso guest.

## <a name="update-in-the-azure-portal"></a>Aggiornare nel portale di Azure

Sono state apportate modifiche ai controlli di sicurezza portale di Azure per le autorizzazioni utente guest.

1. Accedere all'interfaccia [di Azure AD di amministrazione con](https://aad.portal.azure.com) amministratore globale autorizzazioni.
1. Nella pagina **Azure Active Directory** panoramica dell'organizzazione selezionare **Impostazioni utente.**
1. In **Utenti esterni** selezionare Gestisci impostazioni di collaborazione **esterna.**
1. Nella pagina **Impostazioni di collaborazione esterna** selezionare l'opzione L'accesso utente guest è limitato alle proprietà e alle **appartenenze dei propri oggetti directory.**

    ![Azure AD delle impostazioni di collaborazione esterna](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Selezionare **Salva**. L'applicazione delle modifiche può richiedere fino a 15 minuti per gli utenti guest.

## <a name="update-with-the-microsoft-graph-api"></a>Aggiornare con l'API Microsoft Graph

È stata aggiunta una nuova API Microsoft Graph per configurare le autorizzazioni guest nell'organizzazione Azure AD lavoro. Le chiamate API seguenti possono essere effettuate per assegnare qualsiasi livello di autorizzazione. Il valore per guestUserRoleId usato qui è per illustrare l'impostazione dell'utente guest con più restrizioni. Per altre informazioni sull'uso del Microsoft Graph per impostare le autorizzazioni guest, vedere [tipo di risorsa authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Configurazione per la prima volta

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La risposta dovrebbe essere Success 204.

### <a name="updating-the-existing-value"></a>Aggiornamento del valore esistente

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

La risposta dovrebbe essere Success 204.

### <a name="view-the-current-value"></a>Visualizzare il valore corrente

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Esempio di risposta:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Eseguire l'aggiornamento con i cmdlet di PowerShell

Con questa funzionalità è stata aggiunta la possibilità di configurare le autorizzazioni limitate tramite i cmdlet di PowerShell v2. I cmdlet get e set di PowerShell sono stati pubblicati nella versione 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Comando Get: Get-AzureADMSAuthorizationPolicy

Esempio:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Comando Set: Set-AzureADMSAuthorizationPolicy

Esempio:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> È necessario immettere authorizationPolicy come ID quando richiesto.

## <a name="supported-microsoft-365-services"></a>Servizi Microsoft 365 supportati

### <a name="supported-services"></a>Servizi supportati

Per supporto si intende che l'esperienza è come previsto; in particolare, che è uguale all'esperienza guest corrente.

- Teams
- Outlook (OWA)
- SharePoint
- Planner in Teams
- App Web Planner

### <a name="services-currently-not-supported"></a>Servizi attualmente non supportati

Il servizio senza supporto corrente potrebbe avere problemi di compatibilità con la nuova impostazione di restrizione guest.

- Moduli
- App per dispositivi mobili Planner
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

Domanda | Risposta
-------- | ------
Dove si applicano queste autorizzazioni? | Queste autorizzazioni a livello di directory vengono applicate Azure AD servizi e portali, tra cui Microsoft Graph, PowerShell v2, portale di Azure e App personali portale. Microsoft 365 anche i servizi di Microsoft 365 per scenari di collaborazione, in particolare Outlook, Microsoft Teams e SharePoint.
In che modo le autorizzazioni limitate influiscono sui gruppi che i guest possono visualizzare? | Indipendentemente dalle autorizzazioni guest predefinite o limitate, i guest non possono enumerare l'elenco di gruppi o utenti. I guest possono visualizzare i gruppi di cui sono membri sia nel portale portale di Azure che nel portale App personali a seconda delle autorizzazioni:<li>**Autorizzazioni predefinite:** per trovare i gruppi di cui sono membri nel portale di Azure, il  guest deve cercare l'ID oggetto nell'elenco Tutti gli utenti e quindi selezionare **Gruppi**. Qui è possibile visualizzare l'elenco dei gruppi di cui sono membri, inclusi tutti i dettagli del gruppo, inclusi il nome, l'indirizzo di posta elettronica e così via. Nel portale App personali è possibile visualizzare un elenco dei gruppi di cui sono proprietari e dei gruppi di cui sono membri.</li><li>**Autorizzazioni guest con restrizioni:** nel portale di Azure è comunque possibile trovare l'elenco dei gruppi di cui sono membri cercando il relativo ID oggetto nell'elenco Tutti gli utenti e quindi selezionare Gruppi. Possono visualizzare solo dettagli molto limitati sul gruppo, in particolare l'ID oggetto. Per impostazione predefinita, le colonne Nome e Posta elettronica sono vuote e Tipo di gruppo non è riconosciuto. Nel portale App personali non sono in grado di accedere all'elenco dei gruppi di cui sono proprietari o dei gruppi di cui sono membri.</li><br>Per un confronto più dettagliato delle autorizzazioni della directory provenienti dal API Graph, vedere [Autorizzazioni utente predefinite.](../fundamentals/users-default-permissions.md#member-and-guest-users)
Quali parti del portale App personali influiranno su questa funzionalità? | La funzionalità dei gruppi nel portale App personali riapporrà queste nuove autorizzazioni. Sono inclusi tutti i percorsi per visualizzare l'elenco dei gruppi e le appartenenze ai gruppi App personali. Non sono state apportate modifiche alla disponibilità del riquadro del gruppo. La disponibilità del riquadro del gruppo è ancora controllata dall'impostazione del gruppo esistente nella portale di Azure.
Queste autorizzazioni sostituiscono le impostazioni guest di SharePoint o Microsoft Teams? | No. Queste impostazioni esistenti controllano comunque l'esperienza e l'accesso in tali applicazioni. Ad esempio, se si verificano problemi in SharePoint, controllare le impostazioni di condivisione esterne.
Quali sono i problemi di compatibilità noti in Planner e Yammer? | <li>Con le autorizzazioni impostate su "restricted", i guest che hanno eseguito l'accesso all'app per dispositivi mobili Planner non saranno in grado di accedere ai piani o alle attività.<li>Con le autorizzazioni impostate su "restricted", i guest che hanno eseguito l'accesso a Yammer non potranno uscire dal gruppo.
Le autorizzazioni guest esistenti verranno modificate nel tenant? | Non sono state apportate modifiche alle impostazioni correnti. Microsoft mantiene la compatibilità con le impostazioni esistenti. Si decide quando apportare modifiche.
Queste autorizzazioni verranno impostate per impostazione predefinita? | No. Le autorizzazioni predefinite esistenti rimangono invariate. Facoltativamente, è possibile impostare le autorizzazioni in modo che siano più restrittive.
Esistono requisiti di licenza per questa funzionalità? | No, non sono presenti nuovi requisiti di licenza con questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle autorizzazioni guest esistenti in Azure AD, vedere [Quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Per visualizzare i metodi Microsoft Graph'API per limitare l'accesso guest, vedere authorizationPolicy resource type (Tipo [di risorsa authorizationPolicy)](/graph/api/resources/authorizationpolicy)
- Per revocare tutti gli accessi per un utente, vedere [Revocare l'accesso utente in Azure AD](users-revoke-access.md)
