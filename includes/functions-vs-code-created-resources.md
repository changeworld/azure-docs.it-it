---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493500"
---
+ Un [gruppo di risorse](../articles/azure-resource-manager/management/overview.md), che è un contenitore logico per le risorse correlate.
+ Un [account di archiviazione di Azure](../articles/storage/common/storage-account-create.md)standard, che mantiene lo stato e altre informazioni sui progetti.
+ Un piano a consumo, che definisce l'host sottostante per l'app per le funzioni serverless. 
+ Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse all'interno dello stesso piano di hosting.
+ Un'istanza di Application Insights connessa all'app per le funzioni, che tiene traccia dell'utilizzo della funzione serverless.