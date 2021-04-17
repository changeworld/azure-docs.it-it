---
title: Aggiungere un'organizzazione connessa nella Azure AD entitlement - Azure Active Directory
description: Informazioni su come consentire a utenti esterni all'organizzazione di richiedere pacchetti di accesso in modo da poter collaborare ai progetti.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532087"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Aggiungere un'organizzazione connessa nella Azure AD entitlement

Con Azure Active Directory (Azure AD) entitlement, è possibile collaborare con utenti esterni all'organizzazione. Se si collabora spesso con gli utenti in una directory Azure AD o in un dominio esterno, è possibile aggiungerli come organizzazione connessa. Questo articolo descrive come aggiungere un'organizzazione connessa in modo da consentire agli utenti esterni all'organizzazione di richiedere risorse nella directory.

## <a name="what-is-a-connected-organization"></a>Che cos'è un'organizzazione connessa?

Un'organizzazione connessa è un'altra organizzazione con cui si ha una relazione.  Per consentire agli utenti dell'organizzazione di accedere alle risorse, ad esempio i siti o le app di SharePoint Online, è necessaria una rappresentazione degli utenti dell'organizzazione in tale directory.  Poiché nella maggior parte dei casi gli utenti dell'organizzazione non sono già presenti nella directory Azure AD, è possibile usare la gestione dei diritti per portarli nella directory Azure AD in base alle esigenze.  

La gestione dei diritti consente di specificare gli utenti che formano un'organizzazione connessa in tre modi.  Potrebbe essere

* utenti in un'altra directory Azure AD,
* utenti in un'altra directory Azure AD che è stata configurata per la federazione diretta o
* gli utenti in un'altra directory Azure AD, i cui indirizzi di posta elettronica hanno tutti lo stesso nome di dominio in comune.

Si supponga, ad esempio, di lavorare in Woodgrove Bank e di voler collaborare con due organizzazioni esterne. Queste due organizzazioni hanno configurazioni diverse:

- Graphic Design Institute usa Azure AD e gli utenti hanno un nome di entità utente che termina *con graphicdesigninstitute.com*.
- Contoso non usa ancora Azure AD. Gli utenti contoso hanno un nome dell'entità utente che termina *con contoso.com*.

In questo caso, è possibile configurare due organizzazioni connesse. Si crea un'organizzazione connessa per Graphic Design Institute e una per Contoso. Se quindi si aggiungono le due organizzazioni connesse a un criterio, gli utenti di ogni organizzazione con un nome dell'entità utente corrispondente ai criteri possono richiedere pacchetti di accesso. Gli utenti con un nome di entità utente con un dominio di contoso.com corrisponderebbero all'organizzazione connessa a Contoso e potrebbero anche richiedere pacchetti. Gli utenti con un nome di entità utente con un dominio *di graphicdesigninstitute.com* corrisponderebbero all'organizzazione connessa a Graphic Design Institute e potranno inviare richieste. Inoltre, poiché Graphic Design Institute usa Azure AD, tutti gli utenti con un nome di entità corrispondente [a](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) un dominio verificato aggiunto al tenant, ad esempio *graphicdesigninstitute.example,* potranno richiedere pacchetti di accesso usando lo stesso criterio. Se è attivata l'autenticazione [OTP (One-Time Passcode)](../external-identities/one-time-passcode.md) tramite posta elettronica, sono inclusi gli utenti di tali domini che non hanno ancora account Azure AD che eseguiranno l'autenticazione tramite OTP di posta elettronica quando accedono alle risorse. 

![Esempio di organizzazione connessa](./media/entitlement-management-organization/connected-organization-example.png)

La modalità di autenticazione degli utenti Azure AD directory o dominio dipende dal tipo di autenticazione. I tipi di autenticazione per le organizzazioni connesse sono:

- Azure AD
- [Federazione diretta](../external-identities/direct-federation.md)
- [Passcode una sola volta](../external-identities/one-time-passcode.md) (dominio)

Per una dimostrazione di come aggiungere un'organizzazione connessa, guardare il video seguente:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Aggiungere un'organizzazione connessa

Per aggiungere una directory Azure AD o un dominio esterno come organizzazione connessa, seguire le istruzioni in questa sezione.

**Ruolo prerequisito:** *amministratore globale* o *Amministratore utenti*

1. Nel portale di Azure **selezionare** Azure Active Directory e quindi Selezionare Identity **Governance.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse** e quindi Selezionare Aggiungi **organizzazione connessa.**

    ![Pulsante "Aggiungi organizzazione connessa"](./media/entitlement-management-organization/connected-organization.png)

1. Selezionare la **scheda Informazioni di** base e quindi immettere un nome visualizzato e una descrizione per l'organizzazione.

    ![Riquadro Nozioni di base su "Aggiungi organizzazione connessa"](./media/entitlement-management-organization/organization-basics.png)

1. Lo stato verrà impostato automaticamente su **Configurato quando** si crea una nuova organizzazione connessa. Per altre informazioni sulle proprietà dello stato, vedere [Proprietà dello stato delle organizzazioni connesse](#state-properties-of-connected-organizations)

1. Selezionare la **scheda Directory e** dominio e quindi selezionare Aggiungi directory e **dominio**.

    Verrà **visualizzato il riquadro Seleziona directory e** domini.

1. Nella casella di ricerca immettere un nome di dominio per cercare la directory o Azure AD dominio. Assicurarsi di immettere l'intero nome di dominio.

1. Verificare che il nome dell'organizzazione e il tipo di autenticazione siano corretti. La modalità di accesso degli utenti dipende dal tipo di autenticazione.

    ![Riquadro "Seleziona directory e domini"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selezionare **Aggiungi** per aggiungere la directory Azure AD dominio. Attualmente è possibile aggiungere solo una directory o Azure AD dominio per ogni organizzazione connessa.

    > [!NOTE]
    > Tutti gli utenti della Azure AD o del dominio potranno richiedere questo pacchetto di accesso. Sono inclusi gli utenti Azure AD da tutti i sottodomini associati alla directory, a meno che tali domini non siano bloccati dall'elenco di Azure AD business to business (B2B) consentito o negato. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../external-identities/allow-deny-list.md).

1. Dopo aver aggiunto la directory o Azure AD dominio, selezionare **Seleziona**.

    L'organizzazione viene visualizzata nell'elenco.

    ![Riquadro "Directory + dominio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selezionare la **scheda Sponsor** e quindi aggiungere sponsor facoltativi per l'organizzazione connessa.

    Gli sponsor sono utenti interni o esterni già presenti nella directory che sono il punto di contatto per la relazione con questa organizzazione connessa. Gli sponsor interni sono utenti membri nella directory. Gli sponsor esterni sono utenti guest dell'organizzazione connessa che sono stati invitati in precedenza e sono già presenti nella directory. Gli sponsor possono essere utilizzati come responsabili approvazione quando gli utenti dell'organizzazione connessa richiedono l'accesso a questo pacchetto di accesso. Per informazioni su come invitare un utente guest nella directory, vedere [Add Azure Active Directory B2B collaboration users in the portale di Azure](../external-identities/add-users-administrator.md).

    Quando si seleziona **Aggiungi/Rimuovi**, viene visualizzato un riquadro in cui è possibile scegliere sponsor interni o esterni. Il riquadro visualizza un elenco non filtrato di utenti e gruppi nella directory.

    ![Riquadro Sponsor](./media/entitlement-management-organization/organization-sponsors.png)

1. Selezionare la **scheda Rivedi e crea,** rivedere le impostazioni dell'organizzazione e quindi selezionare **Crea.**

    ![Riquadro "Rivedi e crea"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aggiornare un'organizzazione connessa 

Se l'organizzazione connessa cambia in un dominio diverso, il nome dell'organizzazione cambia o si vogliono modificare gli sponsor, è possibile aggiornare l'organizzazione connessa seguendo le istruzioni riportate in questa sezione.

**Ruolo prerequisito:** *amministratore amministratore globale* *o utente*

1. Nel portale di Azure **selezionare** Azure Active Directory e quindi Selezionare Identity **Governance.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse** e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro di panoramica dell'organizzazione connessa selezionare **Modifica per** modificare il nome, la descrizione o lo stato dell'organizzazione.  

1. Nel riquadro **Directory e dominio** selezionare Aggiorna directory e **dominio** per passare a una directory o a un dominio diverso.

1. Nel riquadro **Sponsor selezionare** Aggiungi **sponsor interni** o Aggiungi **sponsor esterni** per aggiungere un utente come sponsor. Per rimuovere uno sponsor, selezionare lo sponsor e nel riquadro destro selezionare **Elimina.**


## <a name="delete-a-connected-organization"></a>Eliminare un'organizzazione connessa

Se non si ha più una relazione con una directory Azure AD o un dominio esterno, è possibile eliminare l'organizzazione connessa.

**Ruolo Prerequisito:** *amministratore globale* o *Amministratore utente*

1. Nella finestra portale di Azure **selezionare** Azure Active Directory e quindi Selezionare **Governance delle identità.**

1. Nel riquadro sinistro selezionare **Organizzazioni connesse** e quindi selezionare l'organizzazione connessa per aprirla.

1. Nel riquadro panoramica dell'organizzazione connessa selezionare **Elimina** per eliminarla.

    ![Pulsante Elimina organizzazione connessa](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Gestione di un'organizzazione connessa a livello di codice

È anche possibile creare, elencare, aggiornare ed eliminare organizzazioni connesse usando Microsoft Graph. Un utente con un ruolo appropriato con un'applicazione con l'autorizzazione delegata può chiamare l'API per gestire gli oggetti `EntitlementManagement.ReadWrite.All` [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true) e impostare gli sponsor.

## <a name="state-properties-of-connected-organizations"></a>Proprietà dello stato delle organizzazioni connesse

Esistono due diversi tipi di proprietà dello stato per le organizzazioni connesse Azure AD gestione degli entitlement attualmente, configurate e proposte: 

- Un'organizzazione connessa configurata è un'organizzazione connessa completamente funzionante che consente agli utenti all'interno di tale organizzazione di accedere ai pacchetti. Quando un amministratore crea una nuova organizzazione connessa nel  portale di Azure, sarà nello stato configurato per impostazione predefinita perché l'amministratore ha creato e vuole usare questa organizzazione connessa. Inoltre, quando un'organizzazione connessa viene creata a livello  di codice tramite l'API, lo stato predefinito deve essere configurato a meno che non venga impostato in modo esplicito su un altro stato. 

    Le organizzazioni connesse configurate verranno mostrate nei selettori per le organizzazioni connesse e saranno nell'ambito di tutti i criteri che hanno come destinazione "tutte" le organizzazioni connesse.

- Un'organizzazione connessa proposta è un'organizzazione connessa che è stata creata automaticamente, ma non è stata creata o approvata da un amministratore. Quando un utente esegue l'accesso a un pacchetto di accesso all'esterno  di un'organizzazione connessa configurata, tutte le organizzazioni connesse create automaticamente saranno nello stato proposto perché nessun amministratore nel tenant ha configurato la relazione. 
    
    Le organizzazioni connesse proposte non sono nell'ambito dell'impostazione "Tutte le organizzazioni connesse configurate" in qualsiasi criterio, ma possono essere usate nei criteri solo per i criteri per organizzazioni specifiche. 

Solo gli utenti di organizzazioni connesse configurate possono richiedere pacchetti di accesso disponibili per gli utenti di tutte le organizzazioni configurate. Gli utenti delle organizzazioni connesse proposte hanno un'esperienza come se non esiste un'organizzazione connessa per tale dominio; può visualizzare e richiedere solo pacchetti di accesso con ambito per la propria organizzazione specifica o con ambito per qualsiasi utente.

> [!NOTE]
> Nell'ambito dell'implementazione di questa nuova funzionalità, tutte le organizzazioni connesse create prima del 20/09/09 sono state considerate **configurate.** Se è stato creato un pacchetto di accesso che ha consentito agli utenti di qualsiasi organizzazione di iscriversi, è necessario esaminare l'elenco delle organizzazioni connesse create prima di tale data per assicurarsi che nessuna venga classificata in modo ergomenti come **configurato.**  Un amministratore può aggiornare la **proprietà State** in base alle esigenze. Per istruzioni, vedere [Aggiornare un'organizzazione connessa.](#update-a-connected-organization)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso per gli utenti esterni](./entitlement-management-external-users.md)
- [Gestire l'accesso per gli utenti non presenti nella directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
