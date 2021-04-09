---
title: 'Esercitazione: Configurare Snowflake per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in fiocco.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539536"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Esercitazione: Configurare Snowflake per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in fiocco di neve e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi in [fiocco di neve](https://www.Snowflake.com/pricing/). Per informazioni importanti sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [che cos'è il provisioning utenti di app Saas automatizzato in Azure ad?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Questo connettore è attualmente disponibile in anteprima pubblica. Per informazioni sulle condizioni per l'utilizzo, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in Snowflake
> * Rimuovere gli utenti in fiocco di neve quando non richiedono più l'accesso
> * Mantenimento della sincronizzazione degli attributi utente tra Azure AD e Snowflake
> * Provisioning di gruppi e appartenenza a gruppi in Snowflake
> * Consenti [Single Sign-on](./snowflake-tutorial.md) a fiocco di neve (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md)
* Un account utente in Azure AD con l' [autorizzazione](../roles/permissions-reference.md) per configurare il provisioning (amministratore dell'applicazione, amministratore di applicazioni cloud, proprietario dell'applicazione o amministratore globale)
* [Tenant A fiocco di neve](https://www.Snowflake.com/pricing/)
* Un account utente in fiocco di pagina con autorizzazioni di amministratore

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1: pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Passaggio 2: configurare fiocco di neve per supportare il provisioning con Azure AD

Prima di configurare fiocco di neve per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di sistema per la gestione delle identità tra domini (SCIM) in fiocco di neve.

1. Accedere alla console di amministrazione di fiocco di neve. Immettere la query seguente nel foglio di testo evidenziato e quindi selezionare **Esegui**.

    ![Screenshot della console di amministrazione di fiocco di neve con pulsante query ed Esegui.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Viene generato un token di accesso SCIM per il tenant di fiocco di neve. Per recuperarlo, selezionare il collegamento evidenziato nello screenshot seguente.

    ![Screenshot di un foglio di un foglio di neve in U i con il token di accesso S C I M denominato.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copiare il valore del token generato e selezionare **fine**. Questo valore viene immesso nella casella **token segreto** nella scheda **provisioning** dell'applicazione fiocco di neve nell'portale di Azure.

    ![Screenshot della sezione dei dettagli, che mostra il token copiato nel campo di testo e l'opzione Done è stata richiamata.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Passaggio 3: aggiungere fiocco di neve dalla raccolta di applicazioni Azure AD

Aggiungere Snowflake dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Snowflake. Se in precedenza è stato impostato fiocco di neve per Single Sign-On (SSO), è possibile utilizzare la stessa applicazione. Tuttavia, si consiglia di creare un'app separata quando si sta testando inizialmente l'integrazione. [Altre informazioni sull'aggiunta di un'applicazione dalla raccolta](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4: definire chi sarà nell'ambito del provisioning 

Il servizio Azure AD provisioning consente di definire l'ambito di chi verrà eseguito il provisioning in base all'assegnazione all'applicazione o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile usare i [passaggi per assegnare utenti e gruppi all'applicazione](../manage-apps/assign-user-or-group-access-portal.md). Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile [utilizzare un filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tenere presente i suggerimenti seguenti:

* Quando si assegnano utenti e gruppi a fiocco di neve, è necessario selezionare un ruolo diverso dall'accesso predefinito. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Passaggio 5: configurare il provisioning utenti automatico in fiocco di neve 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi in fiocco di neve. È possibile basare la configurazione sulle assegnazioni di utenti e gruppi in Azure AD.

Per configurare il provisioning utenti automatico per Snowflake in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Screenshot che mostra il riquadro Applicazioni aziendali.](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **fiocco di neve**.

    ![Screenshot che mostra un elenco di applicazioni.](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l'URL di base scim 2,0 e il token di autenticazione recuperato in precedenza nelle caselle **URL tenant** e **token segreto** . 

   Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a fiocco di neve. Se la connessione non riesce, verificare che l'account fiocco di neve disponga di autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra le caselle per il tenant U R L e il token segreto, insieme al pulsante Test connessione.](common/provisioning-testconnection-tenanturltoken.png)

6. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare quindi la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Screenshot che mostra le caselle per il messaggio di posta elettronica di notifica.](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a fiocco di neve**.

9. Esaminare gli attributi utente sincronizzati da Azure AD a Snowflake nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Snowflake per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|
   |---|---|
   |active|Boolean|
   |displayName|string|
   |emails[type eq "work"].value|string|
   |userName|string|
   |name.givenName|string|
   |name.familyName|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|string|

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi a fiocco di neve**.

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Snowflake nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in Snowflake per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|
    |---|---|
    |displayName|string|
    |Membri di|Informazioni di riferimento|

12. Per configurare i filtri di definizione dell'ambito, vedere le istruzioni riportate in questa [esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per fiocco di neve, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

     ![Screenshot che mostra lo stato del provisioning attivato.](common/provisioning-toggle-on.png)

14. Definire gli utenti e i gruppi di cui si vuole eseguire il provisioning in fiocco di neve scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** . 

    Se questa opzione non è disponibile, configurare i campi obbligatori in **credenziali amministratore**, selezionare **Salva** e aggiornare la pagina. 

     ![Screenshot che mostra le opzioni per l'ambito del provisioning.](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

     ![Screenshot del pulsante per il salvataggio di una configurazione di provisioning.](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Le sincronizzazioni successive vengono eseguite ogni 40 minuti, purché il servizio di provisioning Azure AD sia in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6: monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

- Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
- Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
- Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. [Altre informazioni sugli stati di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitazioni dei connettori

I token SCIM generati a fiocco di neve scadono entro 6 mesi. Tenere presente che è necessario aggiornare questi token prima della scadenza, per consentire la sincronizzazione del provisioning per continuare a funzionare. 

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

Il servizio di provisioning Azure AD attualmente funziona in [intervalli IP](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)specifici. Se necessario, è possibile limitare altri intervalli IP e aggiungere questi intervalli IP specifici all'elenco Consenti dell'applicazione. Questa tecnica consentirà il flusso del traffico dal servizio Azure AD provisioning all'applicazione.

## <a name="change-log"></a>Registro delle modifiche

* 07/21/2020: l'eliminazione temporanea è stata abilitata per tutti gli utenti (tramite l'attributo attivo).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
