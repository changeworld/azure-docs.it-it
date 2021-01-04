---
title: Proteggere l'accesso esterno con i gruppi in Azure Active Directory e Microsoft 365
description: I gruppi di Azure Active Directory e di Microsoft 365 possono essere usati per aumentare la sicurezza quando gli utenti esterni accedono alle risorse.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e28714e2557027a3f8f5504f7052973a77720b6
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744265"
---
# <a name="securing-external-access-with-groups"></a>Sicurezza dell'accesso esterno con i gruppi 

I gruppi sono una parte essenziale di qualsiasi strategia di controllo di accesso. I gruppi di sicurezza e i gruppi di Microsoft 365 (M365) di Azure Active Directory (Azure AD) possono essere usati come base per la protezione dell'accesso alle risorse.

I gruppi sono l'opzione migliore da usare come base per i meccanismi di controllo di accesso seguenti:

* Criteri di accesso condizionale

* Pacchetti di accesso per la gestione dei diritti 

* Accesso alle risorse di M365, ai team Microsoft e ai siti di SharePoint

I gruppi hanno i ruoli seguenti:

* Proprietari: i proprietari del gruppo gestiscono le impostazioni di gruppo e la relativa appartenenza.

* Membri: membri che ereditano le autorizzazioni e l'accesso assegnati al gruppo.

* Guest: i guest sono membri all'esterno dell'organizzazione. 

## <a name="determine-your-group-strategy"></a>Determinare la strategia di gruppo

Quando si sviluppa la strategia di gruppo per proteggere l'accesso esterno alle risorse, prendere in considerazione il comportamento di [sicurezza desiderato](1-secure-access-posture.md) per determinare le opzioni seguenti.

* **Chi deve essere in grado di creare gruppi?** Si desidera che solo gli amministratori creino i gruppi o si desidera che i dipendenti e gli utenti esterni creino anche questi gruppi.

   * *Per impostazione predefinita, qualsiasi membro tenant può creare Azure ad gruppi di sicurezza*. 

      * È possibile [limitare l'accesso al portale per gli amministratori non](../develop/howto-restrict-your-app-to-a-set-of-users.md) e disabilitare la funzionalità di creazione del gruppo in [PowerShell.](../users-groups-roles/groups-troubleshooting.md) 

      * È anche possibile [configurare la gestione dei gruppi self-service in Azure Active Directory](../users-groups-roles/groups-self-service-management.md). 

   * Per *impostazione predefinita, tutti gli utenti possono creare gruppi e gruppi M365 aperti per l'aggiunta a tutti gli utenti (interni ed esterni) del tenant*. 

      * [È possibile limitare la creazione di gruppi di Microsoft 365](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) ai membri di un particolare gruppo di sicurezza. Usare Windows PowerShell per configurare questa impostazione. 

* **Chi deve essere in grado di invitare persone ai gruppi?** Tutti i membri del gruppo possono aggiungere altri membri oppure solo i proprietari del gruppo possono aggiungere membri?

* **Chi può essere invitato a gruppi?** Per impostazione predefinita, gli utenti esterni possono essere aggiunti ai gruppi. 

### <a name="assign-users-to-groups"></a>Assegnare utenti a gruppi

Gli utenti possono essere assegnati ai gruppi manualmente in base agli attributi utente nel relativo oggetto utente o ad altri criteri. Gli utenti possono essere assegnati solo a gruppi in modo dinamico in base ai rispettivi attributi.

Ad esempio, è possibile assegnare utenti ai gruppi in base al relativo:

* titolo o reparto specifico del processo

* organizzazione partner a cui appartengono (manualmente o tramite organizzazioni connesse)

* tipo di utente (membro o Guest)

* partecipazione a un progetto specifico (manualmente)

* location

I gruppi dinamici possono contenere utenti o dispositivi, ma non entrambi. Si aggiungono query basate sugli attributi utente per assegnare gli utenti al gruppo dinamico. Nell'esempio seguente vengono illustrate le query che aggiungono utenti al gruppo se sono membri (non Guest) e nel reparto finanziario.

![Screenshot della configurazione delle regole di appartenenza dinamica.](media/secure-external-access/4-dynamic-membership-rules.png)

Per ulteriori informazioni sui gruppi dinamici, vedere [creare o aggiornare un gruppo dinamico in Azure Active Directory.](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>Non usare i gruppi per più scopi

Quando si usano i gruppi per motivi di sicurezza o di accesso alle risorse, è importante che dispongano di una singola funzione. Se un gruppo viene usato per concedere l'accesso alle risorse, non dovrebbe essere usato per altri scopi. Se un gruppo viene usato per scopi generici, ad esempio per definire la posizione o l'appartenenza al team, non deve essere usato anche per proteggere l'accesso. 

Si consiglia di utilizzare una convenzione di denominazione per i gruppi di sicurezza che lo rende chiaro. Ad esempio:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Tipi di gruppi

È possibile creare sia gruppi di sicurezza che gruppi di Microsoft 365 Azure AD dal portale di Azure AD o dal portale di amministrazione di M365. Entrambi i tipi possono essere usati come base per la protezione dell'accesso esterno:

|Considerazioni | Gruppi di sicurezza Azure AD (manuale e dinamica)| Gruppi di Microsoft 365 |
| - | - | - |
| Che cosa può contenere il gruppo?| Utenti<br>Gruppi<br>Entità servizio<br>Dispositivi| Solo utenti |
| Dove viene creato il gruppo?| Portale di Azure AD<br>Portale di M365 (se la posta elettronica è abilitata)<br>PowerShell<br>Microsoft Graph<br>Portale per gli utenti finali| Portale di M365<br>Portale di Azure AD<br>PowerShell<br>Microsoft Graph<br>In applicazioni Microsoft 365 |
| Chi crea per impostazione predefinita?| Administrators <br>Utenti finali| Administrators<br>Utenti finali |
| Chi può essere aggiunto per impostazione predefinita?| Utenti interni (membri)| Membri tenant e Guest di qualsiasi organizzazione |
| A che cosa concede l'accesso?| Solo le risorse a cui è assegnata.| Tutte le risorse correlate al gruppo:<br>(Gruppo cassetta postale, sito, team, chat e altre risorse M365 incluse)<br>Qualsiasi altra risorsa a cui viene aggiunto il gruppo |
| Può essere usato con| Accesso condizionale<br>Gestione dei diritti<br>Licenze gruppo| Accesso condizionale<br>Gestione dei diritti<br>Etichette di riservatezza |



Usare i gruppi di Microsoft 365 per creare e gestire un set di risorse Microsoft 365, ad esempio un team e i siti e i contenuti associati. Si tratta di un'ottima scelta per un lavoro basato su progetto. 

 

## <a name="azure-ad-security-groups"></a>Gruppi di sicurezza di Azure AD 

I [gruppi di sicurezza Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) possono contenere utenti o dispositivi e possono essere usati per gestire l'accesso a 

* Risorse di Azure, ad esempio app Microsoft 365, app personalizzate e app SaaS (software as a Service), ad esempio ServiceNow di Dropbox.

* Dati e sottoscrizioni di Azure.

* Servizi di Azure.

Azure AD gruppi di sicurezza possono essere usati anche per:

* assegna licenze per servizi come M365, Dynamics 365 ed Enterprise Mobility and Security. Per altre informazioni, vedere [licenze basate sui gruppi](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

* assegnare autorizzazioni elevate. Per altre informazioni, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo (anteprima](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)). 

Per creare un gruppo [nella portale di Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) passare a Azure Active Directory, quindi a gruppi. È anche possibile creare gruppi di sicurezza Azure AD usando i [cmdlet di PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets). 

> [!NOTE]
> Un gruppo di sicurezza può essere usato per l'assegnazione di un massimo di 1500 di applicazioni, ma non di più. 

![Screenshot della creazione di un gruppo di sicurezza.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Per creare un gruppo di sicurezza abilitato alla posta elettronica, passare all'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com/)**. Non è possibile crearlo nel portale Azure AD. 
<br>È necessario abilitare un gruppo di sicurezza per la posta elettronica al momento della creazione. Non è possibile abilitarlo in un secondo momento.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Organizzazioni ibride e gruppi di sicurezza Azure AD

Le organizzazioni ibride hanno sia un'infrastruttura locale che un'infrastruttura Cloud Azure AD. Molte organizzazioni ibride che usano Active Directory creano i gruppi di sicurezza locali e li sincronizzano con il cloud. Con questo metodo, è possibile aggiungere ai gruppi di sicurezza solo gli utenti nell'ambiente locale.

**Proteggi l'infrastruttura locale dalla compromissione, in quanto una violazione locale può essere usata per ottenere l'accesso al tenant di Microsoft 365**. Per informazioni aggiuntive, vedere la pagina [relativa alla protezione Microsoft 365 da attacchi locali](https://aka.ms/protectm365) .

## <a name="microsoft-365-groups"></a>Gruppi di Microsoft 365

I [gruppi di Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) sono il servizio di appartenenza di base che guida tutti gli accessi in M365. È possibile crearli dal [portale di Azure](https://portal.azure.com/)o dal portale di [M365](https://admin.microsoft.com/). Quando viene creato un gruppo di M365, si concede l'accesso a un gruppo di risorse usate per collaborare. Per un elenco completo di queste risorse, vedere [Panoramica dei gruppi di Microsoft 365 per gli amministratori](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) .

I gruppi di M365 presentano le sfumature seguenti per i loro ruoli

* **Proprietari** : i proprietari del gruppo possono aggiungere o rimuovere membri e avere autorizzazioni univoche, ad esempio la possibilità di eliminare conversazioni dalla posta in arrivo condivisa o modificare le impostazioni del gruppo. I proprietari del gruppo possono rinominare il gruppo, aggiornare la descrizione o l'immagine e altro ancora.

* **Membri** -i membri possono accedere a tutti gli elementi del gruppo ma non possono modificare le impostazioni del gruppo. Per impostazione predefinita, i membri del gruppo possono invitare i Guest a partecipare al gruppo, sebbene sia possibile [controllare tale impostazione](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide).

* **Guest** : il gruppo Guest è membro esterno all'organizzazione. Per impostazione predefinita, i guest sono limitati alle funzionalità dei team.

 

### <a name="m365-group-settings"></a>Impostazioni gruppo M365

È possibile selezionare l'alias di posta elettronica, la privacy e se abilitare il gruppo per i team al momento della configurazione. 

![Screenshot della modifica delle impostazioni del gruppo Microsoft 365](media/secure-external-access/4-edit-group-settings.png)

Dopo l'installazione, è necessario aggiungere i membri e configurare le impostazioni per l'utilizzo della posta elettronica e così via.

### <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti sulla protezione dell'accesso esterno alle risorse. Si consiglia di eseguire le azioni nell'ordine elencato.

1. [Determinare la postura di sicurezza desiderata per l'accesso esterno](1-secure-access-posture.md)

2. [Individua lo stato corrente](2-secure-access-current-state.md)

3. [Creazione di un piano di governance](3-secure-access-plan.md)

4. [Usare i gruppi per la sicurezza](4-secure-access-groups.md) .

5. [Transizione a Azure AD B2B](5-secure-access-b2b.md)

6. [Accesso sicuro con la gestione dei diritti](6-secure-access-entitlement-managment.md)

7. [Accesso sicuro con criteri di accesso condizionale](7-secure-access-conditional-access.md)

8. [Proteggere l'accesso con le etichette di riservatezza](8-secure-access-sensitivity-labels.md)

9. [Accesso sicuro a Microsoft teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)