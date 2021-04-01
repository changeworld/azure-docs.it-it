---
title: 'Esercitazione: configurare Playvox per il provisioning utenti automatico usando Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862480"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Esercitazione: configurare Playvox per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da seguire sia per Playvox che per Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti o gruppi in [Playvox](https://www.playvox.com) usando il servizio di provisioning Azure ad. Per informazioni importanti sul funzionamento di questo servizio e sul relativo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Playvox.
> * Rimuovere gli utenti in Playvox quando non sono più necessari per l'accesso.
> * Mantieni gli attributi utente sincronizzati tra Azure AD e Playvox.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario di questa esercitazione si presuppone che siano già presenti i prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md).
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, Ad esempio, un account potrebbe avere l'amministratore dell'applicazione, l'amministratore dell'applicazione cloud, il proprietario dell'applicazione o il ruolo di amministratore globale.
* Un account utente in [Playvox](https://www.playvox.com) con autorizzazioni di amministratore con privilegi avanzati.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1: pianificare la distribuzione del provisioning

1. Informazioni [sul funzionamento del servizio di provisioning](../app-provisioning/user-provisioning.md).

2. Determinare chi sarà [nell'ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Determinare quali dati eseguire il [mapping tra Azure ad e Playvox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Passaggio 2: configurare Playvox per supportare il provisioning tramite Azure AD

1. Accedere alla console di amministrazione di Playvox e passare a **impostazioni > chiavi API**.

2. Selezionare **Crea chiave API**.

    ![Screenshot parziale che mostra la posizione del pulsante Crea chiave API nell'interfaccia utente di Playvox.](media/playvox-provisioning-tutorial/create.png)

3. Immettere un nome significativo per la chiave API e quindi selezionare **Salva**. Dopo la generazione della chiave API, selezionare **Chiudi**.

4. Nella chiave API creata selezionare l'icona **Dettagli** .

    ![Screenshot parziale che mostra la posizione dell'icona dei dettagli, che è una lente di ingrandimento, nell'interfaccia utente di Playvox.](media/playvox-provisioning-tutorial/api.png)

5. Copiare e salvare il valore della **chiave Base64** . In un secondo momento, nella portale di Azure, immettere questo valore nella casella di testo **token segreto** nella scheda **provisioning** dell'applicazione Playvox.

    ![Screenshot della finestra di messaggio chiave API dettagli, con il valore della chiave BASE64 evidenziato.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Passaggio 3: aggiungere Playvox dalla raccolta di applicazioni Azure AD

Per iniziare a gestire il provisioning in Playvox, aggiungere Playvox al tenant di Azure AD dalla raccolta di applicazioni. Per altre informazioni, vedere [Guida introduttiva: aggiungere un'applicazione al tenant di Azure Active Directory (Azure ad)](../manage-apps/add-application-portal.md).

Se in precedenza è stato configurato Playvox per Single Sign-On (SSO), è possibile usare la stessa applicazione. Tuttavia, è consigliabile creare un'applicazione separata quando si testa inizialmente l'integrazione.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4: definire chi sarà nell'ambito del provisioning

Usare il servizio di provisioning Azure AD per definire l'ambito di cui verrà eseguito il provisioning, in base all'assegnazione all'applicazione o agli attributi dell'utente o del gruppo. Per definire l'ambito di chi verrà sottoposta a provisioning per l'app in base all'assegnazione, vedere [gestire l'assegnazione utente per un'app in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) per informazioni su come assegnare utenti o gruppi all'applicazione. Per definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, usare un filtro di ambito come descritto in [provisioning di applicazioni basate su attributi con filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Ricordare questi punti:

* Quando si assegnano utenti a Playvox, è necessario selezionare un ruolo diverso dall'accesso predefinito. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'accesso predefinito è l'unico ruolo disponibile nell'applicazione, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli.

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti o gruppi prima di distribuirlo a tutti. Quando l'ambito del provisioning è basato su utenti o gruppi assegnati, è possibile controllare le dimensioni del set assegnando solo uno o due utenti o gruppi all'app. Quando nell'ambito del provisioning sono inclusi tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) per limitare le dimensioni del set di test.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Passaggio 5: configurare il provisioning utenti automatico in Playvox

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti o gruppi, in base alle assegnazioni di utenti o gruppi in Azure AD.

Per configurare il provisioning utenti automatico per Playvox in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Screenshot parziale del portale di Azure, con applicazioni aziendali e tutti gli elementi delle applicazioni evidenziati](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni cercare e selezionare **Playvox**.

    ![Screenshot parziale dell'elenco di applicazioni, con la casella di ricerca dell'applicazione evidenziata.](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot parziale che mostra la voce di menu del provisioning.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot parziale della scheda provisioning, che mostra l'opzione automatica selezionata nella casella di riepilogo a discesa modalità di provisioning.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere l' **URL del tenant** di Playvox come:

    `https://{tenant}.playvox.com/scim/v1`

    Immettere il **token Secret** copiato in precedenza nel passaggio 2. Selezionare quindi **Test connessione** per assicurarsi che Azure ad possa connettersi a Playvox. Se la connessione non riesce, verificare che l'account Playvox disponga delle autorizzazioni di amministratore e riprovare.

    ![Screenshot parziale che mostra la sezione credenziali amministratore, incluse le caselle di testo URL tenant e token segreto, e il collegamento Test connessione evidenziato.](common/provisioning-testconnection-tenanturltoken.png)

6. Nella casella di testo **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Quindi, selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot parziale che mostra la casella di testo della posta elettronica di notifica e la casella di controllo notifica posta elettronica.](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Playvox**.

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Playvox nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Playvox per le operazioni di aggiornamento. Se si sceglie di modificare l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), assicurarsi che l'API Playvox supporti il filtraggio degli utenti in base a tale attributo. Selezionare **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |active|Boolean|
   |displayName|string|
   |emails[type eq "work"].value|string|
   |name.givenName|string|
   |name.familyName|string|
   |name.formatted|string|
   |externalId|string|

10. Per configurare i filtri di definizione dell'ambito, vedere le istruzioni riportate in questa [esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Playvox, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Screenshot parziale della sezione Settings, che mostra lo stato del provisioning impostato su on.](common/provisioning-toggle-on.png)

12. Sempre in **Impostazioni** definire gli utenti o i gruppi di cui eseguire il provisioning in Playvox scegliendo i valori desiderati nell' **ambito**.

    ![Screenshot parziale della sezione Settings, che mostra la casella di riepilogo a discesa ambito.](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot parziale che mostra le opzioni di salvataggio e scarto.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo dei cicli successivi. I cicli successivi vengono eseguiti approssimativamente ogni 40 minuti, a condizione che il servizio di provisioning Azure AD sia in esecuzione.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6: monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [Provisioning delle applicazioni in stato di quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)