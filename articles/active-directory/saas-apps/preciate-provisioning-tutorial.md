---
title: 'Esercitazione: configurare Precie per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Precie.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: fa640971-87e7-49f2-933b-bc7c95fe51e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2020
ms.author: Zhchia
ms.openlocfilehash: ab6b17d2db2fd45578f74da9750d3f50dba2636f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645655"
---
# <a name="tutorial-configure-preciate-for-automatic-user-provisioning"></a>Esercitazione: configurare Precie per il provisioning utenti automatico

In questa esercitazione vengono descritti i passaggi da eseguire sia in Precie che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per [l'uso del](https://www.preciate.org/) servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Crea utenti in preciate
> * Rimuovere gli utenti in Precie quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e preciate

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Tenant Precie.
* Un account utente in Precie con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e precie](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-preciate-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Precie per supportare il provisioning con Azure AD

1.  Accedere al [portale di amministrazione di precie](https://preciate.com/web/admin/keys) e passare alla pagina **Integrations (integrazioni** ).

    ![Chiave privata](media/preciate-provisioning-tutorial/preciate-secret-path.png)

2.  Selezionare il pulsante **genera** in cui viene indicato Active Directory chiave privata di integrazione. 
 
    ![Genera di preciate](media/preciate-provisioning-tutorial/preciate-secret-generate.png)

3.  Verrà visualizzata una nuova **chiave privata** . Copiare e salvare la **chiave privata**. Prendere nota anche che l'URL del tenant è `https://preciate.com/api/v1/scim` . Questi valori verranno immessi nel campo **token segreto** e **URL tenant** nella scheda provisioning dell'applicazione precie nella portale di Azure.
 
> [!NOTE]
>Ogni volta che si fa clic sul pulsante genera viene creata una nuova chiave privata. Verrà immediatamente invalidato quello corrente. Se un'integrazione sta già usando attivamente la chiave corrente, la generazione di quella nuova provocherà l'arresto del funzionamento dell'integrazione finché il token del segreto non verrà aggiornato nell'applicazione di Precie in Azure porta.


## <a name="step-3-add-preciate-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Precie dalla raccolta di applicazioni di Azure AD

Aggiungere Precie dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning in Precie. Se il programma di installazione è stato impostato in precedenza per SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Altre informazioni sull'aggiunta di un'applicazione dalla [raccolta](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Precie, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-preciate"></a>Passaggio 5. Configurare il provisioning utenti automatico su preciate 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-preciate-in-azure-ad"></a>Per configurare il provisioning utenti automatico per preciate in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **preciate**.

    ![Collegamento di Precie nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Automatico nella scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant di precie e il token segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a precie. Se la connessione non riesce, verificare che l'account in uso disponga delle autorizzazioni di amministratore e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gli utenti a precie**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Precie nella sezione **attribute-mapping** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in precie per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API precie supporti l'applicazione di filtri agli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |displayName|string|
   |title|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per preciate, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

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