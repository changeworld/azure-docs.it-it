---
title: 'Esercitazione: configurare un contenuto per il provisioning utenti automatico con Azure Active Directory'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure Active Directory (Azure AD) a contentful.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516339"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Esercitazione: configurare un contenuto per il provisioning utenti automatico

Questo articolo descrive i passaggi che è necessario completare in contentful e in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Quando è configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi con il [contenuto](https://www.contentful.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e per leggere le risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funzionalità supportate

> [!div class="checklist"]
> * Creare utenti con contenuto
> * Rimuovere gli utenti con contenuto quando non richiedono più l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e con contenuto
> * Effettuare il provisioning di gruppi e appartenenze a gruppi in contentful
> * [Single Sign-on](contentful-tutorial.md) a contentful (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md). 
* Un account utente in Azure AD che disponga dell' [autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale. 
* Un account dell'organizzazione con contenuto con una sottoscrizione che supporta il provisioning di sistema per la gestione delle identità tra domini (SCIM). In caso di domande sulla sottoscrizione dell'organizzazione, contattare il [supporto](mailto:support@contentful.com)per i contenuti.
 
## <a name="plan-your-provisioning-deployment"></a>Pianificare la distribuzione del provisioning

1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare quali dati eseguire il [mapping tra Azure ad e con contenuto](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Configurare il contenuto per il supporto del provisioning con Azure AD

1. In contentful creare un account **utente del servizio** . Tutte le autorizzazioni di provisioning per Azure vengono fornite tramite questo account. Si consiglia di scegliere **proprietario** come ruolo di organizzazione per l'account.

2. Accedere a contentful come utente del **servizio**.

3. Nel menu a sinistra selezionare **Impostazioni organizzazione**  >  **strumenti di accesso**  >  **provisioning utenti**.

   ![Screenshot del menu Impostazioni organizzazione in con contenuto, con provisioning utenti evidenziato in strumenti di accesso.](media/contentful-provisioning-tutorial/access.png)

4. Copiare e salvare l' **URL scim**. Immettere questo valore nella portale di Azure, nella scheda **provisioning** dell'applicazione con contenuto.

5. Selezionare **genera il token di accesso personale**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Nella finestra modale immettere un nome per il token di accesso personale e quindi selezionare **genera**.

7. Vengono generati l'URL SCIM e il token Secret. Copiare e salvare questi valori. Questi valori verranno immessi nella scheda **provisioning** dell'applicazione con contenuto nell'portale di Azure.

    ![Screenshot del riquadro del token di accesso personale, con C F P A T e il nome segnaposto del token evidenziato.](media/contentful-provisioning-tutorial/token.png)


Per eventuali domande durante la configurazione del provisioning nella console di amministrazione con contenuto, contattare il supporto per i [contenuti](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Aggiungere il contenuto dalla raccolta di applicazioni di Azure AD

Per gestire il provisioning in contenuto, aggiungere contentful dalla raccolta di applicazioni Azure AD. Se in precedenza è stato configurato un contenuto per Single Sign-On, è possibile usare la stessa applicazione. Tuttavia, è consigliabile creare un'applicazione separata per testare inizialmente l'integrazione. Informazioni su come [aggiungere un'applicazione nella raccolta](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

È possibile usare il servizio di provisioning Azure AD per definire l'ambito di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione o in base agli attributi dell'utente o del gruppo. 

Se si sceglie di definire l'ambito di cui verrà effettuato il provisioning per l'app in base all'assegnazione, completare la procedura per [assegnare utenti e gruppi all'applicazione](../manage-apps/assign-user-or-group-access-portal.md).

Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, usare un filtro di ambito per [definire le regole condizionali per il provisioning degli account utente](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a contentful, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti che hanno il ruolo di accesso predefinito vengono esclusi dal provisioning e sono indicati nei log di provisioning in modo non autorizzato. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 
* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di procedere alla distribuzione generale. Quando l'ambito del provisioning è impostato su utenti e gruppi assegnati, è possibile controllare l'ambito assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Configurare il provisioning utenti automatico in contenuto 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi in un'app di test in base alle assegnazioni di utenti o gruppi in Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Configurare il provisioning utenti automatico per il contenuto in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** e quindi selezionare **tutte le applicazioni**.

   ![Screenshot che mostra il menu applicazioni aziendali nel portale di Azure, con tutte le applicazioni evidenziate.](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Contentful**.

   ![Screenshot che mostra i primi 20 risultati restituiti nell'elenco delle applicazioni.](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

   ![Screenshot della scheda Provisioning evidenziato nella sezione Gestisci del menu a sinistra.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

   ![Screenshot che mostra le opzioni della modalità di provisioning, con evidenziato automaticamente.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del tenant con contenuto e il token segreto. Per assicurarsi che Azure AD possa connettersi al contenuto, selezionare **Test connessione**. Se la connessione ha esito negativo, assicurarsi che l'account con contenuto sia autorizzato ad avere le autorizzazioni di amministratore, quindi riprovare.

   ![Screenshot che mostra le caselle di testo tenant U R L e Secret token con il pulsante Test connessione evidenziato.](common/provisioning-testconnection-tenanturltoken.png)

6. In **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning, quindi selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

   ![Screenshot che mostra la casella di testo della posta elettronica di notifica.](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti con il contenuto**.

9. Nella sezione **mapping** degli attributi esaminare gli attributi utente sincronizzati da Azure ad a con contenuto. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in contenuto per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), è necessario assicurarsi che l'API contentful supporti il filtraggio degli utenti in base a tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |name.givenName|string|
   |name.familyName|string|

10. Nella sezione **mapping** selezionare **sincronizza gruppi di Azure Active Directory con contenuto**.

11. Nella sezione **mapping** degli attributi esaminare gli attributi di gruppo sincronizzati dal Azure ad al contenuto. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in contenuto per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|Supportato per il filtro|
    |---|---|---|
    |displayName|string|&check;|
    |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di ambito, completare i passaggi descritti nell'esercitazione relativa al [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per il contenuto, nella sezione **Impostazioni** , per **stato provisioning**, selezionare **on**.

    ![Screenshot che mostra lo stato di attivazione e disattivazione dello stato di provisioning.](common/provisioning-toggle-on.png)

14. Per definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in contentful, nella sezione **Impostazioni** , per **ambito**, selezionare l'opzione pertinente.

    ![Screenshot che mostra le opzioni che è possibile selezionare nel riquadro ambito.](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot che mostra il pulsante Salva e il pulsante Annulla.](common/provisioning-configuration-save.png)

Questa operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** **delle impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo, visualizzare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md).
* Per visualizzare lo stato del ciclo di provisioning e la relativa chiusura, controllare l'indicatore di [stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entra in quarantena. Altre informazioni sugli [Stati di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
