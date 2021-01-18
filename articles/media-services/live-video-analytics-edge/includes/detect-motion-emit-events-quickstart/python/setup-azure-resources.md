---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060610"
---
Per questa esercitazione sono necessarie le risorse di Azure seguenti:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) installato

Per questo argomento di avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

1. Aprire [Azure Cloud Shell](https://shell.azure.com).
1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare **Bash** come ambiente.

    ![Selezione dell'ambiente](../../../media/quickstarts/env-selector.png)
1. Eseguire il comando seguente.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Dopo il completamento dello script, verranno visualizzate tutte le risorse necessarie nella sottoscrizione.
1. Al termine dello script, selezionare le parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file nella directory *~/clouddrive/lva-sample*. I file rilevanti per questo avvio rapido sono:

     * * **~/clouddrive/lva-sample/edge-deployment/.env** _ - Questo file contiene le proprietà usate da Visual Studio Code per distribuire moduli a un dispositivo perimetrale.
     _ * **~/clouddrive/lva-sample/appsetting.json** _ - Visual Studio Code usa questo file per eseguire il codice di esempio.
     
    Questi file saranno necessari per configurare l'ambiente di sviluppo in Visual Studio Code nella sezione successiva. È consigliabile copiarli in un file locale per il momento.
    
    ![Impostazioni app](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Se si verificano problemi con le risorse di Azure create, vedere la _ *[guida alla risoluzione dei problemi](../../../troubleshoot-how-to.md#common-error-resolutions)* * più comuni rilevati.