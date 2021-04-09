---
title: "Guida introduttiva: Creare un'app HoloLens con DirectX"
description: Questa Guida introduttiva illustra come creare un'app HoloLens usando gli ancoraggi degli oggetti.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047610"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Guida introduttiva: creare un'app HoloLens con gli ancoraggi degli oggetti di Azure in C++/WinRT e DirectX

Questa Guida introduttiva illustra come creare un'app HoloLens usando gli [ancoraggi degli oggetti di Azure](../overview.md) in C++/WinRT e DirectX. L'ancoraggio di oggetti di Azure è un servizio cloud gestito che converte asset 3D in modelli di intelligenza artificiale che consentono esperienze di realtà miste compatibili con gli oggetti per HoloLens. Al termine, si avrà un'app HoloLens in grado di rilevare un oggetto e la relativa posizione in un'applicazione olografica DirectX 11 (Windows universale).

Si apprenderà come:

> [!div class="checklist"]
> * Creazione e caricamento laterale di un'applicazione HoloLens
> * Rilevare un oggetto e visualizzarne il modello

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

* Un oggetto fisico nell'ambiente in uso e il relativo modello 3D (CAD o analizzato).
* Un computer Windows con installato quanto segue:
  * <a href="https://git-scm.com" target="_blank">Git per Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> con il carico di lavoro di **sviluppo piattaforma UWP (Universal Windows Platform)** e il componente **Windows 10 SDK (10.0.18362.0 o versione successiva)**
* Un dispositivo HoloLens 2 aggiornato e in cui è abilitata la [modalità sviluppatore](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) .
  * Per aggiornare alla versione più recente per HoloLens, aprire l'app **Impostazioni**, scegliere **Aggiornamento e sicurezza**, quindi selezionare **Verifica disponibilità aggiornamenti**.

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Aprire `quickstarts/apps/directx/DirectXAoaSampleApp.sln` in Visual Studio.

Modificare la **configurazione della soluzione** in **Release**, modificare la **piattaforma della soluzione** in **arm64**, selezionare **Device (dispositivo** ) dalle opzioni di destinazione della distribuzione. Compilare quindi il progetto **AoaSampleApp** facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Compila**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Configurare il progetto di Visual Studio per la distribuzione":::

## <a name="deploy-the-app-to-hololens"></a>Distribuire l'app in HoloLens

Dopo aver compilato correttamente il progetto di esempio, è possibile distribuire l'app in HoloLens.

Accendere il dispositivo HoloLens, eseguire l'accesso e connetterlo al PC con un cavo USB. Verificare che il **dispositivo** sia la destinazione di distribuzione scelta (vedere sopra).

Fare clic con il pulsante destro del mouse su progetto **AoaSampleApp** , quindi scegliere **Distribuisci** dal menu a comparsa per installare l'app. Se non viene visualizzato alcun errore nel **finestra di output** di Visual Studio, l'app verrà installata in HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Distribuisci app in HoloLens":::

Prima di avviare l'app, è necessario caricare un modello a oggetti. Seguire le istruzioni riportate nella sezione inserire il **modello a oggetti e rilevare la relativa istanza** di seguito.

Per avviare ed eseguire il debug dell'app, selezionare **debug > avviare il debug**. Per arrestare l'app, selezionare **Arresta debug** o premere **MAIUSC + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Inserire il modello a oggetti e rilevare la relativa istanza

Per eseguire l'app di esempio, è necessario creare un modello a oggetti. Si supponga di avere già un modello di mesh 3D CAD o scansionato di un oggetto nello spazio. Vedere [Guida introduttiva: inserimento di un modello 3D](./get-started-model-conversion.md) per la creazione di un modello.

Scaricare il modello, **Chair. ou** , in questo caso, nel computer. Dal portale per dispositivi HoloLens selezionare **System > file explorer > LocalAppData > AoaSampleApp > LocalState** e selezionare **Sfoglia.** Selezionare quindi il file del modello, **Chair. ou** , ad esempio, e selezionare **upload (carica**). Dovrebbe quindi essere visualizzato il file del modello nella cache locale.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Modello di caricamento del portale":::

Dal HoloLens avviare l'app **AoaSampleApp** (se è già aperta, chiuderla e riaprirla). Avvicinarsi (entro 2-metri di distanza) all'oggetto di destinazione (sedia) e analizzarlo esaminando il percorso da più prospettive. Verrà visualizzato un rettangolo delimitatore rosa intorno all'oggetto con alcuni punti gialli visualizzati vicino alla superficie dell'oggetto, che indica che è stato rilevato.

:::image type="content" source="./media/chair-detection.png" alt-text="Rilevamento della poltrona":::

Figura: è stata rilevata una poltrona con il rettangolo di delimitazione (rosa), il punto (giallo) e un'area di ricerca (casella gialla grande).

È possibile definire uno spazio di ricerca per l'oggetto nell'app facendo clic su di esso con il pulsante destro o sinistro. Lo spazio di ricerca passerà tra una sfera di raggio da 2 metri, un rettangolo di delimitazione di 4 m ^ 3 e una visualizzazione tronco. Per gli oggetti più grandi, ad esempio le automobili, la scelta migliore è in genere usare la selezione della vista tronco mentre si trova verso un angolo dell'oggetto a circa una distanza di 2 metri.
Ogni volta che l'area di ricerca viene modificata, l'app rimuoverà le istanze attualmente rilevate e riproverà a ritrovarle nella nuova area di ricerca.

Questa app consente di tenere traccia di più oggetti in una volta. A tale scopo, caricare più modelli nella cartella **LocalState** e impostare un'area di ricerca che copra tutti gli oggetti di destinazione. Potrebbe essere necessario più tempo per rilevare e tenere traccia di più oggetti.

L'app allinea strettamente un modello 3D alla rispettiva controparte fisica. Un utente può toccare l'aria usando la mano sinistra per attivare la modalità di rilevamento a precisione elevata, che calcola una rappresentazione più accurata. Si tratta ancora di una funzionalità sperimentale che utilizza più risorse di sistema e può comportare una maggiore instabilità nella posizione prevista. Toccare di nuovo con la parte sinistra per tornare alla modalità di rilevamento normale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: inserimento di un modello 3D](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Concetti: Panoramica dell'SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Domande frequenti](../faq.md)