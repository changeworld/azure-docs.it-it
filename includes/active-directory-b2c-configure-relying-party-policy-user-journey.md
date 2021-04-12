---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98951529"
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



