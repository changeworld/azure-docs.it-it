---
author: baanders
description: Includere il file per il processo di pubblicazione di una funzione di Azure da Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480753"
---
Per pubblicare il progetto in un'app per le funzioni in Azure, iniziare in Esplora soluzioni. Fare clic con il pulsante destro del mouse sul progetto e quindi **scegliere Pubblica.**

> [!IMPORTANT] 
> La pubblicazione in un'app per le funzioni in Azure comporta costi aggiuntivi per la sottoscrizione, indipendentemente Gemelli digitali di Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Screenshot della Visual Studio, che mostra il menu della soluzione con il pulsante destro del mouse. Nel menu è evidenziata l'opzione Pubblica.":::

Nella pagina **Pubblica** visualizzata lasciare selezionata la destinazione predefinita **Azure**. Selezionare quindi **Avanti**. 

Per una destinazione specifica, scegliere **App per le funzioni di Azure (Windows)** e quindi selezionare **Avanti.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Screenshot della Visual Studio, che mostra la finestra di dialogo Pubblica funzione di Azure. Nella pagina Destinazione specifica la selezione è App per le funzioni di Azure (Windows).":::

Nella scheda **Istanza di** Funzioni scegliere la sottoscrizione. Selezionare quindi l'icona del segno più (+) per creare una nuova funzione.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Screenshot della Visual Studio, che mostra la finestra di dialogo Pubblica funzione di Azure. L'icona con il segno più è evidenziata.":::

In App **per le funzioni (Windows) - Crea nuova** finestra compilare i campi seguenti:
* **Nome** è il nome del piano a consumo che verrà usato da Azure per ospitare l'app di Funzioni di Azure. Questo nome verrà applicato anche all'app per le funzioni che contiene la funzione effettiva. È possibile scegliere un valore univoco o lasciare il suggerimento predefinito.
* Assicurarsi che **la sottoscrizione** corrisponda alla sottoscrizione che si vuole usare. 
* Assicurarsi che **il gruppo di** risorse sia quello che si vuole usare.
* Lasciare **l'opzione Tipo di** piano selezionata **su Consumo**.
* Selezionare la **località** del gruppo di risorse.
* Creare una nuova **Archiviazione di Azure** risorse selezionando il **collegamento** Nuovo. Impostare la località in modo che corrisponda al gruppo di risorse, usare gli altri valori predefiniti e quindi selezionare **OK.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Screenshot della Visual Studio, che mostra la finestra di dialogo Pubblica funzione di Azure. Vengono compilati i dettagli di una nuova app per le funzioni, tra cui nome, sottoscrizione, gruppo di risorse, tipo di piano, posizione e Archiviazione di Azure.":::

Quindi selezionare **Crea**

Dopo aver creato il servizio app, viene visualizzata la scheda **Istanza di** Funzioni. La nuova app per le funzioni viene visualizzata nell'area App **per le** funzioni sotto il gruppo di risorse. Selezionare **Fine**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Screenshot della Visual Studio, che mostra la finestra di dialogo Pubblica funzione di Azure. La scheda Istanza di Funzioni è selezionata. La nuova app per le funzioni viene visualizzata nel gruppo di risorse.":::

Nel riquadro **Pubblica** visualizzato nella finestra principale Visual Studio verificare che tutte le informazioni siano corrette. Selezionare quindi **Pubblica**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Screenshot della Visual Studio, che mostra il riquadro Pubblica. Il pulsante Pubblica è evidenziato.":::

> [!NOTE]
> Se viene visualizzata una finestra popup come nell'esempio seguente, selezionare Tenta di recuperare **le credenziali da Azure** e quindi selezionare **Salva**.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Screenshot della Visual Studio, che mostra una finestra popup denominata Pubblica credenziali. Contiene i campi per un nome utente e una password. Contiene anche un pulsante per tentare di recuperare le credenziali da Azure." border="false":::
>
> Se viene visualizzato uno degli avvisi seguenti, seguire le istruzioni per eseguire l'aggiornamento alla versione Funzioni di Azure runtime:
> * "Aggiornare la versione di Funzioni in Azure".
> * "La versione del runtime di funzioni non corrisponde alla versione in esecuzione in Azure".
>
> Questi avvisi potrebbero essere visualizzati se si usa una versione precedente di Visual Studio.

L'app per le funzioni è ora pubblicata in Azure.
