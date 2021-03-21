---
title: 'Esercitazione: Provisioning utenti per ThousandEyes - Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735098"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Esercitazione: Configurare ThousandEyes per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in ThousandEyes e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a ThousandEyes. 

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Un tenant di ThousandEyes con il [piano Standard](https://www.thousandeyes.com/pricing) o superiore abilitato 
* Un account utente in ThousandEyes con autorizzazioni di amministratore 

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API SCIM di ThousandEyes](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), disponibile per i team ThousandEyes con piano Standard o superiore.

## <a name="assigning-users-to-thousandeyes"></a>Assegnazione di utenti a ThousandEyes

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app ThousandEyes. Dopo aver stabilito questo, è possibile assegnare tali utenti all'app ThousandEyes seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Suggerimenti importanti per l'assegnazione di utenti a ThousandEyes

* È consigliabile assegnare un singolo utente di Azure AD a ThousandEyes per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a ThousandEyes, è necessario selezionare il ruolo **Utente** o un altro ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Poiché il ruolo **Accesso predefinito** non è applicabile per il provisioning, i relativi utenti vengono ignorati.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurazione del provisioning utenti in ThousandEyes 

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di ThousandEyes e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in ThousandEyes in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per ThousandEyes, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurare il provisioning automatico degli account utente in ThousandEyes con Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Se si è già configurato ThousandEyes per l'accesso Single Sign-On, cercare l'istanza di ThousandEyes usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **ThousandEyes** nella raccolta di applicazioni. Selezionare ThousandEyes nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

    ![Collegamento di ThousandEyes nell'elenco delle applicazioni](common/all-applications.png)
    
3. Selezionare l'istanza di ThousandEyes e quindi la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

![Screenshot che mostra la scheda Provisioning per ThousandEyes con l'opzione Automatico selezionata per Modalità di provisioning.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Nella sezione **Credenziali amministratore** inserire il **token di connessione OAuth** generato dal proprio account ThousandEyes (è possibile trovare o generare il token nella sezione **Profilo** dell'account ThousandEyes.

    ![Screenshot che mostra dove trovare il collegamento Impostazioni account per il gruppo di account corrente.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app ThousandEyes. Se la connessione non riesce, verificare che l'account ThousandEyes abbia autorizzazioni di amministratore di team e ripetere il passaggio 5.

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Salva**.

9. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to ThousandEyes** (Sincronizza utenti di Azure Active Directory in ThousandEyes).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a ThousandEyes. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Parsable per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Parsable supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

     |Attributo|Type|Supportato per il filtro|
     |---|---|---|
     |externalId|string|&check;|
     |userName|string|&check;|
     |active|Boolean|
     |displayName|string|
     |emails[type eq "work"].value|string|
     |name.formatted|string|


11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per ThousandEyes, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in ThousandEyes scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)