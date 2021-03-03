---
title: Usare identità gestite in servizi di comunicazione
titleSuffix: An Azure Communication Services quickstart
description: Le identità gestite consentono di autorizzare l'accesso ai servizi di comunicazione di Azure da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni e altre risorse.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657621"
---
# <a name="use-managed-identities"></a>Usare identità gestite
Introduzione ai servizi di comunicazione di Azure usando identità gestite. L'identità dei servizi di comunicazione e le librerie client SMS supportano l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questa Guida introduttiva illustra come autorizzare l'accesso alle librerie client di identità e SMS da un ambiente Azure che supporta le identità gestite. Viene inoltre descritto come testare il codice in un ambiente di sviluppo.

## <a name="prerequisites"></a>Prerequisiti

 - Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free)
 - Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Configurazione

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Abilitare le identità gestite in una macchina virtuale o in un servizio app

Le identità gestite devono essere abilitate nelle risorse di Azure che si sta autorizzando. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Servizi app](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Assegnare i ruoli di Azure con il portale di Azure

1. Passare al portale di Azure.
1. Passare alla risorsa del servizio di comunicazione di Azure.
1. Passare a controllo di accesso (IAM) menu-> + Aggiungi-> Aggiungi assegnazione di ruolo.
1. Selezionare il ruolo "collaboratore" (questo è l'unico ruolo supportato).
1. Selezionare "identità gestita assegnata dall'utente" o "identità gestita assegnata dal sistema", quindi selezionare l'identità desiderata. Salvare la selezione.

![Ruolo identità gestita](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Assegnare i ruoli di Azure con PowerShell

Per assegnare ruoli e autorizzazioni tramite PowerShell, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end