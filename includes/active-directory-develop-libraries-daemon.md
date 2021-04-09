---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008000"
---
| Linguaggio/Framework | Progetto in<br/>GitHub                                                                 | Pacchetto                                                                                | Ottenere<br/>avviata                           | Consentire l'accesso degli utenti                                            | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Nodo               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nodo](https://www.npmjs.com/package/@azure/msal-node)  | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [MSAL-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/