---
title: Come configurare l'assegnazione di applicazioni self-service| Microsoft Docs
description: Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373962"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Come configurare un'assegnazione di applicazioni self-service

Prima che gli utenti possano individuare automaticamente le applicazioni dal proprio App personali, è necessario abilitare l'accesso alle applicazioni **self-service** a tutte le applicazioni a cui si vuole consentire agli utenti di eseguire l'individuazione autonoma e richiedere l'accesso. Questa funzionalità è disponibile per le applicazioni aggiunte da [Azure AD Gallery,](./add-application-portal.md)Azure AD Application Proxy [o](./application-proxy.md) sono state aggiunte tramite il consenso dell'utente o [dell'amministratore.](../develop/application-consent-experience.md) 

Questa funzionalità è un modo efficace per consentire di risparmiare tempo e denaro al gruppo IT ed è altamente consigliata come parte di una distribuzione moderna di applicazioni con Azure Active Directory.

Questa funzionalità permette di:

-   Consentire agli utenti di individuare autonomamente le [applicazioni App personali](https://myapps.microsoft.com/) senza preoccuparsi del gruppo IT.

-   Aggiungere gli utenti a un gruppo pre-configurato in modo che sia possibile verificare chi ha richiesto l'accesso, rimuovere l'accesso e gestire i ruoli assegnati.

-   Facoltativamente, consentire al revisore aziendale di approvare l'accesso alle applicazioni in modo da rimuovere questa azione dalle operazioni del gruppo IT.

-   Facoltativamente, configurare un massimo di 10 persone che possono approvare l'accesso a questa applicazione.

-   Facoltativamente, consentire a un responsabile approvazione aziendale di impostare le password che gli utenti possono usare per accedere all'applicazione, direttamente dal responsabile approvazione [aziendale App personali](https://myapps.microsoft.com/).

-   Facoltativamente, assegnare automaticamente gli utenti dell'accesso self-service direttamente a un ruolo applicazione.

> [!NOTE]
> Una Azure Active Directory Premium (P1 o P2) è necessaria per gli utenti per richiedere l'aggiunta a un'app self-service e per consentire ai proprietari di approvare o rifiutare le richieste. Senza una licenza Azure Active Directory Premium, gli utenti non possono aggiungere app self-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare automaticamente le applicazioni e, facoltativamente, consentire al gruppo aziendale di approvare l'accesso a tali applicazioni. Per le applicazioni con accesso Single Sign-On basato su password, è anche possibile consentire al gruppo aziendale di gestire le credenziali assegnate a tali utenti dai propri pannelli App personali di accesso.

Per abilitare l'accesso self-service per un'applicazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **Applicazioni aziendali**.

3. Selezionare l'applicazione nell'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, usare i controlli del filtro per selezionare il tipo di applicazione, lo stato o la visibilità e quindi selezionare **Applica**.

4. Nel menu di spostamento a sinistra selezionare **Self-service.**

5. Per abilitare l'accesso self-service per questa applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.

6. Accanto a **A quale gruppo devono essere aggiunti gli utenti assegnati?** fare clic su Seleziona **gruppo.** Scegliere un gruppo e quindi fare clic su **Seleziona.** Quando la richiesta di un utente viene approvata, verrà aggiunta a questo gruppo. Quando si visualizza l'appartenenza a questo gruppo, sarà possibile vedere chi ha ottenuto l'accesso all'applicazione tramite l'accesso self-service.
  
    > [!NOTE]
    > Questa impostazione non supporta i gruppi sincronizzati dall'ambiente locale.

7. **Facoltativo:** Per richiedere l'approvazione aziendale prima che agli utenti sia consentito l'accesso, impostare l'interruttore Richiedi approvazione prima di concedere l'accesso a **questa applicazione?** su **Sì.**

8. **Facoltativo:** per le applicazioni che usano solo l'accesso Single Sign-On per le password, per consentire ai responsabili approvazione aziendali di specificare le password inviate a questa applicazione per gli utenti approvati, impostare l'interruttore Consenti ai responsabili approvazione di impostare le password **dell'utente** per questa applicazione? su **Sì.**

9. **Facoltativo:** Per specificare i responsabili approvazione aziendali autorizzati ad approvare l'accesso a questa applicazione, accanto a Chi è autorizzato ad approvare l'accesso a questa **applicazione?** fare clic su Seleziona responsabili approvazione e quindi selezionare fino a 10 responsabili approvazione aziendali singoli. Quindi fare clic su **Seleziona**.

    >[!NOTE]
    >I gruppi non sono supportati. È possibile selezionare fino a 10 singoli responsabili approvazione aziendali. Se si specificano più responsabili approvazione, qualsiasi singolo responsabile approvazione può approvare una richiesta di accesso.

10. **Facoltativo:** per le applicazioni che espongono ruoli **,** per assegnare utenti approvati self-service a un ruolo, accanto a A quale ruolo assegnare gli utenti in **questa applicazione?**, fare clic su Seleziona ruolo **e** quindi scegliere il ruolo a cui assegnare questi utenti. Quindi fare clic su **Seleziona**.

11. Per terminare, fare clic sul pulsante **Salva** nella parte superiore del riquadro.

Dopo aver completato la configurazione dell'applicazione [self-service,](https://myapps.microsoft.com/) gli utenti possono passare al App personali e fare clic sul pulsante Aggiungi **app self-service** per trovare le app abilitate con accesso self-service. I responsabili approvazione aziendali visualizzano anche una notifica nel [App personali](https://myapps.microsoft.com/). È possibile abilitare un messaggio di posta elettronica per informare i responsabili dell'approvazione quando un utente richiede l'accesso a un'applicazione per cui è necessaria l'approvazione.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione di Azure Active Directory per la gestione self-service dei gruppi](../enterprise-users/groups-self-service-management.md)