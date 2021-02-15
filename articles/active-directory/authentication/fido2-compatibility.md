---
title: Supporto del browser per l'autenticazione con password FIDO2 | Azure Active Directory
description: I browser e le combinazioni del sistema operativo supportano l'autenticazione con password FIDO2 per le app che usano Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0a8f96fabdff3543222077f5113b0bd602997b7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417428"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Supporto browser dell'autenticazione con password FIDO2

Azure Active Directory consente l'uso delle [chiavi di sicurezza di FIDO2](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-keys) come dispositivo con password. La disponibilità dell'autenticazione FIDO2 per gli account Microsoft è stata [annunciata nel 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Come illustrato nell'annuncio, è necessario implementare determinate funzionalità e estensioni facoltative per la specifica CTAP di FIDO2 per supportare l'autenticazione protetta con gli account Microsoft e Azure Active Directory. Il diagramma seguente illustra i browser e le combinazioni del sistema operativo che supportano l'autenticazione senza password usando le chiavi di autenticazione FIDO2 con Azure Active Directory.

## <a name="supported-browsers"></a>Browser supportati

Questa tabella mostra il supporto per l'autenticazione di Azure Active Directory (Azure AD) e gli account Microsoft (MSA). Gli account Microsoft vengono creati dai consumer per servizi quali Xbox, Skype o Outlook.com. I tipi di dispositivi supportati sono **USB**, Near-Field Communication (**NFC**) e Bluetooth Low Energy (**BLE**).

|  | Chrome |  |  | Microsoft Edge |  |  | Firefox |  |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome supporta USB in Windows per gli account AAD.][y] | ![Chrome supporta NFC in Windows per gli account AAD.][y] | ![Chrome supporta BLE in Windows per gli account AAD.][y] | ![Edge supporta USB in Windows per gli account AAD.][y] | ![Edge supporta NFC in Windows per gli account AAD.][y] | ![Edge supporta BLE in Windows per gli account AAD.][y] | ![Firefox supporta USB in Windows per gli account AAD.][y] | ![Firefox supporta NFC in Windows per gli account AAD.][y] | ![Firefox supporta BLE in Windows per gli account AAD.][y] |
| **macOS**  | ![Chrome supporta USB in macOS per gli account AAD.][y] | ![Chrome non supporta NFC in macOS per gli account AAD.][n] | ![Chrome non supporta BLE in macOS per gli account AAD.][n] | ![Edge supporta USB in macOS per gli account AAD.][y] | ![Edge non supporta NFC in macOS per gli account AAD.][n] | ![Edge non supporta BLE in macOS per gli account AAD.][n] | ![Firefox non supporta USB in macOS per gli account AAD.][n] | ![Firefox non supporta NFC in macOS per gli account AAD.][n] | ![Firefox non supporta BLE in macOS per gli account AAD.][n] |
| **Linux**  | ![Chrome supporta USB in Linux per gli account AAD.][y] | ![Chrome non supporta NFC in Linux per gli account AAD.][n] | ![Chrome non supporta BLE in Linux per gli account AAD.][n] | ![Edge non supporta USB in Linux per gli account AAD.][n] | ![Edge non supporta NFC in Linux per gli account AAD.][n] | ![Edge non supporta BLE in Linux per gli account AAD.][n] | ![Firefox non supporta USB in Linux per gli account AAD.][n] | ![Firefox non supporta NFC in Linux per gli account AAD.][n] | ![Firefox non supporta BLE in Linux per gli account AAD.][n] |

## <a name="operating-system-versions-tested"></a>Versioni del sistema operativo testate

Le informazioni nella tabella precedente sono state testate per le seguenti versioni del sistema operativo.

| Sistema operativo | Ultima versione testata |
| --- | --- |
| Windows | Windows 10 20H2 1904 |
| macOS | Sistema operativo X 11 Big Sur |
| Linux | Workstation Fedora 32 |

## <a name="next-steps"></a>Passaggi successivi
[Abilitare l'accesso senza password per la chiave di sicurezza (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
