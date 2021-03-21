---
author: baanders
description: file di inclusione per DefaultAzureCredential negli esempi di dispositivi gemelli digitali di Azure-Nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473664"
---
>[!NOTE]
> Questo esempio usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte della libreria `Azure.Identity`) per l'autenticazione degli utenti con l'istanza di Gemelli digitali di Azure eseguita nel computer locale. Con questo tipo di autenticazione, l'esempio cercherà le credenziali di Azure nell'ambiente locale, ad esempio un account di accesso da un'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) locale o in Visual Studio/Visual Studio Code.
>
> Per altre informazioni sull'uso di `DefaultAzureCredential` e altre opzioni di autenticazione, vedere [*procedura: scrivere codice di autenticazione dell'app*](../articles/digital-twins/how-to-authenticate-client.md).