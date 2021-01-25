---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760690"
---
## <a name="retrying-after-errors-and-exceptions"></a>Nuovi tentativi dopo errori ed eccezioni

Si prevede di implementare i propri criteri di ripetizione dei tentativi quando si chiama MSAL. MSAL effettua chiamate HTTP al servizio Azure AD e talvolta possono verificarsi errori. Ad esempio, la rete può diventare inattiva o il server è sovraccarico.  

### <a name="http-429"></a>HTTP 429

Quando il servizio token di sicurezza è sovraccarico a causa di un numero eccessivo di richieste, restituisce l'errore HTTP 429 con un suggerimento sul tempo disponibile per riprovare nel campo della risposta `Retry-After`.