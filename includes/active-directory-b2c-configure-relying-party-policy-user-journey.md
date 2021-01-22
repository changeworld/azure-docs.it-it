---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674236"
---
## <a name="configure-the-relying-party-policy"></a>Configurare i criteri di relying party

Il criterio di relying party, ad esempio [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), specifica il percorso utente che Azure ad B2C verrà eseguito. Trovare l'elemento **DefaultUserJourney** all'interno [relying party](../articles/active-directory-b2c/relyingparty.md). Aggiornare il  **ReferenceId** in modo che corrisponda all'ID percorso utente in cui è stato aggiunto il provider di identità. 

Nell'esempio seguente, per il `CustomSignUpOrSignIn` percorso utente, **ReferenceId** è impostato su `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Caricare il criterio personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Identity Experience Framework**.
1. Selezionare **carica criteri personalizzati**, quindi caricare i due file di criteri modificati, nel seguente ordine: i criteri di estensione, ad esempio `TrustFrameworkExtensions.xml` , i criteri di relying party, ad esempio `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare i criteri di relying party, ad esempio `B2C_1A_signup_signin`
1. Per **applicazione** selezionare un'applicazione Web registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui adesso** .

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.

