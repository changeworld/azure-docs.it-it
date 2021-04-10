---
title: 'Esercitazione: configurare il segmento per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come effettuare automaticamente il provisioning e il deprovisioning degli account utente dal Azure AD al segmento.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 20939a92-5f48-4ef7-ab95-042e70ec1e0e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2021
ms.author: Zhchia
ms.openlocfilehash: 23e23a2a70a5897a714af17c0f8b6420cb011399
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050644"
---
# <a name="tutorial-configure-segment-for-automatic-user-provisioning"></a>Esercitazione: configurare il segmento per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire in entrambi i segmenti e Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per il [segmento](https://www.segment.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti nel segmento
> * Rimuovere gli utenti nel segmento se non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e segmento
> * Effettuare il provisioning di gruppi e appartenenze a gruppi nel segmento
> * [Single Sign-on per il](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial) segmento (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account utente nel segmento con autorizzazioni proprietario.
* Per l'area di lavoro deve essere abilitata l'accesso SSO (è necessaria una sottoscrizione al livello business).


## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determinare quali dati eseguire il [mapping tra Azure ad e segmento](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-segment-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare il segmento per supportare il provisioning con Azure AD

1. L'URL del tenant è `http://scim.segmentapis.com/scim/v2` . Questo valore verrà immesso nel campo **URL tenant** nella scheda provisioning dell'applicazione del segmento nell'portale di Azure.

2. Accedere all'app [Segment](https://www.segment.com/) .

3. Nel riquadro sinistro passare a impostazioni impostazioni   >    >  **Avanzate** di autenticazione.

    ![panello](media/segment-provisioning-tutorial/left.png)

4. Scorrere verso il basso fino a **sincronizzazione SSO** e fare clic su **genera token SSO**.

    ![access](media/segment-provisioning-tutorial/token.png)

5. Copiare e salvare il valore di Bearer token. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione del segmento nell'portale di Azure.

    ![token](media/segment-provisioning-tutorial/access.png)

## <a name="step-3-add-segment-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere un segmento dalla raccolta di applicazioni Azure AD

Aggiungere un segmento dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning nel segmento. Se è stato precedentemente configurato il segmento per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quando si assegnano utenti e gruppi al segmento, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-segment"></a>Passaggio 5. Configurare il provisioning utenti automatico per il segmento 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-segment-in-azure-ad"></a>Per configurare il provisioning utenti automatico per il segmento in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Segment**.

    ![Collegamento segmento nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant del segmento e il token del segreto recuperati in precedenza nel passaggio 2. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi al segmento. Se la connessione non riesce, verificare che l'account del segmento disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a segmento**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a segmento nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nel segmento per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), sarà necessario assicurarsi che l'API del segmento supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro
   |---|---|--|
   |userName|string|&check;|
   |emails[type eq "work"].value|string|
   |displayName|string|

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi da segmentare**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a segmento nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi nel segmento per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

      |Attributo|Type|Supportato per il filtro
      |---|---|--|
      |displayName|string|&check;|
      |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per segmento, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nel segmento selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

1. Usare i [log di provisioning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
2. Controllare l'[indicatore di stato](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
3. Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
