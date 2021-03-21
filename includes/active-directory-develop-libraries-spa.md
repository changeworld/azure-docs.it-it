---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010703"
---
| Linguaggio/Framework | Progetto in<br/>GitHub                                                                                                    | Pacchetto                                                                      | Ottenere<br/>avviata                             | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [Angolare MSAL V2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| Angular              | [MSAL angolare](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Esercitazione](../articles/active-directory/develop/tutorial-v2-angular.md)| ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| JavaScript           | [MSAL.js V2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Esercitazione](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Esercitazione](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

<sup>2</sup> [flusso del codice di autenticazione][auth-code-flow] con solo PCKE (scelta consigliata). 

<sup>3</sup> solo [flusso di concessione implicita][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md