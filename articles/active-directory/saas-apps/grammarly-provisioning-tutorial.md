---
title: 'Esercitazione: configurare la grammatica per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a grammaticale.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: acfe74577f58cbdc0b1fe235e7fc1f661543fd90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609519"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Esercitazione: configurare la grammatica per il provisioning utenti automatico

Questa esercitazione descrive i passaggi che è necessario eseguire sia in modo grammaticale che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Se configurato, Azure AD esegue automaticamente il provisioning e il deprovisioning di utenti e gruppi per la [grammatica](https://www.grammarly.com/) usando il servizio di provisioning Azure ad. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creazione di utenti in grammatica
> * Rimuovere gli utenti in modo grammaticale quando non sono più necessari per l'accesso
> * Mantieni gli attributi utente sincronizzati tra Azure AD e la grammatica

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Un account utente in Azure AD con l'[autorizzazione](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un account di business grammaticale con accesso amministrativo.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Acquisire informazioni su [come funziona il servizio di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determinare quali dati eseguire il [mapping tra Azure ad e la grammatica](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare la grammatica per supportare il provisioning con Azure AD

Rivolgersi al rappresentante grammaticale oppure scrivere in <support@grammarly.com> per richiedere il token di provisioning.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere la grammatica dalla raccolta di applicazioni di Azure AD

Aggiungere la grammatica dalla raccolta di applicazioni Azure AD per iniziare a gestire il provisioning per la grammatica. Se in precedenza è stata impostata la grammatica per SSO, è possibile usare la stessa applicazione. Si consiglia di creare un'app separata quando si testa inizialmente l'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, vedere [questa Guida introduttiva](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning

È possibile usare il servizio di provisioning Azure AD per definire l'ambito di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito di cui verrà eseguito il provisioning basato esclusivamente sugli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto in effettuare il provisioning delle [app con filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Quando si assegnano utenti e gruppi a una grammatica, è necessario selezionare un ruolo diverso dall' **accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli.

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di procedere alla distribuzione generale. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllare questa opzione assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di definizione dell'ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Passaggio 5. Configurare il provisioning utenti automatico per la grammatica

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Clarizen One in base alle assegnazioni di utenti o gruppi in Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Configurare il provisioning utenti automatico per la grammatica in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Screenshot che mostra il riquadro Applicazioni aziendali.](common/enterprise-applications.png)

1. Nell'elenco di applicazioni selezionare **grammaticale**.

    ![Screenshot che mostra il collegamento grammaticale nell'elenco di applicazioni.](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Screenshot che mostra la scheda Provisioning.](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot che mostra la modalità di provisioning impostata su Automatico.](common/provisioning-automatic.png)

1. Nella sezione **credenziali amministratore** immettere l' **URL del tenant** grammaticale e le informazioni sul **token segreto** . Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi alla grammatica. Se la connessione non riesce, verificare che l'account di grammatica abbia le autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra le caselle URL tenant e token segreto.](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Screenshot che mostra la casella Indirizzo di posta elettronica per le notifiche.](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti con la grammatica**.

1. Esaminare gli attributi utente sincronizzati da Azure AD a grammaticali nella sezione **mapping** degli attributi. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in grammaticale per le operazioni di aggiornamento. Se si modifica l' [attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), è necessario assicurarsi che l'API grammaticale supporti il filtraggio degli utenti in base a tale attributo. Selezionare **Salva** per eseguire il commit delle modifiche.

   |Attributo|Type|Supportato per il filtro|
   |---|---|---|
   |userName|string|&check;|
   |externalId|string|
   |active|Boolean|
   |displayName|string|
   |emails[type eq "work"].value|string|


1. Per configurare i filtri di ambito, vedere le istruzioni fornite nell'esercitazione relativa al [filtro di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning Azure AD per la grammatica, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Screenshot che mostra l'opzione Stato del provisioning impostata su Sì.](common/provisioning-toggle-on.png)

1. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in base alla grammatica selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Screenshot che mostra l'ambito del provisioning.](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Screenshot che mostra il pulsante Salva.](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo iniziale richiede più tempo rispetto ai cicli successivi, che si verificano ogni 40 minuti, purché sia in esecuzione il servizio Azure AD provisioning.

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione

Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato effettuato il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, vedere [stato di provisioning dell'applicazione per la quarantena](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
