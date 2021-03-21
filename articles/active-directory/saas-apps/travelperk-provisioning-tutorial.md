---
title: 'Esercitazione: configurare TravelPerk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 7e3e119c81f6417110d34b7b9b97af9fa1738d35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054849"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Esercitazione: configurare TravelPerk per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in TravelPerk che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [TravelPerk](https://www.travelperk.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
>
> - Creare utenti in TravelPerk
> - Rimuovere gli utenti in TravelPerk quando non richiedono più l'accesso
> - Mantieni gli attributi utente sincronizzati tra Azure AD e TravelPerk
> - [Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/travelperk-tutorial) per TravelPerk (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

- [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
- Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale.
- Un account amministratore di [TravelPerk](https://app.travelperk.com/signup) attivo.
- [Piano](https://www.travelperk.com/pricing/)Premium/Pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning

1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e TravelPerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare TravelPerk per supportare il provisioning con Azure AD

1. Accedere all'applicazione [TravelPerk](https://app.travelperk.com/company/integrations/scim) con l'account amministratore.

2. Passare a **Impostazioni società**  >  **Integrations**  >  **scim**

3. Fare clic su **Abilita API scim**

   ![Abilita](./media/travelperk-provisioning-tutorial/configuration.png)

4. È anche possibile abilitare le approvazioni tramite SCIM. Le approvazioni consentono di impostare una governance aggiuntiva garantendo che i viaggi vengano approvati prima dai responsabili approvazione specificati. Per altre informazioni, vedere [qui](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. È possibile specificare se si desidera che il responsabile di ogni persona diventi automaticamente l'utente responsabile dell'approvazione delle corse. Un responsabile approvazione verrà pertanto assegnato nel processo di approvazione automatica corrispondente. TravelPerk eseguirà il mapping del valore di **gestione** di Azure al responsabile approvazione desiderato dell'utente. È necessario che l'utente esista nella piattaforma prima di diventare il responsabile approvazione dell'utente di cui è stato effettuato il provisioning.
Se non sono configurati correttamente in TravelPerk, i responsabili approvazione non verranno creati.

6. La creazione di un processo di approvazione automatica è disponibile nelle **impostazioni di SCIM** dopo l'abilitazione di SCIM dalla pagina integrazioni. Per attivarlo, selezionare **un provider di identità** e impostare l'interruttore per abilitare la **creazione automatica del processo di approvazione**.

7. Dopo aver configurato il processo di approvazione necessario, fare clic su **Salva modifiche** .

   ![Automatizzare](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere TravelPerk dalla raccolta di applicazioni Azure AD

Aggiungere TravelPerk dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in TravelPerk. Se in precedenza è stato configurato TravelPerk per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

- Quando si assegnano utenti a TravelPerk, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli.

- Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Passaggio 5. Configurare il provisioning utenti automatico in TravelPerk

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per TravelPerk in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

   ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **TravelPerk**.

   ![Collegamento di TravelPerk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

   ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

   ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** fare clic su **Autorizza**. Si verrà reindirizzati alla pagina di accesso di **TravelPerk**. Immettere il **nome utente** e la **password** e fare clic sul pulsante **Accedi** . Fare clic su **autorizza app** nella pagina autorizzazione. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a TravelPerk. Se la connessione non riesce, verificare che l'account SecureLogin disponga delle autorizzazioni di amministratore e riprovare.

   ![Credenziali di amministratore](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Schermata iniziale](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

   ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a TravelPerk**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a TravelPerk nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in TravelPerk per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API TravelPerk supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   | Attributo                                                                         | Type      | Supportato per il filtro |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | string    | &check;                 |
   | externalId                                                                        | string    |
   | active                                                                            | Boolean   |
   | name.honorificPrefix                                                              | string    |
   | name.familyName                                                                   | string    |
   | name.givenName                                                                    | string    |
   | name.middleName                                                                   | string    |
   | preferredLanguage                                                                 | string    |
   | locale                                                                            | string    |
   | phoneNumbers[type eq "work"].value                                                | string    |
   | externalId                                                                        | string    |
   | title                                                                             | string    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | string    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | Informazioni di riferimento |
   | urn: IETF: params: SCIM: schemas: estensione: travelperk: 2.0: utente: sesso                 | string    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: utente: dateOfBirth            | string    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: utente: invoiceProfiles        | Array     |
   | urn:IETF:params:SCIM: schemas:Extension:travelperk: 2.0:User:emergencycontact. Name  | string    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: utente: emergencyContact. Phone | string    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: utente: travelPolicy           | string    |

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per TravelPerk, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in TravelPerk selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
