---
title: 'Esercitazione: Configurare Infor CloudSuite per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353902"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Esercitazione: Configurare Infor CloudSuite per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Infor CloudSuite e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Infor CloudSuite.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Infor CloudSuite](https://www.infor.com/products/infor-os)
* Un account utente in Infor CloudSuite con autorizzazioni di amministratore.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assegnazione di utenti a Infor CloudSuite

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Infor CloudSuite. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Infor CloudSuite seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Suggerimenti importanti per l'assegnazione di utenti a Infor CloudSuite

* È consigliabile assegnare un singolo utente di Azure AD a Infor CloudSuite per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Infor CloudSuite, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurare Infor CloudSuite per il provisioning

1. Accedere alla [console di amministrazione di Infor CloudSuite](https://www.infor.com/customer-center). Fare clic sull'icona dell'utente e quindi passare a **user management** (Gestione utenti).

    ![Console di amministrazione di Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Fare clic sull'icona del menu nell'angolo in alto a sinistra dello schermo. Fare clic su **Manage** (Gestisci).

    ![Aggiunta di SCIM in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Passare a **SCIM Accounts** (Account SCIM).

    ![Account SCIM in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Aggiungere un utente amministratore facendo clic sull'icona del segno più. Specificare un valore in **SCIM Password** (Password SCIM) e digitarlo anche in **Confirm Password** (Conferma password). Fare clic sull'icona della cartella per salvare la password. Verrà visualizzato un valore di **User Identifier** (Identificatore utente) generato per l'utente amministratore.

    ![Utente amministratore in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Password in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Screenshot della console di amministrazione di Infor CloudSuite con una riga di tabella evidenziata. Questa riga contiene un identificatore utente, le password e un timestamp." border="false":::

5. Per generare il token di connessione, copiare i valori di **User Identifier** (Identificatore utente) e **SCIM Password** (Password SCIM). Incollarli nel Blocco note++ separati da due punti. Codificare il valore stringa passando a **Plugins > MIME Tools > Basic64 Encode** (Plug-in > Strumenti MIME > Codifica Base 64). 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Screenshot di un documento nel Blocco note++. Nel menu Plugins è evidenziata l'opzione MIME tools. Nel menu MIME tools è evidenziata l'opzione Base64 encode." border="false":::

3.  Copiare il token di connessione. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Infor CloudSuite nel portale di Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Aggiungere Infor CloudSuite dalla raccolta

Prima di configurare Infor CloudSuite per il provisioning utenti automatico con Azure AD, è necessario aggiungere Infor CloudSuite dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Infor CloudSuite dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Infor CloudSuite**, selezionare **Infor CloudSuite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infor CloudSuite nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurazione del provisioning utenti automatico in Infor CloudSuite 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Infor CloudSuite in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Infor CloudSuite, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Infor CloudSuite](./infor-cloud-suite-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Infor CloudSuite, vedere [qui](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Infor CloudSuite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Infor CloudSuite**.

    ![Collegamento di Infor CloudSuite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **URL tenant**. Immettere il valore del token di connessione recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Infor CloudSuite. Se la connessione non riesce, verificare che l'account Infor CloudSuite abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Infor CloudSuite** (Sincronizza utenti di Azure Active Directory con Infor CloudSuite).

    ![Mapping di utenti in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Infor CloudSuite nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Infor CloudSuite** (Sincronizza gruppi di Azure Active Directory con Infor CloudSuite).

    ![Mapping di gruppi in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Infor CloudSuite nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con i gruppi in Infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo in Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Infor CloudSuite, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Infor CloudSuite scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Infor CloudSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)