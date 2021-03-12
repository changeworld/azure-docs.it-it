---
title: Usare il personale per delegare la gestione degli utenti-Azure AD | Microsoft Docs
description: Delegare la gestione degli utenti tramite il personale e le unità amministrative
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224997"
---
# <a name="manage-your-users-with-my-staff"></a>Gestisci gli utenti con il personale

Il personale IT consente di delegare le autorizzazioni a una figura di autorità, ad esempio un responsabile del negozio o un responsabile del team, per garantire che i membri del personale siano in grado di accedere ai propri account Azure AD. Anziché affidarsi a un helpdesk centrale, le organizzazioni possono delegare attività comuni, come la reimpostazione delle password o la modifica dei numeri di telefono a un Team Manager locale. Con il personale, un utente che non può accedere al proprio account può riottenere l'accesso con pochi clic, senza richiedere supporto tecnico o personale IT.

Prima di configurare il personale per la propria organizzazione, è consigliabile consultare questa documentazione e la [documentazione dell'utente](../user-help/my-staff-team-manager.md) per assicurarsi di comprenderne il funzionamento e il modo in cui influisca sugli utenti. È possibile sfruttare la documentazione dell'utente per eseguire il training e preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione.

## <a name="how-my-staff-works"></a>Funzionamento del personale

Il personale si basa su unità amministrative, ovvero un contenitore di risorse che possono essere usate per limitare l'ambito del controllo amministrativo di un'assegnazione di ruolo. Per ulteriori informazioni, vedere [gestione delle unità amministrative in Azure Active Directory](administrative-units.md). Nel personale, le unità amministrative possono essere usate per contenere un gruppo di utenti in uno Store o un reparto. Un Team Manager può quindi essere assegnato a un ruolo amministrativo in un ambito di una o più unità.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.

  * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione.

  * Se necessario, [creare un tenant di Azure Active Directory](../fundamentals/sign-up-organization.md) o [associare una sottoscrizione di Azure al proprio account](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Per abilitare l'autenticazione basata su SMS, è necessario disporre dei privilegi di *amministratore globale* nel tenant di Azure ad.
* Ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concesso in licenza, anche se non lo usa. Ogni utente abilitato deve avere una delle seguenti Azure AD o licenze Microsoft 365:

  * [Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 o F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 o E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) o [Microsoft 365 (M365) E3 o E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Come abilitare il personale

Una volta configurate le unità amministrative, è possibile applicare questo ambito agli utenti che accedono al personale. Solo gli utenti a cui è assegnato un ruolo amministrativo possono accedere al personale. Per abilitare il personale, attenersi alla procedura seguente:

1. Accedere al portale di Azure come amministratore utente.
2. Passare a **Azure Active Directory**  >  **impostazioni utente le**  >  **anteprime** delle funzionalità utente  >  **Gestisci le impostazioni di anteprima delle funzionalità utente**.
3. In gli **amministratori possono accedere al personale**, è possibile scegliere di abilitare per tutti gli utenti, gli utenti selezionati o nessun accesso utente.

> [!Note]
> Solo gli utenti a cui è stato assegnato un ruolo di amministratore possono accedere al personale. Se si Abilita il personale per un utente a cui non è assegnato un ruolo di amministratore, questi non saranno in grado di accedere al personale.

## <a name="conditional-access"></a>Accesso condizionale

È possibile proteggere il portale personale usando Azure AD i criteri di accesso condizionale. Usare questa funzionalità per attività come la richiesta di autenticazione a più fattori prima di accedere al personale.

Si consiglia vivamente di proteggere il personale utilizzando [Azure ad criteri di accesso condizionale](../conditional-access/index.yml). Per applicare un criterio di accesso condizionale al personale, è prima necessario visitare il sito personale per alcuni minuti per effettuare automaticamente il provisioning dell'entità servizio nel tenant per l'uso da parte dell'accesso condizionale.

L'entità servizio verrà visualizzata quando si crea un criterio di accesso condizionale che si applica all'applicazione cloud personale.

![Creare un criterio di accesso condizionale per l'app personale](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Uso del personale

Quando un utente passa al personale, vengono visualizzati i nomi delle [unità amministrative](administrative-units.md) su cui dispongono di autorizzazioni amministrative. Nella [documentazione dell'utente personale](../user-help/my-staff-team-manager.md)viene usato il termine "location" per fare riferimento a unità amministrative. Se le autorizzazioni di un amministratore non hanno un ambito di unità amministrativa, le autorizzazioni si applicano all'intera organizzazione. Una volta abilitato il personale, gli utenti che sono abilitati e a cui è stato assegnato un ruolo amministrativo possono accedervi tramite [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Possono selezionare un'unità amministrativa per visualizzare gli utenti in tale unità e selezionare un utente per aprire il proprio profilo.

## <a name="reset-a-users-password"></a>Reimpostare la password di un utente

Prima di poter riposare le password per gli utenti locali, è necessario soddisfare le condizioni dei prerequisiti seguenti. Per istruzioni dettagliate, vedere [abilitare la reimpostazione della password self-service](../authentication/tutorial-enable-sspr-writeback.md) .

* Configurare le autorizzazioni per il writeback delle password
* Abilitare il writeback delle password in Azure AD Connect
* Abilitazione del writeback delle password in Azure AD reimpostazione della password self-service (SSPR)

I ruoli seguenti sono autorizzati a reimpostare la password di un utente:

* [Amministratore autenticazione](permissions-reference.md#authentication-administrator)
* [Amministratore di autenticazione con privilegi](permissions-reference.md#privileged-authentication-administrator)
* [Amministratore globale](permissions-reference.md#global-administrator)
* [Amministratore helpdesk](permissions-reference.md#helpdesk-administrator)
* [Amministratore utenti](permissions-reference.md#user-administrator)
* [Amministratore password](permissions-reference.md#password-administrator)

Dal **personale**, aprire il profilo di un utente. Selezionare **Reimposta password**.

* Se l'utente è solo cloud, è possibile visualizzare una password temporanea che è possibile assegnare all'utente.
* Se l'utente viene sincronizzato dall'Active Directory locale, è possibile immettere una password che soddisfi i criteri AD locali. È quindi possibile assegnare la password all'utente.

    ![Indicatore di stato della reimpostazione della password e notifica](./media/my-staff-configure/reset-password.png)

L'utente dovrà modificare la password al successivo accesso.

## <a name="manage-a-phone-number"></a>Gestisci un numero di telefono

Dal **personale**, aprire il profilo di un utente.

* Selezionare la sezione **Aggiungi numero di telefono** per aggiungere un numero di telefono per l'utente
* Selezionare **modifica numero di telefono** per modificare il numero di telefono
* Selezionare **Rimuovi numero di telefono** per rimuovere il numero di telefono dell'utente

A seconda delle impostazioni, l'utente può quindi usare il numero di telefono configurato per accedere con SMS, eseguire l'autenticazione a più fattori ed eseguire la reimpostazione della password self-service.

Per gestire il numero di telefono di un utente, è necessario disporre di uno dei ruoli seguenti:

* [Amministratore autenticazione](permissions-reference.md#authentication-administrator)
* [Amministratore di autenticazione con privilegi](permissions-reference.md#privileged-authentication-administrator)
* [Amministratore globale](permissions-reference.md#global-administrator)

## <a name="search"></a>Cerca

È possibile cercare unità amministrative e utenti nell'organizzazione usando la barra di ricerca del personale. È possibile eseguire ricerche in tutte le unità amministrative e gli utenti dell'organizzazione, ma è possibile apportare modifiche solo agli utenti che si trovano in un'unità amministrativa a cui sono state concesse le autorizzazioni di amministratore.

È anche possibile cercare un utente all'interno di un'unità amministrativa. A tale scopo, usare la barra di ricerca nella parte superiore dell'elenco di utenti.

## <a name="audit-logs"></a>Log di controllo

È possibile visualizzare i log di controllo per le azioni eseguite nel personale nel portale di Azure Active Directory. Se un log di controllo è stato generato da un'azione eseguita nel personale, questo verrà visualizzato in dettagli aggiuntivi nell'evento di controllo.

## <a name="next-steps"></a>Passaggi successivi

Documentazione per gli [utenti del personale](../user-help/my-staff-team-manager.md) 
 [Documentazione sulle unità amministrative](administrative-units.md)
