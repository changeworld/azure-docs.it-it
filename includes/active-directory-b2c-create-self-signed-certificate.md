---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102095290"
---
Se non si dispone già di un certificato, è possibile usare un certificato autofirmato. Un certificato autofirmato è un certificato di sicurezza non firmato da un'autorità di certificazione (CA) e non fornisce le garanzie di sicurezza di un certificato firmato da un'autorità di certificazione. 

# <a name="windows"></a>[Windows](#tab/windows)

In Windows usare il cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) di PowerShell per generare un certificato.

1. Eseguire questo comando di PowerShell per generare un certificato autofirmato. Modificare l'argomento `-Subject` in base al nome dell'applicazione e del tenant Azure AD B2C. Si può anche modificare la data `-NotAfter` per specificare una scadenza diversa per il certificato.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Aprire **Gestire i certificati utente** > **Utente corrente** > **Personale** > **Certificati** > *nomeapp.nometenant.onmicrosoft.com*.
1. Selezionare il certificato e quindi fare clic su **azione**  >  **tutte le attività**  >  **Esporta**.
1. Selezionare **Sì** > **Avanti** > **Sì, esporta la chiave privata** > **Avanti**.
1. Accettare le impostazioni predefinite di **Formato file di esportazione**.
1. Specificare una password per il certificato.

Per Azure AD B2C accettare la password del file con estensione pfx, è necessario crittografare la password con l'opzione TripleDES-SHA1 nell'utilità di esportazione dell'archivio certificati Windows anziché AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

In macOS usare [Assistente certificati](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) nell'accesso Keychain per generare un certificato.

1. Seguire le istruzioni per [creare certificati autofirmati nell'accesso Keychain su Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. Nell'app Keychain Access (accesso portachiavi) nel Mac selezionare il certificato creato.
1. Scegliere   >  **Esporta elementi**.
1. Selezionare un nome file per salvare il certificato. Ad esempio, **autofirmato-certificate. P12**.
1. Per il **formato di file**, selezionare **Personal Information Exchange (. P12)**.
1. Selezionare **Salva**.
1. Immettere una **password** e quindi **verificare** la password.
1. Sostituire l'estensione di file con `.pfx` . Ad esempio, **self-signed-certificate. pfx**.

---