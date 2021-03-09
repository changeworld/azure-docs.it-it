---
author: baanders
description: file di inclusione per la configurazione dell'autenticazione locale per DefaultAzureCredential negli esempi di Gemelli digitali di Azure, con introduzione
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: dcef8030c47e771e5cd771dac09b5f96e3d38abd
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473660"
---
### <a name="set-up-local-azure-credentials"></a>Configurare le credenziali di Azure locali

Questo esempio usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte della libreria `Azure.Identity`) per l'autenticazione degli utenti con l'istanza di Gemelli digitali di Azure eseguita nel computer locale. Per altre informazioni sui diversi modi con cui un'app client può eseguire l'autenticazione con Gemelli digitali di Azure, vedere [*Procedura: Scrivere il codice di autenticazione dell'app*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]