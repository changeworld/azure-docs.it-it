---
title: 'Esercitazione: configurare getAbstract per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 25253e9a302a34fb473da63ad4cad562d6302a8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651744"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Esercitazione: configurare getAbstract per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire sia in getAbstract che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi in [getAbstract](https://www.getabstract.com) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in getAbstract.
> * Rimuovere gli utenti in getAbstract quando non richiedono più l'accesso.
> * Mantieni gli attributi utente sincronizzati tra Azure AD e getAbstract.
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in getAbstract.
> * [Single Sign-on](getabstract-tutorial.md) per getAbstract (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un tenant getAbstract (licenza aziendale getAbstract).
* SSO abilitato in Azure AD tenant e in un tenant getAbstract.
* Approvazione e SCIM abilitazione per getAbstract (inviare un messaggio di posta elettronica a b2b.itsupport@getabstract.com ).

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e getAbstract](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare getAbstract per supportare il provisioning con Azure AD
1. Accedi a getAbstract
2. Fare clic sull'icona delle impostazioni nell'angolo in alto a destra e fare clic sull'opzione **My Central Admin** ,
 
    ![getAbstract amministratore centrale](media/getabstract-provisioning-tutorial/my-account.png)

3. Individuare e fare clic sull'opzione di **Amministrazione scim**
 
    ![Amministratore di SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. Fai clic sul pulsante **Vai** 

    ![ID client SCIM getAbstract](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. Fare clic su **genera nuovo token**

    ![getAbstract SCIM token 1](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. Se si è sicuri, fare clic sul pulsante **genera nuovo token** . In caso contrario, fare clic sul pulsante **Annulla** .

    ![getAbstract SCIM token 2](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. Infine, è possibile fare clic sull'icona copia negli Appunti oppure selezionare l'intero token e copiarlo. Si noti anche che l'URL tenant/base è `https://www.getabstract.com/api/scim/v2` . Questi valori verranno immessi nel campo **token segreto** * e **URL tenant** * nella scheda provisioning dell'applicazione getAbstract nell'portale di Azure.

    ![getAbstract SCIM token 3](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere getAbstract dalla raccolta di applicazioni di Azure AD

Aggiungere getAbstract dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in getAbstract. Se in precedenza è stato configurato getAbstract per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a getAbstract, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Passaggio 5. Configurare il provisioning utenti automatico in getAbstract 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Per configurare il provisioning utenti automatico per getAbstract in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **getAbstract**.

    ![Collegamento getAbstract nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant getAbstract e il token segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a getAbstract. Se la connessione non riesce, verificare che l'account getAbstract disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a getAbstract**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a getAbstract nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in getAbstract per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API getAbstract supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |externalId|string|
   |preferredLanguage|string|

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to getAbstract**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a getAbstract nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in getAbstract per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|Supportato per il filtro|
    |---|---|---|
    |displayName|string|&check;|
    |externalId|string|
    |Membri di|Informazioni di riferimento|
12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per getAbstract, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in getAbstract scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)