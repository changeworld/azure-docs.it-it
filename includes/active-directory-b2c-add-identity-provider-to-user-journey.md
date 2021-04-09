---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674235"
---
## <a name="add-a-user-journey"></a>Aggiungere un percorso utente 

A questo punto, il provider di identità è stato configurato, ma non è ancora disponibile in nessuna delle pagine di accesso. Se non si ha un percorso utente personalizzato, creare un duplicato di un percorso utente del modello esistente. in caso contrario, continuare con il passaggio successivo. 

1. Aprire il file *TrustFrameworkBase.xml* dallo starter pack.
1. Trovare e copiare l'intero contenuto dell'elemento **UserJourney** che include `Id="SignUpOrSignIn"`.
1. Aprire *TrustFrameworkExtensions.xml* e trovare l'elemento **UserJourneys**. Se l'elemento non esiste, aggiungerne uno.
1. Incollare l'intero contenuto dell'elemento **UserJourney** copiato come figlio dell'elemento **UserJourneys**.
1. Rinominare l'ID del percorso utente. Ad esempio: `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Aggiungere il provider di identità a un percorso utente 

Ora che si dispone di un percorso utente, aggiungere il nuovo provider di identità al percorso utente. Si aggiunge prima un pulsante di accesso, quindi si collega il pulsante a un'azione. L'azione è il profilo tecnico creato in precedenza.

1. Trovare l'elemento step dell'orchestrazione che include `Type="CombinedSignInAndSignUp"` o `Type="ClaimsProviderSelection"` nel percorso utente. Si tratta in genere del primo passaggio dell'orchestrazione. L'elemento **ClaimsProviderSelections** contiene un elenco di provider di identità a cui un utente può accedere. L'ordine degli elementi controlla l'ordine dei pulsanti di accesso presentati all'utente. Aggiungere un elemento XML **ClaimsProviderSelection** . Impostare il valore di **TargetClaimsExchangeId** su un nome descrittivo.

1. Nel passaggio successivo dell'orchestrazione aggiungere un elemento **ClaimsExchange** . Impostare l' **ID** sul valore dell'ID di scambio delle attestazioni di destinazione. aggiornare il valore di **TechnicalProfileReferenceId** con l'ID del profilo tecnico creato in precedenza.

Nel codice XML seguente vengono illustrati i primi due passaggi di orchestrazione di un percorso utente con il provider di identità: