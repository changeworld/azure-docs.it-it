---
title: Abilitare le impostazioni di collaborazione esterna B2B - Azure AD
description: Informazioni su come abilitare la collaborazione esterna B2B di Active Directory e gestire chi può invitare utenti guest. Usare il ruolo Mittente dell'invito guest per delegare gli inviti.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747fa3005930414832878757664f4787157302d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645824"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Abilitare la collaborazione esterna B2B e gestire chi può invitare utenti guest

Questo articolo descrive come abilitare la collaborazione B2B Azure Active Directory (Azure AD), indicare gli utenti che possono invitare i guest e determinare le autorizzazioni di cui dispongono gli utenti guest nel Azure AD. 

Per impostazione predefinita, tutti gli utenti e gli utenti guest nella directory possono invitare utenti guest anche se non sono assegnati a un ruolo di amministratore. Le impostazioni di collaborazione esterna consentono di attivare o disattivare gli inviti guest per diversi tipi di utenti dell'organizzazione. È anche possibile delegare gli inviti a singoli utenti assegnando ruoli che consentono loro di invitare utenti guest.

Azure AD consente di limitare le informazioni che gli utenti Guest esterni possono visualizzare nella directory Azure AD. Per impostazione predefinita, gli utenti Guest sono impostati su un livello di autorizzazione limitato che impedisce l'enumerazione di utenti, gruppi o altre risorse della directory, ma consente di visualizzare l'appartenenza di gruppi non nascosti. Una nuova impostazione di anteprima consente di limitare ulteriormente l'accesso guest, in modo che i guest possano visualizzare solo le proprie informazioni sul profilo. Per informazioni dettagliate, vedere [limitare le autorizzazioni di accesso Guest (anteprima)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Configurare le impostazioni di collaborazione esterna B2B

Con Collaborazione B2B di Azure AD, un amministratore tenant può impostare i criteri di invito seguenti:

- Disattivare gli inviti
- Solo gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti
- Gli amministratori, il ruolo Mittente dell'invito guest e i membri possono inviare inviti
- Possono inviare inviti tutti gli utenti, inclusi gli utenti guest

Per impostazione predefinita, tutti gli utenti, inclusi gli utenti guest, possono inviare inviti a utenti guest.

### <a name="to-configure-external-collaboration-settings"></a>Per configurare le impostazioni di collaborazione esterna:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore tenant.
2. Selezionare **Azure Active Directory**.
3. Selezionare **Identità esterne** > **Impostazioni di collaborazione esterna**.

4. In **restrizioni di accesso utente Guest (anteprima)** scegliere il livello di accesso che si desidera che gli utenti Guest dispongano di:
  
   - **Gli utenti Guest hanno lo stesso accesso dei membri (più inclusivo)**: questa opzione consente agli utenti Guest lo stesso accesso alle risorse Azure ad e ai dati di directory come utenti membro.

   - **Gli utenti Guest hanno accesso limitato alle proprietà e alle appartenenze degli oggetti directory**: (impostazione predefinita) questa impostazione blocca i guest da determinate attività di directory, ad esempio l'enumerazione di utenti, gruppi o altre risorse di directory. I guest possono visualizzare l'appartenenza di tutti i gruppi non nascosti.

   - **L'accesso utente Guest è limitato alle proprietà e alle appartenenze dei propri oggetti directory (più restrittivi)**: con questa impostazione, i guest possono accedere solo ai propri profili. Ai guest non è consentito visualizzare i profili, i gruppi o le appartenenze ai gruppi di altri utenti.


5. In **Impostazioni invito Guest** scegliere le impostazioni appropriate:

    ![Impostazioni di invito Guest](./media/delegate-invitations/guest-invite-settings.png)

   - **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare**: per consentire solo agli amministratori e agli utenti del ruolo "Mittente dell'invito guest" di invitare utenti guest, impostare questo criterio su **Sì**.

   - **I membri possono invitare**: per consentire ai membri non amministratori della directory di invitare utenti guest, impostare questo criterio su **Sì**.

   - **Gli utenti guest possono invitare**: per consentire agli utenti guest di invitare altri utenti guest, impostare questo criterio su **Sì**.

   > [!NOTE]
   > Se **i membri possono invitare** è impostato su **No** e **gli amministratori e gli utenti nel ruolo mittente dell'invito Guest possono invitare** è impostato su **Sì**, gli utenti del ruolo di **invito Guest** saranno comunque in grado di invitare i guest.

6. In **password** monouso per i guest scegliere le impostazioni appropriate. per ulteriori informazioni, vedere la pagina relativa all'autenticazione della password monouso per la [posta elettronica](one-time-passcode.md):

   - **Abilitare automaticamente il codice di accesso monouso tramite posta elettronica per i guest nel 2021 ottobre**. Predefinita Se la funzionalità di accesso monouso per il messaggio di posta elettronica non è già abilitata per il tenant, verrà attivata automaticamente nel 2021 ottobre. Non è necessaria alcuna azione aggiuntiva se si desidera che la funzionalità sia abilitata in quel momento. Se la funzionalità è già stata abilitata o disabilitata, questa opzione non sarà disponibile.

   - **Consente di abilitare il codice di posta elettronica monouso per i guest**. Attiva la funzionalità di accesso monouso della posta elettronica per il tenant.

   - **Disabilitare il codice di posta elettronica monouso per i guest**. Disattiva la funzionalità di accesso monouso della posta elettronica per il tenant e impedisce l'attivazione della funzionalità nel 2021 ottobre.

   > [!NOTE]
   > Invece delle opzioni precedenti, verrà visualizzato l'interruttore seguente se è stata abilitata o disabilitata questa funzionalità o se in precedenza è stato scelto l'anteprima:
   >
   >![Abilita la password monouso per la posta elettronica](media/delegate-invitations/enable-email-otp-opted-in.png)

7. In **Abilita i flussi utente per l'iscrizione self-service a Guest** selezionare **Sì** se si vuole essere in grado di creare flussi utente che consentono agli utenti di iscriversi alle app. Per altre informazioni su questa impostazione, vedere [aggiungere un flusso utente di iscrizione self-service a un'app](self-service-sign-up-user-flow.md).

    ![Impostazione dell'iscrizione self-service tramite flussi utente](./media/delegate-invitations/self-service-sign-up-setting.png)

7. In **restrizioni di collaborazione** è possibile scegliere se consentire o negare gli inviti ai domini specificati e immettere nomi di dominio specifici nelle caselle di testo. Per specificare più domini, immettere ognuno in una nuova riga. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](allow-deny-list.md).

    ![Impostazioni delle restrizioni di collaborazione](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>Assegnare il ruolo Mittente dell'invito guest a un utente

Con il ruolo Mittente dell'invito guest è possibile concedere a singoli utenti la possibilità di invitare utenti guest senza assegnare loro il ruolo di amministratore globale o un altro ruolo di amministratore. Assegnare il ruolo Mittente dell'invito guest a singoli utenti. Assicurarsi che l'opzione **Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare** sia impostata su **Sì**.

Ecco un esempio che illustra l'aggiunta di un utente al ruolo Mittente dell'invito guest con PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti guest di Collaborazione B2B senza un invito](add-user-without-invite.md)
- [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)

