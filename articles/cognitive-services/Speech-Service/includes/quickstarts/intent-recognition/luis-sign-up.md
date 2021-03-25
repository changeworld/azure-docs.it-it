---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: ab22ad75b5b49588bbdcedf5fc995ce65fe4e690
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105304"
---
Per completare l'argomento di avvio rapido sul riconoscimento di finalità, è necessario creare un account e un progetto LUIS usando l'anteprima del portale LUIS. Questo argomento di avvio rapido richiede solo una sottoscrizione di LUIS. *Non* è necessario avere una sottoscrizione del servizio Voce.

Per prima cosa è necessario creare un account e un'app LUIS tramite l'anteprima del portale LUIS. L'app LUIS creata userà un dominio predefinito per la domotica, che fornisce finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud che è possibile chiamare con Speech SDK. 

Per creare l'app LUIS, seguire queste istruzioni:

* <a href="/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Guida introduttiva: creare un'app di dominio predefinita </a>

Al termine, saranno necessari quattro elementi:

* Eseguire di nuovo la pubblicazione con il **priming del riconoscimento vocale** attivato
* La **chiave primaria** di LUIS
* La **località** di LUIS
* **ID app** Luis

È possibile trovare queste informazioni nell'[anteprima del portale LUIS](https://preview.luis.ai/):

1. Nel portale di anteprima LUIS selezionare l'app e quindi selezionare il pulsante **Pubblica**.

2. Selezionare lo slot **Produzione**, se si usa `en-US` impostare l'opzione di **Priming del riconoscimento vocale** sulla posizione **attivata**. Quindi selezionare il pulsante **Pubblica**.

    > [!IMPORTANT]
    > L'opzione di **priming del riconoscimento vocale** è particolarmente consigliata, in quanto migliorerà l'accuratezza del riconoscimento vocale.

    > [!div class="mx-imgBorder"]
    > ![Pubblicare LUIS nell'endpoint](../../../media/luis/publish-app-popup.png)

3. Nell'anteprima del portale LUIS selezionare **Gestisci** e quindi **Risorse di Azure**. In questa pagina è possibile trovare la chiave e l'_area_ di LUIS.

   > [!div class="mx-imgBorder"]
   > ![Chiave e area di LUIS](../../../media/luis/luis-key-region.png)

4. Una volta ottenute la chiave e l'area, sarà necessario l'ID app. Selezionare **Impostazioni applicazione**: l'ID app è disponibile in questa pagina.

   > [!div class="mx-imgBorder"]
   > ![ID app di LUIS](../../../media/luis/luis-app-id.png)