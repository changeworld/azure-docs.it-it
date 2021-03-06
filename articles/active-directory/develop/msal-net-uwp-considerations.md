---
title: Considerazioni su UWP (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni relative all'uso di piattaforma UWP (Universal Windows Platform) (UWP) con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122477"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerazioni sull'uso di piattaforma UWP (Universal Windows Platform) con MSAL.NET
Gli sviluppatori di applicazioni che usano piattaforma UWP (Universal Windows Platform) (UWP) con MSAL.NET devono prendere in considerazione i concetti presentati in questo articolo.

## <a name="the-usecorporatenetwork-property"></a>Proprietà UseCorporateNetwork
Nella piattaforma Windows Runtime (WinRT) è presente `PublicClientApplication` la proprietà booleana `UseCorporateNetwork` . Questa proprietà consente alle applicazioni Windows 10 e alle applicazioni UWP di trarre vantaggio dall'autenticazione integrata di Windows (IWA) se l'utente ha effettuato l'accesso a un account che dispone di un tenant federato Azure Active Directory (Azure AD). Gli utenti che hanno eseguito l'accesso al sistema operativo possono anche usare Single Sign-On (SSO). Quando si imposta la `UseCorporateNetwork` proprietà, MSAL.NET utilizza un gestore di autenticazione Web (WAB).

> [!IMPORTANT]
> `UseCorporateNetwork`Se si imposta la proprietà su true, si presuppone che lo sviluppatore dell'applicazione abbia abilitato IWA nell'applicazione. Per abilitare l'autenticazione integrata:
> - Nella scheda funzionalità dell'applicazione UWP `Package.appxmanifest` abilitare le  funzionalità seguenti:
>   - **Autenticazione aziendale**
>   - **Reti private (client e server)**
>   - **Certificato utente condiviso**

Per impostazione predefinita, la funzionalità IWA non è abilitata perché Microsoft Store richiede un livello elevato di verifica prima di accettare le applicazioni che richiedono le funzionalità di autenticazione aziendale o certificati utente condivisi. Non tutti gli sviluppatori vogliono eseguire questo livello di verifica.

Nella piattaforma UWP l'implementazione WAB sottostante non funziona correttamente negli scenari aziendali in cui è abilitato l'accesso condizionale. Gli utenti visualizzano i sintomi di questo problema quando provano ad accedere usando Windows Hello. Quando all'utente viene richiesto di scegliere un certificato:

- Il certificato per il PIN non è stato trovato.
- Quando l'utente sceglie un certificato, non viene richiesto il PIN.

È possibile provare a evitare questo problema usando un metodo alternativo, ad esempio l'autenticazione tramite nome utente e password, ma l'esperienza non è valida.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Alcuni clienti hanno segnalato il seguente errore di accesso in ambienti aziendali specifici in cui sanno che hanno una connessione a Internet e che la connessione funziona con una rete pubblica.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

È possibile evitare questo problema verificando che WAB (il componente Windows sottostante) consenta una rete privata. È possibile eseguire questa operazione impostando una chiave del registro di sistema:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Per ulteriori informazioni, vedere [Web Authentication broker-Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Passaggi successivi
Negli esempi seguenti vengono fornite ulteriori informazioni.

Esempio | Piattaforma | Descrizione 
|------ | -------- | -----------|
|[Active-Directory-DotNet-native-UWP-V2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | Piattaforma UWP | Applicazione client UWP che usa MSAL.NET. Accede a Microsoft Graph per un utente che esegue l'autenticazione tramite un endpoint Azure AD 2,0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Novell-native-V2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | App Novell Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite la piattaforma di identità Microsoft. Viene inoltre illustrato come accedere a Microsoft Graph e visualizzare il token risultante. <br>![Diagramma che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite la piattaforma di identità Microsoft.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
