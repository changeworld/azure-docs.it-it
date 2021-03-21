---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809567"
---
## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Tornare all'area **Azure: Functions** sulla barra laterale, espandere la sottoscrizione, la nuova app per le funzioni e le **funzioni**. Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Esegui la funzione ora in Azure da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica in Visual Studio Code.
