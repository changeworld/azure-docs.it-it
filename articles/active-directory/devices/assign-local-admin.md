---
title: Come gestire gli amministratori locali nei Azure AD aggiunti
description: Informazioni su come assegnare i ruoli di Azure al gruppo di amministratori locale di un dispositivo Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806ff92fcf75ff8d1c8e092d7ff4435751a9e7db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529909"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Come gestire il gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD

Per gestire un dispositivo Windows, è necessario essere un membro del gruppo di amministratori locale. Durante il processo di aggiunta ad Azure Active Directory (Azure AD), Azure AD aggiorna l'appartenenza di questo gruppo in un dispositivo. È possibile personalizzare l'aggiornamento dell'appartenenza per soddisfare i requisiti aziendali. L'aggiornamento di un'appartenenza è, ad esempio, utile se si vuole consentire al personale del supporto tecnico di eseguire le attività che richiedono diritti di amministratore in un dispositivo.

Questo articolo illustra il funzionamento dell'aggiornamento dell'appartenenza degli amministratori locali e come è possibile personalizzarlo durante un Azure AD join. Il contenuto di questo articolo non si applica a un dispositivo **Azure AD aggiunti.**

## <a name="how-it-works"></a>Funzionamento

Quando si connette un dispositivo Windows con Azure AD usando un'aggiunta Azure AD, Azure AD aggiunge le entità di sicurezza seguenti al gruppo administrators locale nel dispositivo:

- Ruolo Amministratore globale di Azure AD
- Ruolo Amministratore dispositivo di Azure AD 
- Utente che esegue l'aggiunta ad Azure AD   

Aggiungendo i ruoli di Azure AD al gruppo di amministratori locale, è possibile aggiornare gli utenti che possono gestire un dispositivo in qualsiasi momento in Azure AD senza modificare nulla sul dispositivo. Azure AD aggiunge anche il ruolo Amministratore dispositivo di Azure AD al gruppo di amministratori locale per supportare il principio dei privilegi minimi necessari. Oltre che agli amministratori globali, è possibile consentire di gestire un dispositivo anche agli utenti a cui è stato assegnato *solo* il ruolo Amministratore dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gestire il ruolo degli amministratori globali

Per visualizzare e aggiornare l'appartenenza al ruolo Amministratore globale, vedere:

- [Visualizzare tutti i membri di un ruolo di amministratore in Azure Active Directory](../roles/manage-roles-portal.md)
- [Assegnare un utente ai ruoli di amministratore in Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gestire il ruolo Amministratore dispositivo 

Nel portale di Azure è possibile gestire il ruolo Amministratore dispositivo nella pagina **Dispositivi**. Per aprire la pagina **Dispositivi**:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
1. Cercare e selezionare *Azure Active Directory*.
1. Nella sezione **Gestisci** fare clic su **Dispositivi**.
1. Nella pagina **Dispositivi** fare clic su **Impostazioni del dispositivo**.

Per modificare il ruolo Amministratore dispositivo, configurare **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**.  

![Amministratori locali aggiuntivi](./media/assign-local-admin/10.png)

>[!NOTE]
> Questa opzione richiede un tenant Azure AD Premium. 

Gli amministratori dispositivo vengono assegnati a tutti i dispositivi aggiunti ad Azure AD. Non è possibile includere gli amministratori dispositivo nell'ambito di un set specifico di dispositivi. L'aggiornamento del ruolo Amministratore dispositivo non ha necessariamente un impatto immediato sugli utenti interessati. Nei dispositivi in cui un utente ha già eseguito l'accesso, l'elevazione dei privilegi avviene quando *si verificano* entrambe le azioni seguenti:

- Sono trascorse fino a 4 ore per Azure AD un nuovo token di aggiornamento primario con i privilegi appropriati. 
- L'utente si chiude e accede di nuovo, non blocca/sblocca, per aggiornare il profilo.

>[!NOTE]
> Le azioni precedenti non sono applicabili agli utenti che non hanno eseguito l'accesso al dispositivo pertinente in precedenza. In questo caso, i privilegi di amministratore vengono applicati immediatamente dopo il primo accesso al dispositivo. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Gestire i privilegi di amministratore usando Azure AD gruppi (anteprima)

A partire Windows 10 2004, è possibile usare i gruppi Azure AD per gestire i privilegi di amministratore Azure AD dispositivi aggiunti con i criteri [MDM](/windows/client-management/mdm/policy-csp-restrictedgroups) gruppi con restrizioni. Questo criterio consente di assegnare singoli utenti o gruppi Azure AD al gruppo administrators locale in un dispositivo aggiunto Azure AD, fornendo la granularità per configurare amministratori distinti per gruppi diversi di dispositivi. 

>[!NOTE]
> A Windows 10 aggiornamento 20H2, è [](/windows/client-management/mdm/policy-csp-localusersandgroups) consigliabile usare i criteri Utenti e gruppi locali anziché i criteri Gruppi con restrizioni


Attualmente, non è disponibile alcuna interfaccia utente in Intune per gestire questi criteri e devono essere configurati usando [impostazioni URI OMA personalizzate.](/mem/intune/configuration/custom-settings-windows-10) Alcune considerazioni sull'uso di uno di questi criteri: 

- L Azure AD di gruppi tramite i criteri richiede il SID del gruppo che può essere ottenuto eseguendo l'API Microsoft Graph [per i gruppi](/graph/api/resources/group). Il SID è definito dalla proprietà nella `securityIdentifier` risposta dell'API.
- Quando viene applicato il criterio Gruppi con restrizioni, tutti i membri correnti del gruppo non presenti nell'elenco Membri vengono rimossi. Pertanto, l'applicazione di questo criterio con nuovi membri o gruppi rimuoverà gli amministratori esistenti, ad esempio l'utente che ha aggiunto il dispositivo, il ruolo amministratore del dispositivo e amministratore globale ruolo dal dispositivo. Per evitare la rimozione di membri esistenti, è necessario configurarli come parte dell'elenco Membri nei criteri Gruppi con restrizioni. Questa limitazione viene affrontata se si usano i criteri Utenti e gruppi locali che consentono aggiornamenti incrementali dell'appartenenza ai gruppi
- I privilegi di amministratore che usano entrambi i criteri vengono valutati solo per i gruppi noti seguenti in un dispositivo Windows 10: Administrators, Users, Guests, Power Users, Desktop remoto Users e Remote Management Users. 
- La gestione degli amministratori locali Azure AD gruppi non è applicabile Azure AD ibrido dispositivi aggiunti o Azure AD registrati.
- Anche se il criterio Gruppi con restrizioni esisteva prima di Windows 10 versione 2004, non supportava i gruppi Azure AD come membri del gruppo administrators locale di un dispositivo. 

## <a name="manage-regular-users"></a>Gestire utenti normali

Per impostazione predefinita, Azure AD aggiunge l'utente che esegue l'aggiunta ad Azure AD al gruppo Amministratori sul dispositivo. Per impedire agli utenti normali di diventare amministratori locali, sono disponibili le opzioni seguenti:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot): Windows Autopilot offre un'opzione per impedire all'utente primario che esegue l'aggiunta di diventare un amministratore locale. A questo scopo, è possibile [creare un profilo di Autopilot](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Registrazione in blocco](/intune/windows-bulk-enroll): un'aggiunta ad Azure AD eseguita nell'ambito di una registrazione in blocco avviene nel contesto di un utente creato automaticamente. Gli utenti che eseguono l'accesso dopo che un dispositivo è stato aggiunto non vengono aggiunti al gruppo Amministratori.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevare manualmente un utente su un dispositivo 

Oltre a usare il processo di aggiunta AD Azure, è anche possibile elevare manualmente un utente normale in modo che diventi un amministratore locale su un dispositivo specifico. Per eseguire questo passaggio, è necessario già essere un membro del gruppo Amministratori locale. 

A partire dalla **versione Windows 10 1709,** è possibile eseguire questa attività da **Impostazioni -> Accounts -> Altri utenti**. Selezionare **Aggiungere un utente aziendale o dell'istituto di istruzione**, immettere l'UPN dell'utente in **Account utente** e selezionare *Amministratore* in **Tipo di account**  
 
È anche possibile aggiungere gli utenti usando il prompt dei comandi:

- Se gli utenti del tenant vengono sincronizzati da Active Directory locale, usare `net localgroup administrators /add "Contoso\username"`.
- Se gli utenti del tenant vengono creati in Azure AD, usare `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerazioni 

Non è possibile assegnare gruppi al ruolo Amministratore dispositivo. Sono consentiti solo singoli utenti.

Gli amministratori dispositivo vengono assegnati a tutti i dispositivi aggiunti ad Azure AD. Non possono essere inclusi nell'ambito di un set specifico di dispositivi.

Quando si rimuovono gli utenti dal ruolo Amministratore dispositivo, questi hanno ancora il privilegio di amministratore locale su un dispositivo, purché abbiano effettuato l'accesso. Il privilegio viene revocato durante l'accesso successivo quando viene emesso un nuovo token di aggiornamento primario. Questa revoca, simile all'elevazione dei privilegi, potrebbe richiedere fino a 4 ore.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla gestione del dispositivo nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md)
- Per altre informazioni sull'accesso condizionale basato su dispositivo, vedere configurare Azure Active Directory di accesso condizionale [basato su dispositivo](../conditional-access/require-managed-devices.md).
