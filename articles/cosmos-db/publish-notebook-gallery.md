---
title: Pubblicare notebook nella raccolta Azure Cosmos DB notebook
description: Informazioni su come scaricare Notebook dalla raccolta pubblica, modificarli e pubblicare Notebook personalizzati nella raccolta.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039326"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Pubblicare notebook nella raccolta Azure Cosmos DB notebook
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB notebook Jupyter incorporati sono integrati direttamente negli account Azure Cosmos DB nel portale di Azure. Usando questi notebook, è possibile analizzare e visualizzare i dati dal portale di Azure. I notebook predefiniti per Azure Cosmos DB sono attualmente disponibili in [molte aree](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Per usare i notebook, [creare un nuovo account Cosmos](create-cosmosdb-resources-portal.md) o [abilitare i notebook in un account esistente](enable-notebooks.md) in una di queste aree.

L'ambiente notebook nel portale di Azure include alcuni esempi pubblicati dal team di Azure Cosmos DB. Dispone inoltre di una raccolta pubblica in cui è possibile pubblicare e condividere i propri notebook. Dopo che un notebook è stato pubblicato nella raccolta, è disponibile per tutti gli utenti Azure Cosmos DB da visualizzare e usare. In questo articolo si apprenderà come usare i notebook dalla raccolta pubblica e pubblicare il notebook nella raccolta.

## <a name="download-a-notebook-from-the-gallery"></a>Scaricare un notebook dalla raccolta

Usare la procedura seguente per visualizzare e scaricare Notebook dalla raccolta all'area di lavoro dei notebook:

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'account di Azure Cosmos DB abilitato con l'ambiente del notebook.

1. Passare alla   >  scheda della raccolta pubblica di Esplora dati **Notebooks**  >  **Gallery**  >   .

1. Accettare il [codice di comportamento](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  prima di visualizzare o pubblicare notebook nella raccolta. Il codice di comportamento viene registrato per ogni utente, in base al livello di sottoscrizione. Quando si passa a una sottoscrizione diversa, è necessario accettarla di nuovo prima di accedere alla raccolta. Per accettare il codice di comportamento, selezionare la casella di controllo e **continuare**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Passare alla raccolta di notebook pubblici e accettare il codice di comportamento.":::

1. Successivamente, è possibile aggiungere un notebook specifico alla scheda Preferiti o scaricarlo. Quando si scarica un notebook, questo viene copiato nell'area di lavoro dei notebook in cui è possibile eseguirlo o modificarlo.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Scaricare un notebook dalla raccolta all'area di lavoro.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Pubblicare un notebook nella raccolta

Quando si compilano Notebook personalizzati o si modificano i notebook esistenti per adattarsi a uno scenario diverso, può essere utile pubblicarli nella raccolta. Dopo che un notebook è stato pubblicato nella raccolta, altri utenti possono accedervi. È possibile [esplorare la raccolta di esempi di notebook](https://cosmos.azure.com/gallery.html) per visualizzare i notebook attualmente disponibili.

Per pubblicare un notebook, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'account di Azure Cosmos DB abilitato con l'ambiente del notebook.

1. Passare alla scheda Notebooks (notebook) di **Esplora dati**  >    >   .

1. Passare al notebook che si desidera pubblicare. Selezionare il pulsante **Salva** sulla barra dei comandi e quindi selezionare **pubblica in raccolta**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Scegliere un notebook da pubblicare nella raccolta.":::

   È anche possibile trovare l'opzione **pubblica nella raccolta** selezionando **...** pulsante accanto al nome del notebook:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Un altro approccio per scegliere un notebook da pubblicare nella raccolta.":::

1. Compilare il modulo **Publish to Gallery** con i dettagli seguenti:

   * **Nome:** Nome descrittivo per identificare il notebook.
   * **Descrizione:**  Breve descrizione delle operazioni del notebook.
   * **Tag:** I tag sono facoltativi e vengono usati per filtrare i risultati quando si esegue una ricerca con una parola chiave.
   * **Immagine di copertina:** Immagine usata nella pagina di copertina quando viene pubblicato il notebook. È possibile scegliere una delle opzioni seguenti:
   * **Immagine personalizzata** : è possibile caricare un'immagine dal computer. Scegliere un file di immagine con proporzioni 256x144.
   * **URL** : specificare un URL accessibile pubblicamente in cui si trova l'immagine.
   * **Acquisisci screenshot** : una schermata del notebook aperto viene automaticamente acquisita e caricata nell'anteprima.
   * **Usare prima** l'output di output della prima cella con un output visualizzato. Le celle che visualizzano solo Markdown/text non vengono conteggiate come output di visualizzazione.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Compilare il modulo Publish to Gallery.":::

   > [!NOTE]
   > Se si usa l'opzione **pubblica nella raccolta** dalla **...** accanto al nome del notebook non vengono visualizzate tutte le opzioni dell' **immagine di copertina** . Questo perché il notebook potrebbe non essere aperto e Azure Cosmos DB non avranno accesso per scattare uno screenshot o accedere al primo output visualizzato.

1. Verificare che l'anteprima risulti corretta e selezionare **pubblica**. Al momento della pubblicazione, il notebook verrà visualizzato nella raccolta pubblica dei notebook Azure Cosmos DB. Assicurarsi di aver rimosso tutti i dati sensibili o l'output prima della pubblicazione. Il contenuto del notebook viene sottoposta a scansione per eventuali violazioni dei criteri Microsoft prima della pubblicazione. Il completamento di questo processo richiede in genere pochi secondi. Se vengono rilevate violazioni, nella scheda notifica verrà visualizzato un messaggio. Il notebook non verrà pubblicato se viene trovata una violazione, si rimuove il testo violato e lo si pubblica di nuovo.

   > [!NOTE]
   > Dopo la pubblicazione dei notebook nella raccolta pubblica, tutti gli utenti Azure Cosmos DB possono visualizzarli. L'accesso non è limitato a per sottoscrizione o a livello di organizzazione.

1. Dopo che il notebook è stato pubblicato nella raccolta, è possibile visualizzarlo nella scheda **lavoro pubblicato** . È anche possibile rimuovere o eliminare i notebook pubblicati dalla raccolta pubblica.

1. È anche possibile segnalare un notebook che viola il codice di comportamento. Se viene rilevata una violazione, Cosmos DB rimuove automaticamente il notebook dalla raccolta. Se un notebook viene rimosso, gli utenti possono visualizzarlo nella scheda **lavoro pubblicato** con la nota rimossa. Per segnalare un notebook, passare alla   >  scheda della raccolta pubblica dei notebook della raccolta di Esplora dati **notebook**  >    >   . Aprire il notebook che si vuole segnalare, passare il puntatore del mouse sul pulsante? nell'angolo destro e selezionare **Segnala abusi**. 

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Segnala un notebook che viola il codice di comportamento.":::

## <a name="next-steps"></a>Passaggi successivi

* Scopri i vantaggi di [Azure Cosmos DB notebook Jupyter](cosmosdb-jupyter-notebooks.md)
* [Usare funzionalità e comandi del notebook Python](use-python-notebook-features-and-commands.md)
* [Usare funzionalità e comandi dei notebook C#](use-csharp-notebook-features-and-commands.md)
* [Importare notebook da un repository GitHub](import-github-notebooks.md)
