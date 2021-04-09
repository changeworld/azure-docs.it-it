---
title: 'Esercitazione: configurare Templafy OpenID Connect per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Templafy OpenID Connect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 6b1894d832c3a74916418aa06885c2917267f4b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652590"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Esercitazione: configurare Templafy OpenID Connect per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Templafy OpenID Connect e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Templafy OpenID Connect.

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
3. Determinare quali dati eseguire il [mapping tra Azure ad e Templafy OpenID Connect](../app-provisioning/customize-application-attributes.md). 

## <a name="assigning-users-to-templafy-openid-connect"></a>Assegnazione di utenti a Templafy OpenID Connect

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Templafy OpenID Connect. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Templafy OpenID Connect seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Suggerimenti importanti per l'assegnazione di utenti a Templafy OpenID Connect

* È consigliabile assegnare un singolo utente Azure AD a Templafy OpenID Connect per testare la configurazione del provisioning utenti automatico. È possibile assegnare un numero maggiore di utenti e/o gruppi in un secondo momento.

* Quando si assegna un utente a Templafy OpenID Connect, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Templafy OpenID Connect per supportare il provisioning con Azure AD

Prima di configurare Templafy OpenID Connect per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Templafy OpenID Connect.

1. Accedere alla console di amministrazione di Templafy. Fare clic su **Administration** (Amministrazione).

    ![Console di amministrazione di Templafy](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Fare clic su **Authentication Method** (Metodo di autenticazione).

    ![Screenshot della sezione di amministrazione di Templafy con l'opzione Authentication Method (Metodo di autenticazione) evidenziata.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Copiare il valore della **chiave API scim** . Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Templafy OpenID Connect nel portale di Azure.

    ![Screenshot della chiave API SCIM.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>Passaggio 3. Aggiungere Templafy OpenID Connect dalla raccolta

Per configurare Templafy OpenID Connect per il provisioning utenti automatico con Azure AD, è necessario aggiungere Templafy OpenID Connect dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Templafy OpenID Connect dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Templafy OpenID Connect**, selezionare **Templafy OpenID Connect** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Templafy OpenID Connect nell'elenco dei risultati](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>Passaggio 4. Configurare il provisioning utenti automatico in Templafy OpenID Connect 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Templafy OpenID Connect in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare la Single Sign-On basata su OpenID Connect per Templafy, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Templafy](templafy-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Templafy OpenID Connect in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Templafy OpenID Connect**.

    ![Collegamento Templafy OpenID Connect nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://scim.templafy.com/scim` in **URL tenant**. Immettere il valore della **chiave API scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Templafy. Se la connessione non riesce, verificare che l'account Templafy abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Templafy OpenID Connect**.

    ![Mapping utente di OpenID Connect Templafy](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Templafy OpenID Connect nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Templafy OpenID Connect per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

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

    ![Mapping del gruppo OpenID Connect Templafy](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Templafy OpenID Connect nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Templafy OpenID Connect per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro|
      |---|---|---|
      |displayName|string|&check;|
      |Membri di|Informazioni di riferimento|
      |externalId|string|      

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Templafy OpenID Connect, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Templafy OpenID Connect scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Templafy OpenID Connect.

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