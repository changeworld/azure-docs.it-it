---
title: Come usare l'accesso alle applicazioni self-service in Azure AD
description: Abilitare il servizio self-service in modo che gli utenti possano trovare app Azure AD
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377616"
---
# <a name="how-to-use-self-service-application-access"></a>Come usare l'accesso alle applicazioni self-service

Prima che gli utenti possano individuare automaticamente le applicazioni dalla pagina App personali, è necessario abilitare l'accesso alle applicazioni **self-service** a tutte le applicazioni a cui si vuole consentire agli utenti di eseguire l'individuazione autonoma e richiedere l'accesso.

Questa funzionalità è un modo efficace per consentire di risparmiare tempo e denaro al gruppo IT ed è altamente consigliata come parte di una distribuzione moderna di applicazioni con Azure Active Directory.

Per altre informazioni sull'App personali dal punto di vista dell'utente finale, vedere la Guida App personali [portale](../user-help/my-apps-portal-end-user-access.md).

Questa funzionalità permette di:

-   Consentire agli utenti di individuare automaticamente le [applicazioni App personali](https://myapps.microsoft.com/) senza preoccuparsi del gruppo IT.
-   Aggiungere gli utenti a un gruppo pre-configurato in modo che sia possibile verificare chi ha richiesto l'accesso, rimuovere l'accesso e gestire i ruoli assegnati.
-   Facoltativamente, consentire a un utente di approvare le richieste di accesso alle app in modo che il gruppo IT non sia necessario.
-   Facoltativamente, configurare un massimo di 10 persone che possono approvare l'accesso a questa applicazione.
-   Facoltativamente, consentire a un utente di impostare le password che gli utenti possono usare per accedere all'applicazione.
-   Facoltativamente, assegnare automaticamente gli utenti dell'accesso self-service direttamente a un ruolo applicazione.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un modo efficace per consentire agli utenti di individuare da soli le applicazioni e, facoltativamente, al gruppo aziendale di approvare l'accesso a tali applicazioni. È possibile consentire al gruppo di business di gestire le credenziali assegnate a tali utenti per l'Single-Sign Accesso alle applicazioni direttamente dalla pagina App personali aziendale.

Per abilitare l'accesso self-service per un'applicazione, seguire questa procedura:
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2. Aprire **l Azure Active Directory di** configurazione selezionando **Tutti** i servizi nella parte superiore del menu di spostamento principale a sinistra.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Fare clic su **Applicazioni aziendali** nel menu di spostamento a sinistra di Azure Active Directory.
5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.
6. Selezionare l'applicazione per cui si desidera abilitare l'accesso self-service dall'elenco.
7. Dopo aver caricato l'applicazione, **selezionare Self-service** dal menu di spostamento a sinistra dell'applicazione.
8. Per abilitare l'accesso self-service per questa applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.
9. Quindi, per selezionare il gruppo a cui devono essere aggiunti gli utenti che richiedono l'accesso a questa applicazione, selezionare il selettore accanto all'etichetta A quale gruppo devono essere assegnati gli **utenti?** e selezionare un gruppo.
10. **Facoltativo:** se si desidera richiedere un'approvazione aziendale prima che venga consentito l'accesso agli utenti, impostare l'opzione **Richiedere l'approvazione prima di concedere l'accesso a questa applicazione?** su **Sì**.
11. **Facoltativo: per applicazioni che usano solo l'accesso Single Sign-On tramite password,** se si desidera consentire ai responsabili approvazione aziendali di specificare le password inviate a questa applicazione per gli utenti approvati, impostare l'opzione **Consentire ai responsabili approvazione di impostare le password utente per questa applicazione?** su **Sì**.
12. **Facoltativo:** Specificare i responsabili approvazione aziendali autorizzati ad approvare l'accesso a questa app. Selezionare **Chi è autorizzato ad approvare l'accesso a questa applicazione?** Selezionare quindi fino a 10 responsabili approvazione aziendali singoli.
    * I gruppi non sono supportati.
13. **Facoltativo:** per le applicazioni che espongono ruoli **,** se si desidera assegnare utenti approvati in modalità self-service a un ruolo, selezionare il selettore accanto a A quale ruolo assegnare gli utenti in questa **applicazione?** per selezionare il ruolo a cui questi utenti devono essere assegnati.
14. Selezionare il **pulsante** Salva nella parte superiore per completare l'operazione.

Dopo aver completato la configurazione dell'applicazione self-service, gli utenti possono passare a App personali e selezionare il pulsante **+Aggiungi** per trovare le app [a](https://myapps.microsoft.com/) cui è stato abilitato l'accesso self-service. I responsabili approvazione aziendali visualizzano anche una notifica [nella App personali](https://myapps.microsoft.com/) aziendale. È possibile abilitare un messaggio di posta elettronica per informare i responsabili dell'approvazione quando un utente richiede l'accesso a un'applicazione per cui è necessaria l'approvazione. 

Tali approvazioni supportano solo flussi di lavoro di approvazione individuali. Se si specificano pertanto più responsabili approvazione, uno qualsiasi di essi potrà approvare l'accesso all'applicazione.

## <a name="things-to-check-if-self-service-isnt-working"></a>Elementi da verificare se il self-service non funziona
-   Assicurarsi che l'utente o il gruppo sia stato abilitato per richiedere l'accesso alle applicazioni self-service.
-   Assicurarsi che l'utente stia visitando la posizione corretta per l'accesso alle applicazioni self-service. Gli utenti possono passare alla [App personali](https://myapps.microsoft.com/) e selezionare il pulsante **+Aggiungi** per trovare le app per cui è stato abilitato l'accesso self-service.
-   Se l'accesso alle applicazioni self-service è stato configurato di recente, provare ad accedere e disconnettersi nuovamente nel App personali dell'utente dopo alcuni minuti per verificare se sono state apportate modifiche all'accesso self-service.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione di Azure Active Directory per la gestione self-service dei gruppi](../enterprise-users/groups-self-service-management.md)
