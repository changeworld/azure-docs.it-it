---
title: 'Esercitazione: Configurare NetSuite OneWorld per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648085"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di NetSuite per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in NetSuite OneWorld e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a NetSuite.

> [!WARNING]
> Questa integrazione del provisioning smette di funzionare con il rilascio dell'aggiornamento Spring 2021 di NetSuite a causa di una modifica apportata alle API NetSuite usate da Microsoft per il provisioning degli utenti in NetSuite.  Questo aggiornamento raggiungerà i clienti di NetSuite tra febbraio e aprile 2021. Di conseguenza, la funzionalità di provisioning dell'applicazione NetSuite nella raccolta di app di Azure Active Directory Enterprise verrà rimossa a breve. La funzionalità SSO dell'applicazione rimarrà intatta. Microsoft collabora con NetSuite per creare una nuova integrazione di provisioning modernizzata, ma al momento non esiste alcun valore di ETA.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Una sottoscrizione di NetSuite OneWorld. Si noti che il provisioning utenti automatico è attualmente supportato solo con NetSuite OneWorld.
*   Un account utente in Netsuite con autorizzazioni di amministratore.
*   Per l'integrazione con Azure AD è necessaria un'esenzione 2FA. Per richiedere l'esenzione, contattare il team di supporto di NetSuite.

## <a name="assigning-users-to-netsuite-oneworld"></a>Assegnazione di utenti a NetSuite OneWorld

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app NetSuite. Dopo averlo stabilito, è possibile assegnare gli utenti all'app NetSuite seguendo le istruzioni riportate in:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Suggerimenti importanti per l'assegnazione di utenti a Netsuite OneWorld

*   È consigliabile assegnare un singolo utente di Azure AD a NetSuite per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a NetSuite, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di NetSuite e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in NetSuite in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP] 
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per NetSuite, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in NetSuite.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se si è già configurato NetSuite per l'accesso Single Sign-On, cercare l'istanza di NetSuite usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **NetSuite** nella raccolta di applicazioni. Selezionare NetSuite nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di NetSuite e quindi la scheda **Provisioning**.

1. Impostare **Modalità di provisioning** su **Automatico**. 

    ![Screenshot che mostra la pagina di provisioning di NetSuite, con l'opzione Modalità di provisioning impostata su Automatica e altri valori che è possibile impostare.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome di account NetSuite che abbia il profilo **Amministratore di sistema** assegnato in NetSuite.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.
      
1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app NetSuite.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva**.

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Netsuite** (Sincronizza utenti di Azure Active Directory in NetSuite).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a NetSuite. Si noti che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in NetSuite per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per NetSuite, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

1. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a NetSuite nella sezione Utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app NetSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configurare l'accesso Single Sign-On](netsuite-tutorial.md)
