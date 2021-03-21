---
title: 'Esercitazione: Configurare Zscaler per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936584"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zscaler e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD.
* Un tenant di Zscaler.
* Un account utente in Zscaler con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'API SCIM Zscaler, disponibile per gli sviluppatori di Zscaler per gli account con il pacchetto Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Aggiunta di Zscaler dalla raccolta

Prima di configurare Zscaler per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

**Per aggiungere Zscaler dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler**, selezionare **Zscaler** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Assegnazione di utenti a Zscaler

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Zscaler seguendo le istruzioni riportate di seguito:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler

* È consigliabile assegnare un singolo utente di Azure AD a Zscaler per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zscaler, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configurazione del provisioning utenti automatico in Zscaler

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Zscaler in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Zscaler, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Zscaler](zscaler-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Quando si esegue il provisioning o il deprovisioning di utenti e gruppi, è consigliabile riavviare periodicamente il provisioning per assicurarsi che le appartenenze ai gruppi vengano aggiornate correttamente. Il riavvio forza il servizio a valutare di nuovo tutti i gruppi e ad aggiornare le appartenenze. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zscaler in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Zscaler**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zscaler**.

    ![Collegamento di Zscaler nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot della barra laterale di the Zscaler - Provisioning - Applicazione aziendale con l'opzione Provisioning evidenziata.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot della pagina Provisioning con l'opzione Modalità di provisioning impostata su Automatico.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali amministratore** immettere i valori nei campi **URL tenant** e **Token segreto** dell'account Zscaler, come descritto nel passaggio 6.

6. Per ottenere i valori di **URL tenant** e **Token segreto**, passare a **Administration > Authentication Settings** (Amministrazione > Impostazioni di autenticazione) nell'interfaccia utente del portale di Zscaler, quindi fare clic su **SAML** in **Authentication Type** (Tipo di autenticazione).

    ![Screenshot della pagina Authentication Settings.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Fare clic su **Configure SAML** (Configura SAML) per aprire le opzioni per la **configurazione di SAML**.

    ![Screenshot della finestra di dialogo Configure SAML con le caselle di testo Base URL e Bearer Token evidenziate.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM), copiare il valore di **Base URL** (URL di base) e **Bearer Token** (Token di connessione), quindi salvare le impostazioni. Copiare il valore di **Base URL** in **URL tenant** e il valore di **Bearer Token** in **Token segreto** nel portale di Azure.

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zscaler. Se la connessione non riesce, verificare che l'account Zscaler abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot della sezione Credenziali amministratore con l'opzione Test connessione evidenziata.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot della casella di testo Indirizzo di posta elettronica per le notifiche.](./media/zscaler-provisioning-tutorial/notification.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zscaler** (Sincronizza utenti di Azure Active Directory con Zscaler).

    ![Screenshot della sezione Mapping con l'opzione Synchronize Azure Active Directory Users to Zscaler (Sincronizza utenti di Azure Active Directory con Zscaler) evidenziata.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati tra Azure AD e Zscaler nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Screenshot della sezione Mapping degli attributi con i sette mapping visualizzati.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Zscaler** (Sincronizza gruppi di Azure Active Directory con Zscaler).

    ![Screenshot della sezione Mapping con l'opzione Synchronize Azure Active Directory Groups to Zscaler (Sincronizza gruppi di Azure Active Directory con Zscaler) evidenziata.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati tra Azure AD e Zscaler nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Screenshot della sezione Mapping degli attributi con i tre mapping visualizzati.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**.

    ![Screenshot dell'opzione Stato del provisioning impostata su Attivato.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Zscaler scegliendo i valori appropriati in **Ambito** nella sezione **Impostazioni**.

    ![Screenshot dell'impostazione Ambito con l'opzione Sincronizza solo utenti e gruppi assegnati evidenziata.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Screenshot della barra laterale di Zscaler - Provisioning - Applicazione aziendale con l'opzione Salva evidenziata.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zscaler.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png