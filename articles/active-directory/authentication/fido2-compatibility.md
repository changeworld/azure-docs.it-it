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
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632102"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>Supporto browser dell'autenticazione con password FIDO2

Azure Active Directory consente l'uso delle [chiavi di sicurezza di FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) come dispositivo con password. La disponibilità dell'autenticazione FIDO2 per gli account Microsoft è stata [annunciata nel 2018](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910). Come illustrato nell'annuncio, alcune funzionalità facoltative ed estensioni per la specifica FIDO2 CTAP devono essere implementate per supportare l'autenticazione protetta con gli account Microsoft e Azure Active Directory. Il diagramma seguente illustra i browser e le combinazioni del sistema operativo che supportano l'autenticazione senza password usando le chiavi di autenticazione FIDO2 con Azure Active Directory.

## <a name="supported-browsers"></a>Browser supportati

Questa tabella mostra il supporto per l'autenticazione di Azure Active Directory (Azure AD) e gli account Microsoft (MSA). Gli account Microsoft vengono creati dai consumer per servizi quali Xbox, Skype o Outlook.com. I tipi di dispositivi supportati sono **USB**, Near-Field Communication (**NFC**) e Bluetooth Low Energy (**BLE**).

| OS | Chrome | Chrome  | Chrome | Microsoft Edge | Microsoft Edge | Microsoft Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome supporta USB in Windows per gli account AAD.][y] | ![Chrome supporta NFC in Windows per gli account AAD.][y] | ![Chrome supporta BLE in Windows per gli account AAD.][y] | ![Edge supporta USB in Windows per gli account AAD.][y] | ![Edge supporta NFC in Windows per gli account AAD.][y] | ![Edge supporta BLE in Windows per gli account AAD.][y] | ![Firefox supporta USB in Windows per gli account AAD.][y] | ![Firefox supporta NFC in Windows per gli account AAD.][y] | ![Firefox supporta BLE in Windows per gli account AAD.][y] |
| **macOS**  | ![Chrome supporta USB in macOS per gli account AAD.][y] | ![Chrome non supporta NFC in macOS per gli account AAD.][n] | ![Chrome non supporta BLE in macOS per gli account AAD.][n] | ![Edge supporta USB in macOS per gli account AAD.][y] | ![Edge non supporta NFC in macOS per gli account AAD.][n] | ![Edge non supporta BLE in macOS per gli account AAD.][n] | ![Firefox non supporta USB in macOS per gli account AAD.][n] | ![Firefox non supporta NFC in macOS per gli account AAD.][n] | ![Firefox non supporta BLE in macOS per gli account AAD.][n] |
| **Linux**  | ![Chrome supporta USB in Linux per gli account AAD.][y] | ![Chrome non supporta NFC in Linux per gli account AAD.][n] | ![Chrome non supporta BLE in Linux per gli account AAD.][n] | ![Edge non supporta USB in Linux per gli account AAD.][n] | ![Edge non supporta NFC in Linux per gli account AAD.][n] | ![Edge non supporta BLE in Linux per gli account AAD.][n] | ![Firefox non supporta USB in Linux per gli account AAD.][n] | ![Firefox non supporta NFC in Linux per gli account AAD.][n] | ![Firefox non supporta BLE in Linux per gli account AAD.][n] |



## <a name="unsupported-browsers"></a>Browser non supportati

Il sistema operativo e le combinazioni di browser seguenti non sono supportati, ma è in corso l'analisi del supporto e dei test futuri. Per visualizzare un altro sistema operativo e il supporto del browser, è possibile lasciare commenti e suggerimenti utilizzando lo strumento per il feedback del prodotto nella parte inferiore della pagina.

| Sistema operativo | Browser |
| ---- | ---- |
| iOS | Safari, Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>Versione minima del browser

Di seguito sono riportati i requisiti minimi per la versione del browser. 

| Browser | Versione minima |
| ---- | ---- |
| Chrome | 76 |
| Microsoft Edge | Windows 10 versione 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup> Tutte le versioni del nuovo supporto Microsoft Edge basato su cromo Fido2. Il supporto per Microsoft Edge Legacy è stato aggiunto in 1903.

## <a name="next-steps"></a>Passaggi successivi
[Abilitare l'accesso senza password per la chiave di sicurezza (anteprima)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
