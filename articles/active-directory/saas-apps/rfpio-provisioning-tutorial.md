---
title: 'Esercitazione: Configurare RFPIO per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in RFPIO.
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
ms.openlocfilehash: ff859e7d77fd19cd006cf45a6faa737297fdb9a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349640"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Esercitazione: Configurare RFPIO per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in RFPIO e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in RFPIO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di RFPIO](https://www.rfpio.com/product/).
* Un account utente in RFPIO con autorizzazioni di amministratore.

## <a name="assigning-users-to-rfpio"></a>Assegnazione di utenti a RFPIO

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a RFPIO. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a RFPIO seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Suggerimenti importanti per l'assegnazione di utenti a RFPIO

* È consigliabile assegnare un singolo utente di Azure AD a RFPIO per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a RFPIO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-rfpio-for-provisioning"></a>Configurare RFPIO per il provisioning

Prima di configurare RFPIO per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in RFPIO.

1.  Accedere alla console di amministrazione di RFPIO. In basso a sinistra nella console di amministrazione fare clic su **Tenant**.

    ![Console di amministrazione di RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Fare clic su **Organization Settings** (Impostazioni organizzazione).
    
    ![Amministrazione di RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Passare a **USER MANAGEMENT** > **SECURITY** > **SCIM** (Gestione utenti > Sicurezza > SCIM).

    ![Aggiunta di SCIM in RFPIO](media/rfpio-provisioning-tutorial/scim.png)

4.  Assicurarsi che l'opzione **Auto User Provisioning** (Provisioning utente automatico) sia abilitata. Fare clic su **GENERATE SCIM API TOKEN** (Genera token dell'API SCIM).

    ![Screenshot della sezione SCIM con l'opzione GENERATE SCIM API TOKEN evidenziata.](media/rfpio-provisioning-tutorial/generate.png)

5.  Salvare il **token dell'API SCIM** perché non verrà più visualizzato per motivi di sicurezza. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione RFPIO nel portale di Azure.

    ![Screenshot della sezione SCIM con la finestra di dialogo di avviso visualizzata dopo la selezione di SUBMIT.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Aggiungere RFPIO dalla raccolta

Per configurare RFPIO per il provisioning utenti automatico con Azure AD, è necessario aggiungere RFPIO dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere RFPIO dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **RFPIO**, selezionare **RFPIO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![RFPIO nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Configurazione del provisioning utenti automatico per RFPIO 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in RFPIO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per RFPIO, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per RFPIO](rfpio-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Per configurare il provisioning utenti automatico per RFPIO in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **RFPIO**.

    ![Collegamento di RFPIO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` in **URL tenant**. Un valore di esempio è `https://Azure-test1.rfpio.com/rfpserver/scim/v2`. Immettere il valore del **token dell'API SCIM** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a RFPIO. Se la connessione non riesce, verificare che l'account RFPIO abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to RFPIO** (Sincronizza utenti di Azure Active Directory con RFPIO).

    ![Mapping di utenti in RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e RFPIO nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in RFPIO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per RFPIO, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in RFPIO scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in RFPIO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* RFPIO attualmente non supporta il provisioning di gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
