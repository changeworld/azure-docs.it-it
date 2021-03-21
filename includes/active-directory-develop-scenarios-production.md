---
title: includere file
description: includere file
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954961"
---
## <a name="enable-logging"></a>Abilitazione della registrazione

Per semplificare il debug e gli scenari di risoluzione degli errori di autenticazione, Microsoft Authentication Library fornisce il supporto predefinito per la registrazione. La registrazione è illustrata negli articoli seguenti:

:::row:::
    :::column:::
        - [Registrazione in MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Registrazione in MSAL per Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Registrazione in MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Registrazione in MSAL per iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Registrazione in MSAL per Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Registrazione in MSAL per Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Ecco alcuni suggerimenti per la raccolta dei dati:

- Gli utenti potrebbero chiedere assistenza in caso di problemi. Una procedura consigliata consiste nell'acquisire e archiviare temporaneamente i log. Specificare un percorso in cui gli utenti possono caricare i log. MSAL fornisce estensioni di registrazione per l'acquisizione di informazioni dettagliate sull'autenticazione.

- Se la telemetria è disponibile, abilitarla tramite MSAL per raccogliere i dati sul modo in cui gli utenti possono accedere all'app.


## <a name="validate-your-integration"></a>Convalidare l'integrazione

Testare l'integrazione seguendo l'[elenco di controllo per l'integrazione di Microsoft Identity Platform](../articles/active-directory/develop/identity-platform-integration-checklist.md).
