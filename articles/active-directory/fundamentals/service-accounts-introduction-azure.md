---
title: Introduzione alla protezione degli account del servizio Azure Active Directory
description: Spiegazione dei tipi di account del servizio disponibili in Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693168"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Introduzione alla protezione degli account dei servizi di Azure

Esistono tre tipi di account del servizio nativi da Azure Active Directory: identità gestite, entità servizio e account del servizio basati su utente. Gli account del servizio sono un tipo speciale di account progettato per rappresentare un'entità non umana, ad esempio un'applicazione, un'API o un altro servizio. Queste entità operano all'interno del contesto di sicurezza fornito dall'account del servizio. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Tipi di account del servizio Azure Active Directory

Per i servizi ospitati in Azure, è consigliabile usare un'identità gestita, se possibile, e un'entità servizio in caso contrario. Non è possibile usare le identità gestite per i servizi ospitati all'esterno di Azure. In tal caso, è consigliabile un'entità servizio. Se è possibile usare un'identità gestita o un'entità servizio, eseguire questa operazione. Si consiglia di non usare un account utente Azure Active Directory come entità servizio. Per un riepilogo, vedere la tabella seguente.
 

| Hosting del servizio| Identità gestita| Entità servizio| Account utente Azure |
| - | - | - | - |
|Il servizio è ospitato in Azure.| Sì. <br>Consigliato se il servizio <br>supporta un'identità gestita.| Sì.| Non consigliato. |
| Il servizio non è ospitato in Azure.| No| Sì. Consigliato.| Non consigliato. |
| Il servizio è multi-tenant| No| Sì. Consigliato.| No. |


## <a name="managed-identities"></a>Identità gestite

Le identità gestite sono identità sicure di Azure Active Directory (Azure AD) create per fornire identità per le risorse di Azure. Esistono [due tipi di identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types): 
 
* Le identità gestite assegnate dal sistema possono essere assegnate direttamente a un'istanza di un servizio. 

* Le identità gestite assegnate dall'utente possono essere create come risorse autonome. 

Per altre informazioni, vedere [protezione delle identità gestite](service-accounts-managed-identities.md). Per informazioni generali sulle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="service-principals"></a>Entità servizio

Se non è possibile usare un'identità gestita per rappresentare l'applicazione, usare un'entità servizio. Le entità servizio possono essere usate con applicazioni a tenant singolo e multi-tenant. 

Un'entità servizio è la rappresentazione locale di un oggetto applicazione in un singolo tenant di Azure AD. Funziona come l'identità dell'istanza dell'applicazione, definisce gli utenti che possono accedere all'applicazione e le risorse a cui l'applicazione può accedere. Un'entità servizio viene creata in (locale a) ogni tenant in cui viene usata l'applicazione e fa riferimento all'oggetto applicazione univoco globale. Il tenant protegge l'accesso dell'entità servizio e l'accesso alle risorse.

Esistono due meccanismi per l'autenticazione tramite entità servizio: i certificati client e i segreti client. I certificati sono più sicuri: usare i certificati client, se possibile. A differenza dei segreti client, i certificati client non possono essere incorporati accidentalmente nel codice.

Per informazioni sulla protezione delle entità servizio, vedere protezione delle entità servizio.

 
## <a name="next-steps"></a>Passaggi successivi


Per ulteriori informazioni sulla protezione degli account dei servizi di Azure, vedere:

[Protezione delle identità gestite](service-accounts-managed-identities.md)

[Protezione delle entità servizio](service-accounts-principal.md)

[Gestione degli account del servizio di Azure](service-accounts-governing-azure.md)



