---
title: 'Esercitazione: configurare Templafy SAML2 per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Templafy SAML2.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8a966ef5-e364-435b-9e29-3caf27ffb498
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 4cc89c1ad94ae738b25e4ea18d5fe687880969c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648221"
---
# <a name="tutorial-configure-templafy-saml2-for-automatic-user-provisioning"></a>Esercitazione: configurare Templafy SAML2 per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Templafy SAML2 e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Templafy SAML2.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Templafy](https://www.templafy.com/pricing/).
* Un account utente in Templafy con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e TEMPLAFY Saml2](../app-provisioning/customize-application-attributes.md). 

## <a name="assigning-users-to-templafy-saml2"></a>Assegnazione di utenti a Templafy SAML2

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Templafy SAML2. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Templafy SAML2 seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-saml2"></a>Suggerimenti importanti per l'assegnazione di utenti a Templafy SAML2

* È consigliabile assegnare un singolo utente Azure AD a Templafy SAML2 per testare la configurazione del provisioning utenti automatico. È possibile assegnare un numero maggiore di utenti e/o gruppi in un secondo momento.

* Quando si assegna un utente a Templafy SAML2, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="step-2-configure-templafy-saml2-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Templafy SAML2 per supportare il provisioning con Azure AD

Prima di configurare Templafy SAML2 per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Templafy SAML2.

1. Accedere alla console di amministrazione di Templafy. Fare clic su **Administration** (Amministrazione).

    ![Console di amministrazione di Templafy](media/templafy-saml-2-provisioning-tutorial/templafy-admin.png)

2. Fare clic su **Authentication Method** (Metodo di autenticazione).

    ![Screenshot della sezione di amministrazione di Templafy con l'opzione Authentication Method (Metodo di autenticazione) evidenziata.](media/templafy-saml-2-provisioning-tutorial/templafy-auth.png)

3. Copiare il valore della **chiave API scim** . Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Templafy SAML2 nell'portale di Azure.

    ![Screenshot della chiave API SCIM.](media/templafy-saml-2-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-saml2-from-the-gallery"></a>Passaggio 3. Aggiungere Templafy SAML2 dalla raccolta

Per configurare Templafy SAML2 per il provisioning utenti automatico con Azure AD, è necessario aggiungere Templafy SAML2 dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Templafy SAML2 dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **TEMPLAFY Saml2**, selezionare **Templafy Saml2** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Templafy SAML2 nell'elenco dei risultati](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-saml2"></a>Passaggio 4. Configurare il provisioning utenti automatico in Templafy SAML2 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Templafy SAML2 in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Templafy, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Templafy](templafy-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-templafy-saml2-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Templafy SAML2 in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Templafy SAML2**.

    ![Collegamento Templafy SAML2 nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://scim.templafy.com/scim` in **URL tenant**. Immettere il valore della **chiave API scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Templafy. Se la connessione non riesce, verificare che l'account Templafy abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Templafy Saml2**.

    ![Mapping utente Templafy SAML2](media/templafy-saml-2-provisioning-tutorial/user-mapping.png)

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Templafy SAML2 nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Templafy Saml2 per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |displayName|string|
   |title|string|
   |preferredLanguage|string|
   |name.givenName|string|
   |name.familyName|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |phoneNumbers[type eq "fax"].value|string|
   |externalId|string|
   |addresses[type eq "work"].locality|string|
   |addresses[type eq "work"].postalCode|string|
   |addresses[type eq "work"].region|string|
   |addresses[type eq "work"].streetAddress|string|
   |addresses[type eq "work"].country|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Templafy** (Sincronizza gruppi di Azure Active Directory con Templafy).

    ![Templafy mapping del gruppo SAML2](media/templafy-saml-2-provisioning-tutorial/group-mapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Templafy SAML2 nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Templafy Saml2 per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;|
      |Membri di|Informazioni di riferimento|
      |externalId|string|      


12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Templafy SAML2, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Templafy SAML2 scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Templafy Saml2.

## <a name="step-5-monitor-your-deployment"></a>Passaggio 5. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)