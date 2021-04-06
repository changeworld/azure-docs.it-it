---
title: "Esercitazione: configurare l'utente gestito di GitHub Enterprise per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD all'utente gestito aziendale GitHub.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: cbae87a005240c15a2c3c28dcb8ab126d9957ba6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801231"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Esercitazione: configurare l'utente gestito di GitHub Enterprise per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire sia nell'utente gestito di GitHub Enterprise che nel Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in GitHub Enterprise Managed User usando il servizio di provisioning Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nell'utente gestito di GitHub Enterprise
> * Rimuovere gli utenti nell'utente gestito di GitHub Enterprise quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e l'utente gestito aziendale di GitHub
> * Effettuare il provisioning di gruppi e appartenenze a gruppi nell'utente gestito di GitHub Enterprise
> * Single Sign-on per l'utente gestito di GitHub Enterprise (scelta consigliata)

> [!NOTE]
> Questo connettore di provisioning è abilitato solo per i partecipanti della versione beta di utenti gestiti dall'organizzazione.


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md)
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
* Gli utenti gestiti aziendali hanno abilitato GitHub Enterprise e sono stati configurati per l'accesso con SAML SSO tramite il tenant Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e l'utente gestito aziendale di GitHub](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare l'utente gestito di GitHub Enterprise per supportare il provisioning con Azure AD

1. L'URL del tenant è `https://api.github.com/scim/v2/enterprises/{enterprise}` . Questo valore verrà immesso nel campo URL tenant nella scheda provisioning dell'applicazione utente gestita di GitHub Enterprise nel portale di Azure.

2. Per accedere all'angolo superiore destro di un amministratore gestito aziendale di GitHub, > fare clic sul profilo foto-> quindi fare clic su **Impostazioni**.

3. Nella barra laterale sinistra fare clic su **Impostazioni sviluppatore**.

4. Nella barra laterale sinistra fare clic su **token di accesso personali**.

5. Fare clic su **genera nuovo token**.

6. Selezionare l'ambito **admin: Enterprise** per questo token.

7. Fare clic su **Generate Token** (Genera token).

8. Copiare e salvare il **token Secret**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione utente gestita di GitHub Enterprise nel portale di Azure.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere un utente gestito aziendale GitHub dalla raccolta di applicazioni Azure AD

Aggiungere l'utente gestito aziendale di GitHub dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in GitHub Enterprise Managed User. Se in precedenza è stato configurato l'utente gestito di GitHub Enterprise per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando si assegnano utenti e gruppi a un utente gestito da GitHub Enterprise, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning.

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Passaggio 5. Configurare il provisioning utenti automatico per l'utente gestito di GitHub Enterprise

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Per configurare il provisioning utenti automatico per l'utente gestito di GitHub Enterprise in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **GitHub Enterprise Managed User**.

    ![Collegamento utente gestito di GitHub Enterprise nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant utente gestito di GitHub Enterprise e il token segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi all'utente gestito di GitHub Enterprise. Se la connessione non riesce, verificare che l'account utente gestito di GitHub Enterprise abbia creato il token segreto come proprietario dell'organizzazione e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a GitHub Enterprise Managed User**.

9. Esaminare gli attributi utente sincronizzati da Azure AD all'utente gestito aziendale di GitHub nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nell'utente gestito di GitHub Enterprise per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), è necessario assicurarsi che l'API utente gestito di GitHub Enterprise supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |externalId|string|&check;|
   |userName|string|
   |active|Boolean|
   |Ruoli|string|
   |displayName|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |emails[type eq "work"].value|string|
   |emails[type eq "home"].value|string|
   |emails[type eq "other"].value|string|

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to GitHub Enterprise Managed User**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD all'utente gestito aziendale di GitHub nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi nell'utente gestito aziendale di GitHub per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |externalId|string|&check;|
      |displayName|string|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per l'utente gestito di GitHub Enterprise, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nell'utente gestito di GitHub Enterprise selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

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