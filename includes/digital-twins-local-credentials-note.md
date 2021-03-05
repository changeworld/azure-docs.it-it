---
author: baanders
description: file di inclusione per DefaultAzureCredential negli esempi di dispositivi gemelli digitali di Azure-Nota
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211784"
---
>[!NOTE]
> Questo esempio usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (parte della libreria `Azure.Identity`) per l'autenticazione degli utenti con l'istanza di Gemelli digitali di Azure eseguita nel computer locale. Con questo tipo di autenticazione, l'esempio cercherà le credenziali di Azure nell'ambiente locale, ad esempio un account di accesso da un'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) locale o in Visual Studio/Visual Studio Code.
>
> Per altre informazioni sull'uso di `DefaultAzureCredential` e altre opzioni di autenticazione, vedere [*procedura: scrivere codice di autenticazione dell'app*](../articles/digital-twins/how-to-authenticate-client.md).