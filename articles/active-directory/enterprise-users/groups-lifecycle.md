---
title: Impostare la scadenza per Microsoft 365 gruppi - Azure Active Directory | Microsoft Docs
description: Come configurare la scadenza per Microsoft 365 gruppi in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529830"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Configurare i criteri di scadenza per Microsoft 365 gruppi

Questo articolo illustra come gestire il ciclo di vita dei Microsoft 365 impostando criteri di scadenza per tali gruppi. È possibile impostare i criteri di scadenza solo Microsoft 365 gruppi in Azure Active Directory (Azure AD).

Dopo l'impostazione della scadenza di un gruppo:

- I gruppi con attività utente vengono rinnovati automaticamente quando si avvicina la scadenza.
- Ai proprietari del gruppo viene notificato di rinnovare il gruppo, se il gruppo non viene rinnovato automaticamente.
- Qualsiasi gruppo non rinnovato viene eliminato.
- Qualsiasi Microsoft 365 che viene eliminato può essere ripristinato entro 30 giorni dai proprietari del gruppo o dall'amministratore.

Attualmente, è possibile configurare un solo criterio di scadenza per tutti Microsoft 365 gruppi in un'Azure AD organizzativa.

> [!NOTE]
> La configurazione e l'uso dei criteri di scadenza per i gruppi Microsoft 365 richiedono di disporre, ma non necessariamente di assegnare licenze Azure AD Premium per i membri di tutti i gruppi a cui vengono applicati i criteri di scadenza.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph 2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Rinnovo automatico basato sulle attività

Con Azure AD intelligence, i gruppi vengono ora rinnovati automaticamente a seconda che siano stati usati di recente. Questa funzionalità elimina la necessità di un'azione manuale da parte dei proprietari dei gruppi, perché si basa sull'attività degli utenti nei gruppi Microsoft 365 servizi come Outlook, SharePoint o Teams. Ad esempio, se un proprietario o un membro di un gruppo esegue un'operazione come caricare un documento in SharePoint, visitare un canale Teams o inviare un messaggio di posta elettronica al gruppo in Outlook, il gruppo viene rinnovato automaticamente circa 35 giorni prima della scadenza del gruppo e il proprietario non ottiene alcuna notifica di rinnovo.

### <a name="activities-that-automatically-renew-group-expiration"></a>Attività che rinnovano automaticamente la scadenza del gruppo

Le azioni utente seguenti causano il rinnovo automatico del gruppo:

- SharePoint: visualizzare, modificare, scaricare, spostare, condividere o caricare file
- Outlook: Aggiungere un gruppo, leggere/scrivere un messaggio di gruppo dallo spazio del gruppo, Come un messaggio (in Outlook Accesso Web)
- Teams: Visitare un canale teams

### <a name="auditing-and-reporting"></a>Controllo e creazione di report

Gli amministratori possono ottenere un elenco di gruppi rinnovati automaticamente dai log di controllo attività Azure AD.

![Rinnovo automatico dei gruppi in base all'attività](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Di seguito sono riportati i ruoli che possono configurare e usare la scadenza Microsoft 365 gruppi in Azure AD.

Ruolo | Autorizzazioni
-------- | --------
amministratore globale, Amministratore gruppo o Amministratore utenti | È possibile creare, leggere, aggiornare o eliminare le impostazioni dei criteri Microsoft 365 scadenza dei gruppi<br>Può rinnovare qualsiasi Microsoft 365 gruppo
User | È possibile rinnovare Microsoft 365 gruppo di cui è proprietario<br>Può ripristinare un Microsoft 365 di cui è proprietario<br>Questo ruolo consente di leggere le impostazioni dei criteri di scadenza

Per altre informazioni sulle autorizzazioni per ripristinare un gruppo eliminato, vedere Ripristinare un gruppo Microsoft 365 [eliminato in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire [l Azure AD di amministrazione](https://aad.portal.azure.com) con un account di amministratore globale nell'Azure AD aziendale.

2. Selezionare **Gruppi** e quindi Scadenza **per** aprire le impostazioni di scadenza.
  
   ![Impostazioni di scadenza per i gruppi](./media/groups-lifecycle/expiration-settings.png)

3. Nella **pagina Scadenza** è possibile:

    - Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti o un valore personalizzato (deve essere di almeno 30 giorni).
    - Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario.
    - Selezionare quali Microsoft 365 scadono. È possibile impostare la scadenza per:
      - **Tutti** Microsoft 365 gruppi
      - Elenco di **gruppi di** Microsoft 365 selezionati
      - **Nessuno per limitare** la scadenza per tutti i gruppi
    - Al termine salvare le impostazioni facendo clic su **Salva**.

> [!NOTE]
> - Quando si configura la scadenza per la prima volta, tutti i gruppi precedenti all'intervallo di scadenza vengono impostati su 35 giorni fino alla scadenza, a meno che il gruppo non venga rinnovato automaticamente o il proprietario non lo rinnovi.
> - Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.
> - Le avvisi di scadenza per i gruppi usati in Teams vengono visualizzate nel feed Proprietari di Teams.
> - Quando si abilita la scadenza per i gruppi selezionati, è possibile aggiungere fino a 500 gruppi all'elenco. Se è necessario aggiungere più di 500 gruppi, è possibile abilitare la scadenza per tutti i gruppi. In questo scenario, la limitazione di 500 gruppi non si applica.

## <a name="email-notifications"></a>Notifiche di posta elettronica

Se i gruppi non vengono rinnovati automaticamente, le notifiche di posta elettronica come questa vengono inviate ai proprietari del gruppo Microsoft 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo. La lingua del messaggio di posta elettronica è determinata dalla lingua preferita del proprietario dei gruppi o dall'impostazione Azure AD lingua. Se il proprietario del gruppo ha definito una lingua preferita o più proprietari hanno la stessa lingua preferita, viene utilizzata tale lingua. Per tutti gli altri casi, viene Azure AD'impostazione della lingua.

![Notifiche di posta elettronica di scadenza](./media/groups-lifecycle/expiration-notification.png)

Dal messaggio **di posta elettronica di notifica** Rinnova gruppo, i proprietari del gruppo possono accedere direttamente alla pagina dei dettagli del gruppo nel [Pannello di accesso](https://account.activedirectory.windowsazure.com/r#/applications). In questa area è possibile ottenere altre informazioni sul gruppo, ad esempio la descrizione, la data dell'ultimo rinnovo, la data di scadenza, e anche procedere con il rinnovo del gruppo. La pagina dei dettagli del gruppo include ora anche i collegamenti alle risorse Microsoft 365 gruppo, in modo che il proprietario del gruppo possa visualizzare facilmente il contenuto e l'attività nel gruppo.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Una notifica di posta elettronica come questa viene inviata ai proprietari del gruppo Microsoft 365 per informarli della scadenza e della successiva eliminazione del gruppo Microsoft 365 gruppo.

![Notifiche di posta elettronica di eliminazione del gruppo](./media/groups-lifecycle/deletion-notification.png)

Il gruppo può essere ripristinato entro 30 giorni dall'eliminazione selezionando Ripristina gruppo o usando i cmdlet di PowerShell, come descritto in Ripristinare un gruppo Microsoft 365 [eliminato in Azure Active Directory](groups-restore-deleted.md).  Si noti che il periodo di ripristino del gruppo di 30 giorni non è personalizzabile.

Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Come recuperare la data Microsoft 365 di scadenza del gruppo

Oltre a Pannello di accesso in cui gli utenti possono visualizzare i dettagli del gruppo, tra cui la data di scadenza e l'ultima data di rinnovo, la data di scadenza di un gruppo Microsoft 365 può essere recuperata dalla versione beta dell'API REST Microsoft Graph. expirationDateTime come proprietà di gruppo è stato abilitato in Microsoft Graph Beta. Può essere recuperato con una richiesta GET. Per altre informazioni, vedere questo [esempio.](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)

> [!NOTE]
> Per gestire le appartenenze ai gruppi in Pannello di accesso, "Limitare l'accesso ai gruppi in Pannello di accesso" deve essere impostato su "No" nell'impostazione generale Azure Active Directory gruppi.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Funzionamento Microsoft 365 scadenza del gruppo con una cassetta postale in blocco legale

Quando un gruppo scade e viene eliminato, 30 giorni i dati del gruppo presenti in app come Planner, Siti o Teams vengono eliminati definitivamente, mentre la cassetta postale del gruppo che è in blocco a fini giudiziari viene mantenuta. L'amministratore può usare i cmdlet di Exchange per ripristinare la cassetta postale e recuperare i dati.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Funzionamento Microsoft 365 scadenza del gruppo con i criteri di conservazione

I criteri di conservazione vengono configurati tramite il Centro sicurezza e conformità. Se sono stati impostati criteri di conservazione per i gruppi di Microsoft 365, quando un gruppo scade e viene eliminato, le conversazioni di gruppo nella cassetta postale del gruppo e nei file nel sito del gruppo vengono mantenute nel contenitore di conservazione per il numero specifico di giorni definito nei criteri di conservazione. Dopo la scadenza gli utenti non potranno vedere il gruppo o i relativi contenuti, ma potranno recuperare i dati del sito e della cassetta postale tramite e-discovery.

## <a name="powershell-examples"></a>Esempi di PowerShell

Ecco alcuni esempi di come è possibile usare i cmdlet di PowerShell per configurare le impostazioni di scadenza per Microsoft 365 gruppi nell'organizzazione Azure AD lavoro:

1. Installare il modulo PowerShell v2.0 e accedere al prompt di PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configurare le impostazioni di scadenza Usare il cmdlet New-AzureADMSGroupLifecyclePolicy per impostare la durata di tutti i gruppi Microsoft 365 nell'organizzazione Azure AD su 365 giorni. Le notifiche di rinnovo Microsoft 365 gruppi senza proprietari verranno inviate a ' emailaddress@contoso.com '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperare i criteri esistenti Get-AzureADMSGroupLifecyclePolicy: questo cmdlet recupera le impostazioni di scadenza correnti Microsoft 365 gruppo configurate. Questo esempio contiene gli elementi seguenti:

   - ID dei criteri
   - La durata di tutti Microsoft 365 gruppi nell'organizzazione Azure AD è impostata su 365 giorni
   - Le notifiche di rinnovo Microsoft 365 gruppi senza proprietari verranno inviate a emailaddress@contoso.com '.'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aggiornare i criteri esistenti Set-AzureADMSGroupLifecyclePolicy. Questo cmdlet consente di aggiornare un criterio esistente. Nell'esempio seguente la durata del gruppo nei criteri esistenti viene modificata da 365 giorni a 180 giorni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Aggiungere gruppi specifici al criterio Add-AzureADMSLifecyclePolicyGroup. Questo cmdlet aggiunge un gruppo ai criteri del ciclo di vita. Ad esempio:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Rimuovere il criterio esistente Remove-AzureADMSGroupLifecyclePolicy: questo cmdlet elimina le impostazioni di scadenza Microsoft 365 gruppo, ma richiede l'ID criteri. Questo cmdlet disabilita la scadenza per i Microsoft 365 predefiniti.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
I cmdlet seguenti possono essere usati per configurare i criteri in modo più dettagliato. Per altre informazioni, vedere la [documentazione di PowerShell.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire i membri di un gruppo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
