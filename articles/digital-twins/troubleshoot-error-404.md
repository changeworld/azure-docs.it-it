---
title: 'Gemelli digitali di Azure richiesta non riuscita con stato: 404 Sub-Domain non trovato'
description: "Cause e risoluzioni per 'Richiesta di servizio non riuscita. Stato: 404 Sub-Domain non trovato' in Gemelli digitali di Azure."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590283"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Richiesta di servizio non riuscita. Stato: 404 Sub-Domain non trovato

Questo articolo descrive le cause e i passaggi di risoluzione per la ricezione di un errore 404 dalle richieste di servizio Gemelli digitali di Azure. 

## <a name="symptoms"></a>Sintomi

Questo errore può verificarsi quando si accede a un'istanza di Gemelli digitali di Azure usando un'entità servizio o un account utente appartenente a un tenant Azure Active Directory [(Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) diverso dall'istanza. I ruoli [corretti sembrano](concepts-security.md) essere assegnati all'identità, ma le richieste API hanno esito negativo con stato di errore . `404 Sub-Domain not found`

## <a name="causes"></a>Cause

### <a name="cause-1"></a>Causa #1

Gemelli digitali di Azure richiede che tutti gli utenti di autenticazione appartengano allo stesso tenant Azure AD'istanza Gemelli digitali di Azure locale.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Soluzioni

### <a name="solution-1"></a>Soluzione #1

È possibile risolvere questo problema richiedendo a ogni identità federata di un altro tenant di richiedere un **token** dal tenant "home" dell'istanza di Gemelli digitali di Azure. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Soluzione #2

Se si usa la classe nel codice e si continua a riscontrare questo problema dopo aver ottenere un token, è possibile specificare il tenant principale nelle opzioni per chiarire il tenant anche quando l'autenticazione è di tipo `DefaultAzureCredential` `DefaultAzureCredential` diverso.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla sicurezza e sulle autorizzazioni Gemelli digitali di Azure:
* [*Concetti: Sicurezza per Gemelli digitali di Azure soluzioni*](concepts-security.md)
