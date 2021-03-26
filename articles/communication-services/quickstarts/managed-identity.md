---
title: Usare identità gestite in servizi di comunicazione
titleSuffix: An Azure Communication Services quickstart
description: Le identità gestite consentono di autorizzare l'accesso ai servizi di comunicazione di Azure da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni e altre risorse.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110747"
---
# <a name="use-managed-identities"></a>Usare identità gestite
Introduzione ai servizi di comunicazione di Azure usando identità gestite. L'identità dei servizi di comunicazione e gli SDK SMS supportano l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questa Guida introduttiva illustra come autorizzare l'accesso agli SDK di identità e SMS da un ambiente Azure che supporta le identità gestite. Viene inoltre descritto come testare il codice in un ambiente di sviluppo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per .NET](/dotnet/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../quickstarts/access-tokens.md)
- [Inviare SMS](../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../concepts/telephony-sms/concepts.md)
