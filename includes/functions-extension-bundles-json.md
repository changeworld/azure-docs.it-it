---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88689558"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

In `extensionBundle` sono disponibili le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| id | Spazio dei nomi per i bundle di estensioni di Funzioni di Azure Microsoft. |
| version | Versione del bundle da installare. Il runtime di Funzioni sceglie sempre la versione massima consentita definita dall'intervallo o dall'intervallo di versioni. Il valore version precedente consente tutte le versioni del bundle a partire dalla 1.0.0 fino alla 2.0.0 esclusa. Per altre informazioni, vedere la [notazione degli intervalli per specificare gli intervalli di versione](/nuget/reference/package-versioning#version-ranges). |