---
title: Risoluzione degli errori dell'API di creazione report di Azure AD | Microsoft Docs
description: Fornisce la risoluzione degli errori durante la chiamata delle API di creazione report di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533709"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Risoluzione degli errori dell'API di creazione report di Azure Active Directory

Questo articolo elenca i messaggi di errore comuni che è possibile eseguire durante l'accesso ai report attività usando l'API Microsoft Graph e i passaggi per la risoluzione.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Errore interno 500 del server HTTP durante l'accesso all'endpoint Microsoft Graph versione 2

L'endpoint Microsoft Graph versione 2 non è attualmente supportato: assicurarsi di accedere ai log attività usando l'endpoint Microsoft Graph versione 1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Nessuno dei tenant è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Errore: L'utente non appartiene a ruoli autorizzati 

Se viene visualizzato questo messaggio di errore durante il tentativo di accedere ai log di controllo o agli accessi usando l'API, assicurarsi che l'account faccia parte del **ruolo con autorizzazioni di lettura per la sicurezza** o del **ruolo lettore report** nel tenant di Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Errore: applicazione mancante Microsoft Graph'API 'Lettura tutti i dati del log di controllo'

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

[Usare le informazioni di riferimento sull'API di controllo](/graph/api/resources/directoryaudit) 
 [Usare le informazioni di riferimento sull'API](/graph/api/resources/signin) del report attività di accesso