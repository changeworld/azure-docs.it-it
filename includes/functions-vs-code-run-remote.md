---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493518"
---
## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Nell'area **Azure: Functions** nella barra laterale espandere funzioni **del progetto locale**  >  . Fare clic con il pulsante destro del mouse su (Windows) o fare clic con il <kbd>pulsante destro del</kbd> mouse (MacOS) sulla `HttpExample` funzione e scegliere **Esegui ora funzione...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Esegui la funzione ora in Azure da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione.  

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica in Visual Studio Code.
