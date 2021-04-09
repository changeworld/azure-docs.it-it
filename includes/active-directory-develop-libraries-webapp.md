---
ms.openlocfilehash: 124290321815d22cad74d235205401b9380ff395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578371"
---
| Linguaggio/Framework | Progetto in<br/>GitHub                                                                                     | Pacchetto                                                                                                    | Ottenere<br/>avviata                               | Consentire l'accesso degli utenti                                            | Accedere alle API Web                                                    | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![La libreria non può richiedere token ID per l'accesso utente.][n] | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET Security](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria non può richiedere token di accesso per le API Web protette.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     |[Guida introduttiva](../articles/active-directory/develop/quickstart-v2-java-webapp.md) | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
| Node.js              | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nodo](https://www.npmjs.com/package/@azure/msal-node)                                                | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-nodejs-webapp-msal.md) | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                               |
| Node.js              | [Passport Azure AD](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport-Azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-nodejs-webapp.md)      | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [MSAL](https://pypi.org/project/msal)                                                                      | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-python-webapp.md)      | ![La libreria può richiedere token ID per l'accesso utente.][y]    | ![La libreria può richiedere token di accesso per le API Web protette.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/