---
ms.openlocfilehash: 9ddc240257bda29d5db5cae3eb6a830273d339cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107589870"
---
| Linguaggio/framework | Progetto in<br/>GitHub                                                                                     | Pacchetto                                                                               | Ottenere<br/>avviata                        | Consentire l'accesso degli utenti                                         | Accedere alle API Web                                                 | Disponibile a livello generale (GA) *o*<br/>Anteprima pubblica<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Elettrone             | [Msal Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | Anteprima pubblica                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [MSAL per iOS e MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Esercitazione](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| Piattaforma UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Esercitazione](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Esercitazione](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![La libreria può richiedere token ID per l'accesso utente.][y] | ![La libreria può richiedere token di accesso per le API Web protette.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1 Le</sup> [condizioni supplementari per l'utilizzo delle Microsoft Azure si][preview-tos] applicano alle librerie *nell'anteprima pubblica.*

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/