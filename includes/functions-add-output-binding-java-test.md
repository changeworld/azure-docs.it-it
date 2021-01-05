---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739816"
---
Poiché l'archetipo crea anche un set di test, è necessario aggiornare questi test per gestire il nuovo parametro `msg` nella firma del metodo `run`.  

Passare al percorso del codice di test, _src/test/java_, aprire il file di progetto *Function.java* e sostituire la riga di codice sotto `//Invoke` con il codice seguente.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
