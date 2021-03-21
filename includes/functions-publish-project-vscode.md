---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 2517f132578b5de6b062b38ce94581f118327a13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493592"
---
## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice.

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.


1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    - **Selezionare la cartella**: scegliere una cartella dall'area di lavoro o selezionarne una che contenga l'app per le funzioni. Questa opzione non verrà visualizzata se è già stata aperta un'app per le funzioni valida.

    - **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    - **Selezionare l'app per le funzioni in Azure**: Scegliere `- Create new Function App`. Non scegliere l'opzione `Advanced`, che non è illustrata in questo articolo.
      
    - **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure.
    
    - **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina. 
    
    L'estensione Mostra lo stato delle singole risorse man mano che vengono create in Azure nell'area di notifica.

    :::image type="content" source="media/functions-publish-project-vscode/resource-notification.png" alt-text="Notifica della creazione di risorse di Azure":::
    
1.  Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti con i nomi basati sul nome dell'app per le funzioni:
    
    [!INCLUDE [functions-vs-code-created-resources](functions-vs-code-created-resources.md)]

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. 

    [!INCLUDE [functions-vs-code-create-tip](functions-vs-code-create-tip.md)]

4. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](media/functions-publish-project-vscode/function-create-notifications.png)
