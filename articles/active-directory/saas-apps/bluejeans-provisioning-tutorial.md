---
title: 'Esercitazione: Configurare BlueJeans per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 58cd69ebe97d9d0965d7e648b0ded012ac71cd0d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646128"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Esercitazione: Configurare BlueJeans per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire sia in BlueJeans che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning degli utenti in [BlueJeans](https://www.bluejeans.com/pricing) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in BlueJeans
> * Rimuovere gli utenti in BlueJeans quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e BlueJeans
> * [Single Sign-on](./bluejeans-tutorial.md) per BlueJeans (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un tenant di BlueJeans con [il piano aziendale](https://www.bluejeans.com/pricing) o superiore abilitato.
* Un account utente in BlueJeans con autorizzazioni di amministratore.
* Provisioning di SCIM abilitato in BlueJeans Enterprise.

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API di BlueJeans](https://BlueJeans.github.io/developer), disponibile per i team di BlueJeans con piano Standard o superiore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare BlueJeans per supportare il provisioning con Azure AD

1. Accedere a BlueJeans Admin Console. Passare a impostazioni gruppo > sicurezza.
2. Selezionare **Single Sign-on** e **configurare Now**.

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. Nella finestra **Provisioning & Integration** selezionare **Create and Manage User Accounts through IDP** e fare clic su **generate token**.

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Copiare e salvare il token. 
5. L'URL del tenant di BlueJeans è `https://api.bluejeans.com/v2/scim` . L' **URL del tenant** e il **token Secret** del passaggio precedente verranno immessi nella scheda provisioning dell'applicazione BlueJeans nel portale di Azure.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere BlueJeans dalla raccolta di applicazioni Azure AD

Aggiungere BlueJeans dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in BlueJeans. Se in precedenza è stato configurato BlueJeans per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà effettuato il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà effettuato il provisioning esclusivamente in base agli attributi dell'utente, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti a BlueJeans, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti prima di procedere alla distribuzione generale. Quando l'ambito per il provisioning è impostato su utenti assegnati, è possibile mantenere il controllo assegnando uno o due utenti all'app. Quando l'ambito è impostato su tutti gli utenti, è possibile specificare un [filtro di definizione dell'ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Passaggio 5. Configurare il provisioning utenti automatico in BlueJeans

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti in Coda in base alle assegnazioni di utenti in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Per configurare il provisioning utenti automatico per BlueJeans in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BlueJeans**.

    ![Collegamento a BlueJeans nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant di BlueJeans e il token del segreto recuperato nel passaggio 2. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a BlueJeans. Se la connessione non riesce, verificare che l'account BlueJeans abbia autorizzazioni di amministratore e riprovare.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona che deve ricevere le notifiche degli errori di provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to BlueJeans** (Sincronizza utenti di Azure Active Directory con BlueJeans).

9. Esaminare gli attributi utente sincronizzati da Azure AD a BlueJeans nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in BlueJeans per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API BlueJeans supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

|Attributo|Type|Supportato per il filtro|
|---|---|---|
|userName|string|&check;|
|active|Boolean|
|title|string|
|emails[type eq "work"].value|string|
|name.givenName|string|
|name.familyName|string|
|phoneNumbers[type eq "work"].value|string|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per BlueJeans, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti di cui si vuole eseguire il provisioning in BlueJeans selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Bluejeans non consente nomi utente che superano i 30 caratteri.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)