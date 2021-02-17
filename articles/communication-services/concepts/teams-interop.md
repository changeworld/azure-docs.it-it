---
title: Interoperabilità delle riunioni di Teams
titleSuffix: An Azure Communication Services concept document
description: Partecipare alle riunioni di Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 34d2a18d2e556b7eb9916d058c4d80fe0956ba55
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554743"
---
# <a name="teams-interoperability"></a>Interoperabilità di Teams

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

È possibile usare Servizi di comunicazione di Azure per creare esperienze personalizzate di riunioni che interagiscono con Microsoft Teams. Gli utenti delle soluzioni per i servizi di comunicazione possono interagire con i team partecipanti attraverso la voce, il video, la chat e la condivisione dello schermo.

L'interoperabilità del team consente di creare applicazioni personalizzate che connettono gli utenti alle riunioni del team. Gli utenti delle applicazioni personalizzate non devono necessariamente avere identità di Azure Active Directory o licenze di Teams per provare questa funzionalità. Questa soluzione è ideale per coinvolgere i dipendenti (che possono avere familiarità con Teams) e gli utenti esterni (che usano un'esperienza di applicazione personalizzata) in un'esperienza di riunione senza soluzione di continuità. Ad esempio:

1. I dipendenti usano Teams per pianificare una riunione 
1. I dettagli della riunione sono condivisi con utenti esterni tramite l'applicazione personalizzata.
   * **Utilizzo di API Graph** L'applicazione Servizi di comunicazione personalizzata usa le API Microsoft Graph per accedere ai dettagli della riunione da condividere. 
   * **Utilizzo di altre opzioni** Il collegamento alla riunione, ad esempio, può essere copiato dal calendario in Microsoft teams.
1. Gli utenti esterni usano l'applicazione personalizzata per partecipare alla riunione dei team (tramite le librerie client per la chiamata e la chat dei servizi di comunicazione)

L'architettura generale per questo caso d'uso è simile alla seguente: 

![Architettura per l'interoperabilità di Teams](./media/call-flows/teams-interop.png)

Sebbene alcuni team soddisfino le funzionalità, ad esempio la modalità di generazione, la modalità insieme e le chat room, saranno disponibili solo per gli utenti del team, l'applicazione personalizzata avrà accesso alle funzionalità di base audio, video, chat e condivisione dello schermo della riunione.

Quando un utente di Servizi di comunicazione partecipa alla riunione di Teams, agli utenti di Teams verrà mostrato il nome visualizzato fornito tramite la libreria client Chiamata. L'utente di Servizi di comunicazione verrà altrimenti trattato come un utente anonimo in Teams.  Per l'applicazione personalizzata è necessario considerare l'autenticazione degli utenti e altre misure di sicurezza, in modo da proteggere le riunioni di Teams. Tenere presenti le implicazioni per la sicurezza derivanti dalla partecipazione di utenti anonimi alle riunioni e seguire la [guida alla sicurezza di Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) per configurare le funzionalità disponibili per utenti anonimi.

L'interoperabilità tra i team di servizi di comunicazione è attualmente in anteprima privata. Se disponibile a livello generale, gli utenti di servizi di comunicazione verranno considerati come "utenti di accesso esterno". Scopri di più sull'accesso esterno in [Call, chat e collabora con persone esterne all'organizzazione in Microsoft teams](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations).

Gli utenti di Servizi di comunicazione possono partecipare a riunioni pianificate di Teams purché la partecipazione anonima sia abilitata nelle [impostazioni](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Teams in Government Community Cloud (GCC)
L'interoperabilità dei servizi di comunicazione di Azure non è compatibile con le distribuzioni di team che usano i [cloud di Microsoft 365 Government (GCC)](/MicrosoftTeams/plan-for-government-gcc) al momento. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere l'app di chiamata a una riunione di Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
