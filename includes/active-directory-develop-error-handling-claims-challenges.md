---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760699"
---
## <a name="conditional-access-and-claims-challenges"></a>Accesso condizionale e richieste di attestazioni

Se i token vengono recuperati in modalità invisibile all'utente, l'applicazione potrebbe riscontrare errori quando una [richiesta di verifica delle attestazioni di accesso condizionale](../articles/active-directory/develop/v2-conditional-access-dev-guide.md), ad esempio criteri di autenticazione a più fattori, è necessaria per un'API a cui si sta tentando di accedere.

Il modello per gestire questo errore consiste nell'acquisire in modo interattivo un token tramite MSAL. In questo modo viene richiesto all'utente e viene offerta la possibilità di soddisfare i criteri di accesso condizionale richiesti.

Quando si chiama un'API che richiede l'Accesso Condizionale, in alcuni casi è possibile ricevere una richiesta di attestazioni nell'errore dall'API. Se ad esempio i criteri di Accesso Condizionale prevedono un dispositivo gestito (Intune), l'errore sarà simile a [AADSTS53000: è necessario un dispositivo gestito per l'accesso a questa risorsa](../articles/active-directory/develop/reference-aadsts-error-codes.md). In questo caso, è possibile passare le attestazioni nella chiamata per l'acquisizione del token, in modo che all'utente venga richiesto di soddisfare i criteri appropriati.
