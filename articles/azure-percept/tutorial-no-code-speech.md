---
title: Creare un assistente vocale con Azure Percept DK e Azure Percept audio
description: Informazioni su come creare e distribuire una soluzione di sintesi vocale senza codice in Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 3c5e6fd62e4f4db9ccc1306d32d09b8338cbf963
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098027"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Creare un assistente vocale con Azure Percept DK e Azure Percept audio

In questa esercitazione verrà creato un assistente vocale da un modello da usare con Azure Percept DK e Azure Percept audio. La demo di Assistente vocale viene eseguita in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e contiene una selezione di oggetti virtuali controllati dalla voce. Per controllare un oggetto, pronunciare la parola chiave, ovvero una parola o una frase breve che riattiva il dispositivo, seguito da un comando. Ogni modello risponde a un set di comandi specifici.

Questa guida illustra il processo di configurazione dei dispositivi, la creazione di un assistente vocale e le risorse necessarie per i [servizi vocali](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) , il test dell'Assistente vocale, la configurazione della parola chiave e la creazione di parole chiave personalizzate.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- Audio di Azure Percept
- Altoparlante o cuffie che possono connettersi a un jack audio da 3,5 mm (facoltativo)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): la DevKit è stata connessa a una rete di Wi-Fi, è stato creato un hub Internet e la DevKit è stata connessa all'hub Internet.
- [Installazione audio di Azure Percept](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Creare un assistente vocale usando un modello disponibile

1. Passare ad [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Aprire la scheda **demo & esercitazioni** .

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Screenshot della Home page di portale di Azure.":::

1. Fare clic su **prova i modelli di Assistente vocale** in **esercitazioni vocali e demo**. Verrà visualizzata una finestra sul lato destro dello schermo.

1. Nella finestra eseguire le operazioni seguenti:

    1. Nel menu a discesa **Hub** tutto selezionare l'hub Internet a cui è connessa la devkit.

    1. Nel menu a discesa **dispositivo** selezionare il devkit.

    1. Selezionare uno dei modelli di Assistente vocale disponibili.

    1. Fare clic sulla casella di controllo Accetto le **condizioni & per questo progetto** .

    1. Fare clic su **Crea**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Screenshot della creazione di modelli di Assistente vocale.":::

1. Dopo aver fatto clic su **Crea**, il portale apre un'altra finestra per creare la risorsa del tema vocale. Nella finestra eseguire le operazioni seguenti:

    1. Selezionare la sottoscrizione di Azure nella casella **sottoscrizione** .

    1. Selezionare il gruppo di risorse preferito dal menu a discesa **gruppo di risorse** . Se si vuole creare un nuovo gruppo di risorse da usare con l'Assistente vocale, fare clic su **Crea** nel menu a discesa e seguire le istruzioni.

    1. In **prefisso applicazione** immettere un nome. Questo sarà il prefisso per il progetto e il nome del comando personalizzato.

    1. In **Region (area**) selezionare l'area in cui distribuire le risorse.

    1. Nel piano **tariffario Luis Prediction** selezionare **standard** (il livello gratuito non supporta le richieste di riconoscimento vocale).

    1. Fare clic sul pulsante **Create** (Crea). Le risorse per l'applicazione di Assistente vocale verranno distribuite nella sottoscrizione.

        > [!WARNING]
        > Non **chiudere la** finestra fino al termine della distribuzione della risorsa da parte del portale. La chiusura prematura della finestra può causare un comportamento imprevisto dell'Assistente vocale. Una volta distribuita la risorsa, verrà visualizzata la demo.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Screenshot della finestra di selezione della sottoscrizione e del gruppo di risorse.":::

## <a name="test-out-your-voice-assistant"></a>Testare l'Assistente vocale

Per interagire con l'Assistente vocale, pronunciare la parola chiave seguita da un comando. Quando il modello Ear è in grado di riconoscere la parola chiave, il dispositivo emette un carillon (che è possibile sentire se un altoparlante o una cuffia è connessa) e i LED lampeggeranno il blu. I LED passeranno a Racing Blue durante l'elaborazione del comando. La risposta dell'Assistente vocale al comando verrà stampata nel testo della finestra demo e emessa in modo udibile attraverso l'altoparlante o le cuffie. La parola chiave default (elencata accanto alla **parola chiave Custom**) è impostata su "computer" e ogni modello dispone di un set di comandi compatibili che consentono di interagire con gli oggetti virtuali nella finestra demo. Se ad esempio si usa la demo Hospitality o Healthcare, ad esempio "computer, accendere TV" per accendere la TV virtuale.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Screenshot della finestra demo dell'ospitalità.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Comandi demo di Hospitality e Healthcare

Sia le demo per l'assistenza sanitaria che l'ospitalità hanno TV, luci, bui e termostati che è possibile interagire con. Sono supportati i comandi e le varianti aggiuntive seguenti:

* "Accendere/spegnere le luci".
* "Accendere/spegnere la TV".
* "Attivare/disattivare l'AC".
* "Apri/Chiudi i bui".
* "Impostare la temperatura su X gradi". (X è la temperatura desiderata, ad esempio 75).

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Screenshot della finestra demo sanitaria.":::

### <a name="automotive-demo-commands"></a>Comandi demo Automotive

La demo automobilistica ha una postazione virtuale più calda, defroster e termostato con cui è possibile interagire. Sono supportati i comandi e le varianti aggiuntive seguenti:

* "Attivare/disattivare il defroster".
* "Attivare/disattivare il posto più caldo".
* "Impostare la temperatura su X gradi". (X è la temperatura desiderata, ad esempio 75).
* "Aumento/riduzione della temperatura per Y gradi".


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Screenshot della finestra demo automobilistica.":::

### <a name="inventory-demo-commands"></a>Comandi demo di inventario

La demo di inventario ha una selezione di caselle blu, gialle e verdi virtuali con cui interagire insieme a un'app di inventario virtuale. Sono supportati i comandi e le varianti aggiuntive seguenti:

* "Aggiungi/Rimuovi X caselle". (X è il numero di caselle, ad esempio 4).
* "Order/Ship X box".
* "Quanti sono le caselle in magazzino?"
* "Conteggio caselle Y". (Y è il colore delle caselle, ad esempio giallo).
* "Spedire tutto in magazzino".


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Screenshot della finestra demo di inventario.":::

## <a name="configure-your-keyword"></a>Configurare la parola chiave

È possibile personalizzare la parola chiave per l'applicazione dell'Assistente vocale.

1. Fare clic su **Cambia** accanto a **parola chiave personalizzata** nella finestra demo.

1. Selezionare una delle parole chiave disponibili. Sarà possibile scegliere tra una selezione di parole chiave di esempio e qualsiasi parola chiave personalizzata creata.

1. Fare clic su **Salva**.

### <a name="create-a-custom-keyword"></a>Creare una parola chiave personalizzata

È possibile creare una parola chiave personalizzata per l'applicazione vocale. Il training per la parola chiave personalizzata può essere completato in pochi minuti.

1. Fare clic su **+ Crea parola chiave personalizzata** nella parte superiore della finestra demo. 

1. Immettere la parola chiave desiderata, che può essere una parola singola o una breve frase.

1. Selezionare la **risorsa vocale** (elencata accanto al **comando personalizzato** nella finestra demo e contiene il prefisso dell'applicazione).

1. Fare clic su **Salva**. 

## <a name="create-a-custom-command"></a>Creare un comando personalizzato

Il portale fornisce anche la funzionalità per la creazione di comandi personalizzati con le risorse vocali esistenti. "Comando personalizzato" si riferisce all'applicazione di Assistente vocale, non a un comando specifico all'interno dell'applicazione esistente. Creando un comando personalizzato, si crea un nuovo progetto vocale, che è necessario sviluppare ulteriormente in [speech studio](https://speech.microsoft.com/).

Per creare un nuovo comando personalizzato nella finestra demo, fare clic su **+ Crea comando personalizzato** nella parte superiore della pagina ed eseguire le operazioni seguenti:

1. Immettere un nome per il comando personalizzato.

1. Immettere una descrizione del progetto (facoltativo).

1. Selezionare la lingua preferita.

1. Selezionare la risorsa di riconoscimento vocale.

1. Selezionare la risorsa LUIS.

1. Selezionare la risorsa LUIS authoring o crearne una nuova.

1. Fare clic su **Crea**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Screenshot della finestra di creazione dei comandi personalizzati.":::

Dopo aver creato un comando personalizzato, è necessario passare a [speech studio](https://speech.microsoft.com/) per ulteriori attività di sviluppo. Se si apre speech studio e non si Visualizza il comando personalizzato elencato, attenersi alla procedura seguente:

1. Nel pannello del menu a sinistra in Azure Percept Studio fare clic su **voce vocale** in **progetti di intelligenza artificiale**.

1. Selezionare la scheda **comandi** .

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Screenshot dell'elenco di comandi personalizzati disponibili per la modifica.":::

1. Selezionare il comando personalizzato che si desidera sviluppare. Verrà aperto il progetto in speech studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Screenshot della schermata iniziale di speech studio.":::

Per ulteriori informazioni sullo sviluppo di comandi personalizzati, vedere la [documentazione relativa al servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>L'Assistente vocale è stato creato ma non risponde ai comandi

Controllare i LED luminosi sulla scheda interposer:

* Tre luci blu a tinta unita indicano che l'Assistente vocale è pronto e in attesa della parola chiave.
* Se il LED centrale (L02) è bianco, l'inizializzazione di DevKit è stata completata e deve essere configurata con una parola chiave.
* Se il LED centrale (L02) lampeggia in bianco, l'inizializzazione dell'audio SoM non è ancora stata completata. Il completamento dell'inizializzazione può richiedere alcuni minuti.

Per altre informazioni sugli indicatori LED, vedere l' [articolo](./audio-button-led-behavior.md)relativo ai LED.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Assistente vocale non risponde a una parola chiave personalizzata creata in speech studio

Questo problema può verificarsi se il modulo vocale non è aggiornato. Seguire questa procedura per aggiornare il modulo vocale alla versione più recente:

1. Fare clic su **dispositivi** nel pannello di menu a sinistra della Home page di Azure Percept Studio.

1. Trovare e selezionare il dispositivo.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Screenshot dell'elenco dei dispositivi in Azure Percept Studio.":::

1. Nella finestra dispositivo selezionare la scheda **sintesi vocale** .

1. Controllare la versione del modulo di riconoscimento vocale. Se è disponibile un aggiornamento, verrà visualizzato un pulsante **Aggiorna** accanto al numero di versione.

1. Fare clic su **Aggiorna** per distribuire l'aggiornamento del modulo vocale. Il completamento del processo di aggiornamento richiede in genere 2-3 minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'utilizzo dell'applicazione di Assistente vocale, attenersi alla procedura seguente per pulire le risorse vocali distribuite durante questa esercitazione:

1. Dal [portale di Azure](https://portal.azure.com)selezionare gruppi di **risorse** dal pannello di menu a sinistra o digitarlo nella barra di ricerca.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Screenshot della Home page di portale di Azure che mostra i gruppi di risorse e il pannello di menu sinistro.":::

1. Selezionare un gruppo di risorse.

1. Selezionare tutte le sei risorse che contengono il prefisso dell'applicazione e fare clic sull'icona **Elimina** nel pannello dei menu in alto.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Screenshot delle risorse vocali selezionate per l'eliminazione.":::

1. Per confermare l'eliminazione, digitare **Yes** nella casella di conferma, verificare di aver selezionato le risorse corrette, quindi fare clic su **Elimina**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Screenshot della finestra di conferma dell'eliminazione.":::

> [!WARNING]
> Verranno rimosse tutte le parole chiave personalizzate create con le risorse vocali da eliminare e la demo di Assistente vocale non funzionerà più.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata creata una soluzione di riconoscimento vocale senza codice, provare a creare una soluzione per la [visione senza codice](./tutorial-nocode-vision.md) per Azure Percept dk.
