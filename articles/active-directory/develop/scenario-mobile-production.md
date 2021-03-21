---
title: Preparare le API Web per la chiamata di app per dispositivi mobili per la produzione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web (Preparare le app per la produzione).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675958"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparare le app per dispositivi mobili per la produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e l'affidabilità dell'app per dispositivi mobili prima di spostarla nell'ambiente di produzione.

## <a name="handle-errors"></a>Gestire gli errori

Quando si prepara un'app per dispositivi mobili per la produzione, possono verificarsi diverse condizioni di errore. I casi principali che si gestiranno sono gli errori e i fallback invisibile all'interazione. Altre condizioni che è necessario considerare includono situazioni senza rete, interruzioni del servizio, requisiti per il consenso dell'amministratore e altri casi specifici dello scenario.

Per ogni tipo di Microsoft Authentication Library (MSAL), è possibile trovare il codice di esempio e il contenuto wiki che descrivono come gestire le condizioni di errore:

- [Wiki di MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki di MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Per provare esempi aggiuntivi, vedere [app client pubbliche per desktop e dispositivi mobili](sample-v2-code.md#desktop-and-mobile-public-client-apps).