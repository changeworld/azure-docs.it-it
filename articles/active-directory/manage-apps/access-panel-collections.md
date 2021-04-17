---
title: Creare raccolte per App personali portali in Azure Active Directory | Microsoft Docs
description: Usare App personali raccolte per personalizzare le App personali per un'esperienza App personali più semplice per gli utenti finali. Organizzare le applicazioni in gruppi con schede separate.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377684"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Creare le raccolte nel portale App personali

Gli utenti possono usare il App personali per visualizzare e avviare le applicazioni basate sul cloud a cui hanno accesso. Per impostazione predefinita, tutte le applicazioni a cui un utente può accedere sono elencate insieme in un'unica pagina. Per organizzare meglio questa pagina per gli utenti, se si ha una licenza Azure AD Premium P1 o P2, è possibile configurare le raccolte. Con una raccolta è possibile raggruppare le applicazioni correlate (ad esempio, in base al ruolo, all'attività o al progetto) e visualizzarle in una scheda separata. Una raccolta applica essenzialmente un filtro alle applicazioni a cui un utente può già accedere, in modo che l'utente veda solo le applicazioni nella raccolta che gli sono state assegnate.

> [!NOTE]
> Questo articolo illustra come un amministratore può abilitare e creare raccolte. Per informazioni sull'uso delle raccolte e del portale di App personali, vedere Accedere e [usare le raccolte.](../user-help/my-applications-portal-workspaces.md)

## <a name="enable-the-latest-my-apps-features"></a>Abilitare le funzionalità di App personali più recenti

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore utente o amministratore globale.

2. Passare **a** Azure Active Directory  >  **Impostazioni utente.**

3. In **Anteprime delle funzionalità utente** selezionare Gestisci le impostazioni **dell'anteprima delle funzionalità utente.**

4. In **Gli utenti possono usare le funzionalità di anteprima App personali**, scegliere una delle opzioni seguenti:
   * **Selezionato:** abilita le funzionalità per un gruppo specifico. Usare **l'opzione Selezionare un** gruppo per selezionare il gruppo per il quale si desidera abilitare le funzionalità.  
   * **Tutti:** abilita le funzionalità per tutti gli utenti.

> [!NOTE]
> Per aprire il App personali, gli utenti possono usare il collegamento o il `https://myapps.microsoft.com` collegamento personalizzato per l'organizzazione, ad esempio `https://myapps.microsoft.com/contoso.com` . Dopo aver abilitato la nuova esperienza App personali, nella parte superiore della pagina App personali verrà visualizzato il banner An  **updated My Applications is available** (Un'esperienza di applicazioni personalizzate aggiornata) e gli utenti possono selezionare Prova per visualizzare la nuova esperienza. Per interrompere l'uso della nuova esperienza, gli utenti possono selezionare **Sì** nel **banner** Lascia nuova esperienza nella parte superiore della pagina.

## <a name="create-a-collection"></a>Creare una raccolta

Per creare una raccolta, è necessario avere una Azure AD Premium P1 o P2.

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore con una licenza Azure AD Premium P1 o P2.

2. Passare a **Azure Active Directory**  >  **Applicazioni aziendali**.

3. In **Gestisci** selezionare **Raccolte**.

4. Selezionare **Nuova raccolta**. Nella pagina **Nuova raccolta** immettere **un** nome per la raccolta . È consigliabile non usare "collection" nel nome. Immettere quindi una **Descrizione.**

   ![Nuova pagina della raccolta](media/acces-panel-collections/new-collection.png)

5. Selezionare la **scheda** Applicazioni. Selezionare **+ Aggiungi applicazione** e  quindi nella pagina Aggiungi applicazioni selezionare tutte le applicazioni  da aggiungere alla raccolta oppure usare la casella di ricerca per trovare le applicazioni.

   ![Aggiungere un'applicazione alla raccolta](media/acces-panel-collections/add-applications.png)

6. Al termine dell'aggiunta di applicazioni, selezionare **Aggiungi**. Viene visualizzato l'elenco delle applicazioni selezionate. È possibile usare le frecce su per modificare l'ordine delle applicazioni nell'elenco. Per spostare un'applicazione verso il basso o eliminarla dalla raccolta, selezionare il menu **Altro** (**...**).

7. Selezionare la **scheda Proprietari.** Selezionare **+ Aggiungi utenti e** gruppi  e quindi nella pagina Aggiungi utenti e gruppi selezionare gli utenti o i gruppi a cui si vuole assegnare la proprietà. Al termine della selezione di utenti e gruppi, scegliere **Seleziona**.

9. Selezionare la **scheda Utenti e** gruppi. Selezionare **+ Aggiungi utenti e** gruppi  e quindi nella pagina Aggiungi utenti e gruppi selezionare gli utenti o i gruppi a cui si vuole assegnare la raccolta. Oppure usare la **casella di** ricerca per trovare utenti o gruppi. Al termine della selezione di utenti e gruppi, scegliere **Seleziona.**

   ![Aggiungere utenti e gruppi](media/acces-panel-collections/add-users-and-groups.png)

11. Selezionare **Rivedi e crea**. Vengono visualizzate le proprietà per la nuova raccolta.


## <a name="view-audit-logs"></a>Visualizzare i log di controllo

I log di controllo registrano App personali raccolte, incluse le azioni dell'utente finale per la creazione di raccolte. Gli eventi seguenti vengono generati da App personali:

* Creare una raccolta
* Modifica la raccolta
* Elimina raccolta
* Avviare un'applicazione (utente finale)
* Aggiunta di un'applicazione self-service (utente finale)
* Eliminazione di applicazioni self-service (utente finale)

È possibile accedere ai log di [controllo nel portale di Azure](https://portal.azure.com) selezionando Azure Active Directory log di controllo delle applicazioni aziendali   >    >   nella sezione Attività. Per **Servizio** selezionare **App personali**.

## <a name="get-support-for-my-account-pages"></a>Ottenere supporto per le pagine Account personale

Nella pagina App personali un utente può selezionare **My account** View my account (Visualizza il  >  **mio account)** per aprire le impostazioni dell'account. Nella pagina Azure AD **Account personale** gli utenti possono gestire le informazioni di sicurezza, i dispositivi, le password e altro ancora. Possono anche accedere alle impostazioni dell'account di Office.

Nel caso in cui sia necessario inviare una richiesta di supporto per un problema con la pagina dell'account Azure AD o l'account office, seguire questa procedura in modo che la richiesta venga indirizzata correttamente: 

* Per problemi con la **Azure AD "Account personale",** aprire una richiesta di supporto dall'interno del portale di Azure. Passare a **portale di Azure**  >  **Azure Active Directory** nuova  >  **richiesta di supporto.**

* Per problemi con la pagina **"Account personale"** di Office, aprire una richiesta di supporto dall'interno del interfaccia di amministrazione di Microsoft 365. Passare a **interfaccia di amministrazione di Microsoft 365**  >  **supporto.** 

## <a name="next-steps"></a>Passaggi successivi
[Esperienze per gli utenti finali per le applicazioni in Azure Active Directory](end-user-experiences.md)