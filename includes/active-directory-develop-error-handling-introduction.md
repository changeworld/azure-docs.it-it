---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760647"
---
Questo articolo presenta una panoramica dei diversi tipi di errori e fornisce raccomandazioni per la gestione degli errori di accesso comuni.

## <a name="msal-error-handling-basics"></a>Informazioni di base sulla gestione degli errori di MSAL

Le eccezioni in Microsoft Authentication Library (MSAL) sono destinate agli sviluppatori di app per la risoluzione dei problemi e non per la visualizzazione agli utenti finali. I messaggi di eccezione non sono localizzati.

Durante l'elaborazione di eccezioni ed errori, è possibile usare il tipo di eccezione e il codice di errore per distinguere le eccezioni.  Per un elenco di codici di errore, vedere [autenticazione di Azure ad e codici di errore di autorizzazione](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Durante l'esperienza di accesso, è possibile che si verifichino errori relativi a consenso, accesso condizionale (autenticazione a più fattori, gestione dei dispositivi, restrizioni basate sulla posizione), rilascio e riscatto di token e proprietà utente.

La sezione seguente fornisce altri dettagli sulla gestione degli errori per l'app.