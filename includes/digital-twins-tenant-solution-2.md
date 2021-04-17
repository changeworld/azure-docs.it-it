---
author: baanders
description: file di inclusione che descrive una soluzione di codice per la limitazione tra tenant con Gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589377"
---
L'esempio seguente illustra come impostare un valore id tenant per `InteractiveBrowserTenantId` nelle `DefaultAzureCredential` opzioni:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Screenshot del codice che mostra il metodo DefaultAzureCredentialOptions. Il valore di InteractiveBrowserTenantId è impostato su un valore ID tenant di esempio.":::

Sono disponibili opzioni simili per impostare un tenant per l'autenticazione con Visual Studio e Visual Studio Code. Per altre informazioni sulle opzioni disponibili, vedere la documentazione [di DefaultAzureCredentialOptions.](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)