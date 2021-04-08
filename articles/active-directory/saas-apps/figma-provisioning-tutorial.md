---
title: 'Esercitazione: Configurare Figma per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353919"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Esercitazione: Configurare Figma per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Figma e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Figma.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Figma](https://www.figma.com/pricing/).
* Un account utente in Figma con autorizzazioni di amministratore.

## <a name="assign-users-to-figma"></a>Assegnare utenti a Figma.
Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Figma. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Figma seguendo le istruzioni riportate di seguito:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Suggerimenti importanti per l'assegnazione di utenti a Figma

 * È consigliabile assegnare un singolo utente di Azure AD Figma per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Figma, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-figma-for-provisioning"></a>Configurare Figma per il provisioning

Prima di configurare Figma per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni da Figma.

1. Accedere alla [console di amministrazione di Figma](https://www.Figma.com/). Fare clic sull'icona dell'ingranaggio accanto al tenant.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Screenshot della console di amministrazione di Figma. È visibile un tenant denominato AAD Scim Test. Accanto al tenant è evidenziata l'icona di un ingranaggio." border="false":::

2. Passare a **General > Update Log in Settings** (Generale > Aggiorna impostazioni di accesso).

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Screenshot della scheda General della console di amministrazione di Figma. In Log in and provisioning è evidenziata l'opzione Update log in settings." border="false":::

3. Copiare l'**ID tenant**. Questo valore verrà usato per creare l'URL dell'endpoint SCIM da immettere nel campo **URL tenant** nella scheda Provisioning dell'applicazione Figma nel portale di Azure.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Screenshot della sezione SAML SSO nella console di amministrazione di Figma. L'etichetta ID tenant e il collegamento adiacente Copy sono evidenziati." border="false":::

4. Scorrere in basso e fare clic su **Generate API Token** (Genera token API).

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Screenshot della sezione SCIM provisioning nella console di amministrazione di Figma. Il collegamento Generate API token è evidenziato." border="false":::

5. Copiare il valore di **API Token** (Token API). Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Figma nel portale di Azure. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Screenshot di una pagina della console di amministrazione di Figma. Nella sezione API token per il provisioning è evidenziato un segnaposto per il token." border="false":::

## <a name="add-figma-from-the-gallery"></a>Aggiungere Figma dalla raccolta

Per configurare Figma per il provisioning utenti automatico con Azure AD, è necessario aggiungere Figma dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Figma**, selezionare **Figma** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Figma nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configurazione del provisioning utenti automatico in Figma 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Figma in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Figma, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Figma](figma-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Figma in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Figma**.

    ![Collegamento a Figma nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://www.figma.com/scim/v2/<TenantID>` in **URL tenant**, dove **TenantID** è il valore recuperato in precedenza da Figma. Immettere il valore di **API Token** (Token API) in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Figma. Se la connessione non riesce, verificare che l'account Figma abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mappings** selezionare **Synchronize Azure Active Directory Users to Figma** (Sincronizza utenti di Azure Active Directory con Figma).

    ![Mapping di utenti in Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Esaminare gli attributi utente sincronizzati tra Azure AD e Figma nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Figma per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente in Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Figma, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui effettuare il provisioning in Figma scegliendo i valori appropriati **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Figma.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)