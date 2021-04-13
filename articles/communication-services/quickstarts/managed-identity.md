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
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307440"
---
# <a name="use-managed-identities"></a>Usare identità gestite
Introduzione ai servizi di comunicazione di Azure usando identità gestite. L'identità dei servizi di comunicazione e gli SDK SMS supportano l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questa Guida introduttiva illustra come autorizzare l'accesso agli SDK di identità e SMS da un ambiente Azure che supporta le identità gestite. Viene inoltre descritto come testare il codice in un ambiente di sviluppo.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free)
- Per una risorsa di servizi di comunicazione di Azure attiva, vedere [creare una risorsa di servizi di comunicazione](./create-communication-resource.md) , se non è già presente.
- Per inviare un SMS è necessario un [numero di telefono](./telephony-sms/get-phone-number.md).
- Un'identità gestita del programma di installazione per un ambiente di sviluppo, vedere [autorizzare l'accesso con identità gestita](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per .NET](/dotnet/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../quickstarts/access-tokens.md)
- [Inviare SMS](../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../concepts/telephony-sms/concepts.md)
