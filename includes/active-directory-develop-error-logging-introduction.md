---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763480"
---
Le app Microsoft Authentication Library (MSAL) generano messaggi di log che consentono di diagnosticare i problemi. Un'app può configurare la registrazione con alcune righe di codice e avere un controllo personalizzato sul livello di dettaglio, oltre che sulla necessità di registrare o meno i dati personali o dell'organizzazione. È consigliabile creare un callback di registrazione MSAL e fornire agli utenti un modo per inviare i log quando si verificano problemi di autenticazione.

## <a name="logging-levels"></a>Livelli di registrazione

MSAL offre diversi livelli di dettaglio per la registrazione:

- Errore: indica che si è verificato un errore e che è stato generato un errore. Utilizzato per il debug e l'identificazione dei problemi.
- Avviso: non è stato necessariamente generato un errore o un errore, ma sono destinati alla diagnostica e alla individuazione dei problemi.
- Info: MSAL registrerà gli eventi destinati a scopi informativi non necessariamente destinati al debug.
- Verbose: valore predefinito. MSAL registra i dettagli completi del comportamento della libreria.

## <a name="personal-and-organizational-data"></a>Dati personali e dell'organizzazione

Per impostazione predefinita, il logger MSAL non acquisisce dati personali o aziendali altamente sensibili. La libreria fornisce la possibilità di abilitare la registrazione dei dati personali e aziendali se si decide di eseguire questa operazione.

Le sezioni seguenti forniscono altre informazioni sulla registrazione degli errori di MSAL per l'applicazione.