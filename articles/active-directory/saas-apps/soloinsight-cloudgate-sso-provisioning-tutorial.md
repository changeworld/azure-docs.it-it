---
title: 'Esercitazione: Configurare Soloinsight-CloudGate SSO per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Soloinsight-CloudGate SSO.
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
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96347498"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Esercitazione: Configurare Soloinsight-CloudGate SSO per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Soloinsight-CloudGate SSO e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Soloinsight-CloudGate SSO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Soloinsight-CloudGate SSO](https://www.soloinsight.com/)
* Un account utente in Soloinsight-CloudGate SSO con autorizzazioni di amministratore.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Assegnazione di utenti a Soloinsight-CloudGate SSO

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Soloinsight-CloudGate SSO. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Soloinsight-CloudGate SSO seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Suggerimenti importanti per l'assegnazione di utenti a Soloinsight-CloudGate SSO

* È consigliabile assegnare un singolo utente di Azure AD a Soloinsight-CloudGate SSO per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Soloinsight-CloudGate SSO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurare Soloinsight-CloudGate SSO per il provisioning

1. Accedere alla [console di amministrazione di Soloinsight-CloudGate SSO](https://soloinsight.sigateway.com/login). Passare a **Administration > System Settings** (Amministrazione > Impostazioni di sistema).

    ![Console di amministrazione di Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Passare a **General** (Generale).

    ![Aggiunta di SCIM in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Scorrere verso la fine della pagina e recuperare i valori di **Tenant URL** (URL tenant) e **Secret Token** (Token segreto). Copiare il **token segreto**. Questo valore dovrà essere immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Soloinsight-CloudGate SSO nel portale di Azure.

    ![Creazione del token in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Aggiungere Soloinsight-CloudGate SSO dalla raccolta

Prima di configurare Soloinsight-CloudGate SSO per il provisioning utenti automatico con Azure AD, è necessario aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Soloinsight-CloudGate SSO**, selezionare **Soloinsight-CloudGate SSO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Soloinsight-CloudGate SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurazione del provisioning utenti automatico in Soloinsight-CloudGate SSO 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Soloinsight-CloudGate SSO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Soloinsight-CloudGate SSO, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Soloinsight-CloudGate SSO](./soloinsight-cloudgate-sso-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Soloinsight-CloudGate SSO in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Soloinsight-CloudGate SSO**.

    ![Collegamento di Soloinsight-CloudGate SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://sigateway.com/scim/v2/sync/serviceproviderconfig` in **URL tenant**. Immettere il valore del **token di autenticazione SCIM** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Soloinsight-CloudGate SSO. Se la connessione non riesce, verificare che l'account Soloinsight-CloudGate SSO abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Soloinsight-CloudGate SSO** (Sincronizza utenti di Azure Active Directory con Soloinsight-CloudGate SSO).

    ![Mapping di utenti in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati tra Azure AD e Soloinsight-CloudGate SSO nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Soloinsight-CloudGate SSO** (Sincronizza gruppi di Azure Active Directory con Soloinsight-CloudGate SSO).

    ![Mapping di gruppi in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Soloinsight-CloudGate SSO nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo in Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Soloinsight-CloudGate SSO, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Soloinsight-CloudGate SSO scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Soloinsight-CloudGate SSO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)