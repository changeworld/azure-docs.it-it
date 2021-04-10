---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007894"
---
| Piattaforma          | Progetto in<br/>GitHub                                                                          | Pacchetto                                                                               | Ottenere<br/>avviata                    | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Guida introduttiva](../articles/active-directory/develop/quickstart-v2-android.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| iOS (Swift/obj-C) | [MSAL per iOS e MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Esercitazione](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [le condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure][preview-tos] si applicano alle librerie disponibili in *anteprima pubblica*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/