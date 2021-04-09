---
title: 'Esercitazione: Configurare Blink per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Blink.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e67c3157f1d354fe79b2e4bb4b6e822aaf3e7506
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731349"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Esercitazione: Configurare Blink per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Blink e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti in Blink.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Blink](https://joinblink.com/pricing)
* Un account utente in Blink con autorizzazioni di amministratore.

## <a name="assigning-users-to-blink"></a>Assegnazione di utenti a Blink

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i membri dei gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o membri di gruppi in Azure AD devono poter accedere a Blink. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Blink seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Suggerimenti importanti per l'assegnazione di utenti a Blink

* È consigliabile assegnare un singolo utente di Azure AD a Blink per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Blink, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-blink-for-provisioning"></a>Configurare Blink per il provisioning

1. Registrare un [caso di supporto](https://support.joinblink.com) o inviare un messaggio di posta elettronica al **supporto tecnico di Blink** all'indirizzo support@joinblink.com per richiedere un token SCIM.

2.  Copiare il valore di **SCIM Authentication Token** (Token di autenticazione SCIM). Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Blink nel portale di Azure.

## <a name="add-blink-from-the-gallery"></a>Aggiungere Blink dalla raccolta

Prima di configurare Blink per il provisioning utenti automatico con Azure AD, è necessario aggiungere Blink dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Blink dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Blink**, selezionare **Blink** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Blink nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Configurazione del provisioning utenti automatico per Blink 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Blink in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Blink, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Blink](./blink-tutorial.md). Il Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Blink in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Blink**.

    ![Collegamento di Blink nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://api.joinblink.com/scim` in **URL tenant**. Immettere il valore del **token di autenticazione SCIM** recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Blink. Se la connessione non riesce, verificare che l'account Blink abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Blink** (Sincronizza utenti di Azure Active Directory in Blink).

    ![Mapping utente di Blink](media/blink-provisioning-tutorial/User_mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Blink nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Blink per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |title|string|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |phoneNumbers[type eq "work"].value|string|
   |phoneNumbers[type eq "mobile"].value|string|
   |externalId|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
   |urn:ietf:params:scim:schemas:extension:blink:2.0:User:company|string|
   urn:ietf:params:scim:schemas:extension:blink:2.0:User:description|string|
   urn:ietf:params:scim:schemas:extension:blink:2.0:User:location|string|

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Blink, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti di cui si vuole effettuare il provisioning in Blink selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Blink.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).  


## <a name="change-log"></a>Registro delle modifiche

* 14/01/2021 - Sono stati aggiunti gli attributi di estensione personalizzati **company**, **description** e **location**.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)