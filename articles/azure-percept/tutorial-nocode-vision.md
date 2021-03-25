---
title: Creare una soluzione per la visione senza codice in Azure Percept Studio
description: Informazioni su come creare una soluzione per la visione senza codice in Azure Percept studio e distribuirla in Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023130"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Creare una soluzione per la visione senza codice in Azure Percept Studio

Azure Percept Studio consente di creare e distribuire soluzioni personalizzate per la visione artificiale, senza necessità di scrivere codice. Contenuto dell'articolo:

- Creare un progetto di visione in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Raccogli le immagini di training con la DevKit
- Etichettare le immagini di training in [visione personalizzata](https://www.customvision.ai/)
- Eseguire il training del modello di classificazione o del rilevamento oggetti personalizzato
- Distribuire il modello in DevKit
- Migliorare il modello impostando la ripetizione del training

Questa esercitazione è adatta per gli sviluppatori che non hanno alcuna esperienza di intelligenza artificiale e hanno appena iniziato a usare Azure Percept.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- Esperienza di installazione di Azure Percept DK: la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.

## <a name="create-a-vision-prototype"></a>Creare un prototipo di visione

1. Avviare il browser e passare ad [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Nella pagina Panoramica fare clic sulla scheda **demo & esercitazioni** .  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Schermata di Panoramica di Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. In **esercitazioni e demo di Vision** fare clic su **Crea un prototipo di visione**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Schermata delle demo e delle esercitazioni di Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Nella pagina **New Azure Percept visione personalizzata Prototype** eseguire le operazioni seguenti:

    1. Nella casella **nome progetto** immettere un nome per il prototipo della visione.

    1. Immettere una descrizione del prototipo della visione nella casella **Descrizione progetto** .

    1. Selezionare **Azure PERCEPT dk** nel menu a discesa **tipo di dispositivo** .

    1. Selezionare una risorsa nel menu a discesa **risorse** oppure fare clic su **Crea una nuova risorsa**. Se si sceglie di creare una nuova risorsa, eseguire le operazioni seguenti nella finestra **Crea** :
        1. Immettere un nome per la nuova risorsa.
        1. Selezionare la sottoscrizione di Azure.
        1. selezionare un gruppo di risorse o crearne uno nuovo.
        1. Selezionare l'area preferita.
        1. Selezionare il piano tariffario (si consiglia S0).
        1. Fare clic su **Crea** nella parte inferiore della finestra.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Finestra Crea risorsa.":::

    1. In **tipo di progetto** scegliere se il progetto visione eseguirà il rilevamento degli oggetti o la classificazione delle immagini. Per ulteriori informazioni sui tipi di progetto, fare clic su **help me choose**.

    1. Per **ottimizzazione**, scegliere se si desidera ottimizzare il progetto per l'accuratezza, una latenza di rete bassa o un saldo di entrambi.

    1. Fare clic sul pulsante **Create** (Crea).

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Crea la schermata del prototipo della visione personalizzata.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Connettere un dispositivo al progetto e acquisire immagini

Dopo la creazione di una soluzione per la visione artificiale, è necessario aggiungere il DevKit e il relativo hub Internet corrispondente.

1. Accendere la devkit.

1. Nel menu a discesa **Hub** tutto selezionare l'hub delle cose a cui è stata connessa la DevKit durante la configurazione guidata.

1. Nel menu a discesa **dispositivi** selezionare il devkit.

Successivamente, è necessario caricare immagini o acquisire immagini per il training del modello di intelligenza artificiale. Si consiglia di caricare almeno 30 immagini per tipo di tag. Se ad esempio si vuole creare un rilevatore di cani e gatti, è necessario caricare almeno 30 immagini di cani e 30 immagini dei gatti. Per acquisire immagini con il SoM della visione del DevKit, seguire questa procedura:

1. Nella finestra **acquisizione immagine** selezionare **Visualizza flusso dispositivo** per visualizzare il flusso video som visione.

1. Controllare il flusso video per assicurarsi che la videocamera SoM della visione sia allineata correttamente per scattare le immagini di training. Apportare le modifiche necessarie.

1. Nella finestra **acquisizione immagine** fare clic su **scatta foto**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Schermata di acquisizione dell'immagine.":::

1. In alternativa, configurare un'acquisizione automatica delle immagini per raccogliere una grande quantità di immagini alla volta selezionando la casella **acquisizione automatica immagine** . Selezionare la frequenza di imaging preferita in **frequenza di acquisizione** e il numero totale di immagini che si desidera raccogliere in **destinazione**. Fare clic su **imposta acquisizione automatica** per avviare il processo di acquisizione automatica delle immagini.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Menu a discesa acquisizione immagini automatica.":::

Quando si dispone di un numero sufficiente di foto, fare clic su **Avanti: contrassegna le immagini e il training del modello** nella parte inferiore della schermata. Tutte le immagini verranno salvate in [visione personalizzata](https://www.customvision.ai/).

> [!NOTE]
> Se si sceglie di caricare immagini di training direttamente in Visione personalizzata, si noti che le dimensioni del file di immagine non possono superare i 6 MB.

## <a name="tag-images-and-train-your-model"></a>Contrassegnare le immagini ed eseguire il training del modello

Prima di eseguire il training del modello, aggiungere etichette alle immagini.

1. Nella pagina **tag images and Model Training** fare clic su **Open Project in visione personalizzata**.

1. Sul lato sinistro della pagina **visione personalizzata** fare clic su senza **tag** sotto **tag** per visualizzare le immagini appena raccolte nel passaggio precedente. Selezionare una o più immagini senza tag.

1. Nella finestra **Dettagli immagine** fare clic sull'immagine per iniziare l'assegnazione di tag. Se è stato selezionato rilevamento oggetti come tipo di progetto, è necessario anche creare un [rettangolo](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) di selezione intorno a oggetti specifici che si desidera contrassegnare. Modificare il rettangolo di delimitazione in base alle esigenze. Digitare il tag oggetto e fare clic su **+** per applicare il tag. Se, ad esempio, si crea una soluzione per la visione che invia una notifica quando è necessario eseguire il riapprovvigionamento di uno scaffale del negozio, aggiungere il tag "scaffale vuoto" alle immagini degli scaffali vuoti e aggiungere il tag "shelf completo" alle immagini degli scaffali completamente archiviati. Ripetere per tutte le immagini senza tag.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Schermata di Tag Image in Visione personalizzata.":::

1. Dopo l'assegnazione di tag alle immagini, fare clic sull'icona **X** nell'angolo superiore destro della finestra. Fare clic su **Tagged** in **tag** per visualizzare tutte le immagini appena contrassegnate.

1. Una volta etichettate le immagini, si è pronti per eseguire il training del modello di intelligenza artificiale. A tale scopo, fare clic su **Train** nella parte superiore della pagina. È necessario avere almeno 15 immagini per ogni tipo di tag per eseguire il training del modello (si consiglia di usare almeno 30). Il training richiede in genere circa 30 minuti, ma potrebbe richiedere più tempo se il set di immagini è molto grande.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Selezione dell'immagine di training con il pulsante Train evidenziato.":::

1. Al termine del training, nella schermata vengono visualizzate le prestazioni del modello. Per ulteriori informazioni sulla valutazione di questi risultati, consultare la [documentazione relativa alla valutazione del modello](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector). Dopo il training, è anche possibile [testare il modello](../cognitive-services/custom-vision-service/test-your-model.md) su immagini aggiuntive e ripetere il training in modo necessario. Ogni volta che si esegue il training del modello, questo verrà salvato come nuova iterazione. Per ulteriori informazioni su come migliorare le prestazioni del modello, fare riferimento alla [documentazione visione personalizzata](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) .

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Risultati del training del modello.":::

    > [!NOTE]
    > Se si sceglie di testare il modello su immagini aggiuntive in Visione personalizzata, tenere presente che le dimensioni del file dell'immagine di test non possono superare 4 MB.

Una volta soddisfatte le prestazioni del modello, chiudere Visione personalizzata chiudendo la scheda Esplorazione.

## <a name="deploy-your-ai-model"></a>Distribuire il modello di intelligenza artificiale

1. Tornare alla scheda Azure Percept studio e fare clic su **Avanti: Evaluate e Distribuisci** nella parte inferiore della schermata.

1. La finestra **valuta e Distribuisci** mostrerà le prestazioni dell'iterazione del modello selezionato. Selezionare l'iterazione che si desidera distribuire in DevKit nel menu a discesa **iterazione modello** e fare clic su **Distribuisci modello** nella parte inferiore della schermata.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Schermata di distribuzione del modello." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Dopo aver distribuito il modello, visualizzare il flusso video del dispositivo per vedere l'inferenza del modello in azione.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Flusso del dispositivo che mostra il rilevatore di cuffie in azione.":::

Dopo la chiusura di questa finestra, è possibile tornare indietro e modificare il progetto visione in qualsiasi momento facendo clic su **visione** in **progetti di intelligenza artificiale** nella Home page di Azure Percept studio e selezionando il nome del progetto visione.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Pagina del progetto visione." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Migliorare il modello impostando la ripetizione del training

Dopo aver eseguito il training del modello e averlo distribuito al dispositivo, è possibile migliorare le prestazioni del modello impostando i parametri di ripetizione del training per acquisire più dati di training. Questa funzionalità viene usata per migliorare le prestazioni di un modello sottoposto a training offrendo la possibilità di acquisire immagini in base a un intervallo di probabilità. Ad esempio, è possibile impostare il dispositivo in modo che acquisisca solo le immagini di training quando la probabilità è bassa. Di seguito sono riportate alcune [indicazioni aggiuntive](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) sull'aggiunta di altre immagini e sul bilanciamento dei dati di training.

1. Per configurare la ripetizione del training, tornare al **progetto**, quindi al **Riepilogo del progetto**
1. Nella scheda **acquisizione immagine** selezionare **acquisizione automatica immagini** e **configurare la** ripetizione del training.
1. Configurare l'acquisizione automatica delle immagini per raccogliere una grande quantità di immagini alla volta selezionando la casella **acquisizione automatica immagine** .
1. Selezionare la frequenza di imaging preferita in **frequenza di acquisizione** e il numero totale di immagini che si desidera raccogliere in **destinazione**.
1. Nella sezione **set up retraining** selezionare l'iterazione per la quale si desidera acquisire altri dati di training, quindi selezionare l'intervallo di probabilità. Solo le immagini che soddisfano il tasso di probabilità verranno caricate nel progetto.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="acquisizione di immagini.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se per questa esercitazione è stata creata una nuova risorsa di Azure e non si vuole più sviluppare o usare la soluzione per la visione, seguire questa procedura per eliminare la risorsa:

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
1. Fare clic su **tutte le risorse**.
1. Fare clic sulla casella di controllo accanto alla risorsa creata durante questa esercitazione. Il tipo di risorsa verrà elencato come **Servizi cognitivi**.
1. Fare clic sull'icona **Elimina** nella parte superiore della schermata.

## <a name="video-walkthrough"></a>Procedura dettagliata video

Per una procedura dettagliata visiva dei passaggi descritti in precedenza, vedere il video seguente:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Passaggi successivi

Successivamente, consultare gli articoli sulle procedure per informazioni sulle funzionalità aggiuntive della soluzione di visione di Azure Percept Studio.

<!--
Add links to how-to articles and oobe article.
-->