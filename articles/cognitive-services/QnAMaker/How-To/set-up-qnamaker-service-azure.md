---
title: Configurare un servizio QnA Maker-QnA Maker
description: Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219268"
---
# <a name="manage-qna-maker-resources"></a>Gestisci risorse QnA Maker

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

Una conoscenza approfondita dei concetti seguenti è utile prima di creare la risorsa:

* [Risorse QnA Maker](../Concepts/azure-resources.md)
* [Creazione e pubblicazione di chiavi](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Creare un nuovo servizio QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

Questa procedura consente di creare le risorse di Azure necessarie per gestire il contenuto della Knowledge base. Dopo aver completato questi passaggi, le chiavi della _sottoscrizione_ sono disponibili nella pagina **chiavi** della risorsa nel portale di Azure.

1. Accedere al portale di Azure e [creare una risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selezionare **Crea** dopo aver letto i termini e le condizioni:

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker** selezionare i livelli e le aree appropriati:

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Nel campo **nome** immettere un nome univoco per identificare questo servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui verranno associate le Knowledge base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il piano **tariffario** per i servizi di gestione QnA Maker (portale e API di gestione). Vedere [altri dettagli sui prezzi degli SKU](https://aka.ms/qnamaker-pricing).
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o utilizzarne uno esistente in cui distribuire questa risorsa QnA Maker. QnA Maker crea diverse risorse di Azure. Quando si crea un gruppo di risorse che contiene queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Selezionare una **località del gruppo di risorse**.
    * Scegliere il piano **tariffario di ricerca** del servizio ricerca cognitiva di Azure. Se l'opzione livello gratuito non è disponibile (visualizzata in grigio), significa che è già stato distribuito un servizio gratuito tramite la sottoscrizione. In tal caso, è necessario iniziare con il livello Basic. Vedi [i dettagli sui prezzi di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/).
    * Scegliere il **percorso di ricerca** in cui si desidera distribuire gli indici ricerca cognitiva di Azure. Le restrizioni relative al percorso di archiviazione dei dati dei clienti contribuiranno a determinare il percorso scelto per ricerca cognitiva di Azure.
    * Nel campo **nome app** immettere un nome per l'istanza del servizio app Azure.
    * Per impostazione predefinita, il servizio app viene impostato sul livello standard (S1). È possibile modificare il piano dopo la creazione. Altre informazioni sui [prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).
    * Scegliere il **percorso del sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > Il **percorso di ricerca** può essere diverso dal **percorso del sito Web**.

    * Specificare se si desidera abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso di App Insights** in cui verrà distribuita la risorsa Application Insights.
    * Per le misure di risparmio sui costi, è possibile [condividere](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) alcune ma non tutte le risorse di Azure create per QnA Maker.

1. Dopo la convalida di tutti i campi, selezionare **Crea**. Il completamento del processo potrebbe richiedere alcuni minuti.

1. Al termine della distribuzione, verranno visualizzate le risorse seguenti create nella sottoscrizione:

   ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    La risorsa con il tipo di _Servizi cognitivi_ ha le chiavi di _sottoscrizione_ .

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

Questa procedura consente di creare le risorse di Azure necessarie per gestire il contenuto della Knowledge base. Dopo aver completato questi passaggi, le chiavi della *sottoscrizione* sono disponibili nella pagina **chiavi** della risorsa nel portale di Azure.

1. Accedere al portale di Azure e [creare una risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selezionare **Crea** dopo aver letto i termini e le condizioni:

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker** selezionare la casella di controllo gestito (anteprima) e selezionare i livelli e le aree appropriati:

    ![Creare un nuovo servizio gestito QnA Maker-piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o utilizzarne uno esistente in cui distribuire questa QnA Maker risorsa gestita (anteprima). QnA Maker Managed (Preview) crea alcune risorse di Azure. Quando si crea un gruppo di risorse che contiene queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Nel campo **nome** immettere un nome univoco per identificare questo servizio QnA Maker gestito (anteprima). 
    * Scegliere il **percorso** in cui si desidera distribuire il servizio QnA Maker Managed (Preview). Le API di gestione e l'endpoint servizio saranno ospitati in questa posizione. 
    * Selezionare il piano **tariffario** per il servizio QnA Maker gestito (anteprima) (gratuito per l'anteprima). Vedere [altri dettagli sui prezzi degli SKU](https://aka.ms/qnamaker-pricing).
    * Scegliere il **percorso di ricerca** in cui si desidera distribuire gli indici ricerca cognitiva di Azure. Le restrizioni relative al percorso di archiviazione dei dati dei clienti contribuiranno a determinare il percorso scelto per ricerca cognitiva di Azure.
    * Scegliere il piano **tariffario di ricerca** del servizio ricerca cognitiva di Azure. Se l'opzione livello gratuito non è disponibile (visualizzata in grigio), significa che è già stato distribuito un servizio gratuito tramite la sottoscrizione. In tal caso, è necessario iniziare con il livello Basic. Vedi [i dettagli sui prezzi di Azure ricerca cognitiva](https://azure.microsoft.com/pricing/details/search/).

1. Dopo la convalida di tutti i campi, selezionare **Verifica + crea**. Il completamento del processo potrebbe richiedere alcuni minuti.

1. Al termine della distribuzione, verranno visualizzate le risorse seguenti create nella sottoscrizione:

    ![Risorsa ha creato un nuovo servizio QnA Maker gestito (anteprima)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    La risorsa con il tipo di _Servizi cognitivi_ ha le chiavi di _sottoscrizione_ .

---

## <a name="upgrade-azure-resources"></a>Aggiornare le risorse di Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>Aggiornare QnA Maker SKU

Per ulteriori domande e risposte nella Knowledge base, oltre al livello corrente, aggiornare il piano tariffario del servizio QnA Maker.

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>Aggiornare il servizio app

Quando la Knowledge base deve servire più richieste dall'app client, aggiornare il piano tariffario del servizio app.

È possibile [aumentare](../../../app-service/manage-scale-up.md) o ridurre il servizio app.

Passare alla risorsa del servizio app nel portale di Azure e selezionare l'opzione di **scalabilità verticale** o **orizzontale** secondo le esigenze.

![Scalabilità del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Aggiornare il servizio Azure ricerca cognitiva

Se si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio Ricerca cognitiva di Azure.

Attualmente, non è possibile eseguire un aggiornamento sul posto dello SKU di ricerca di Azure. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker. A questo scopo, seguire questa procedura:

1. Creare una nuova risorsa di ricerca di Azure nella portale di Azure e selezionare lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il [codice di esempio di ripristino del backup](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Dopo il ripristino dei dati, passare alla nuova risorsa ricerca di Azure, selezionare **chiavi** e annotare il **nome** e la **chiave amministratore**:

    ![Chiavi di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Per collegare la nuova risorsa di ricerca di Azure allo stack di QnA Maker, passare all'istanza del servizio app QnA Maker.

    ![QnA Maker istanza del servizio app](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selezionare **Impostazioni applicazione** e modificare le impostazioni nei campi **AzureSearchName** e **AzureSearchAdminKey** del passaggio 3.

    ![Impostazione del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Riavviare l'istanza del servizio app.

    ![Riavvio dell'istanza del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Criteri di inattività per le risorse di ricerca gratuita

Se non si usa una risorsa QnA Maker, è necessario rimuovere tutte le risorse. Se non si rimuovono le risorse inutilizzate, la Knowledge base smetterà di funzionare se è stata creata una risorsa di ricerca gratuita.

Le risorse di ricerca gratuite vengono eliminate dopo 90 giorni senza ricevere una chiamata API.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Aggiornare il servizio Azure ricerca cognitiva

Se si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio Ricerca cognitiva di Azure.

Attualmente, non è possibile eseguire un aggiornamento sul posto dello SKU di ricerca di Azure. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker. A questo scopo, seguire questa procedura:

1. Creare una nuova risorsa di ricerca di Azure nella portale di Azure e selezionare lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il [codice di esempio di ripristino del backup](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Per collegare la nuova risorsa di ricerca di Azure al servizio QnA Maker gestito (anteprima), vedere l'argomento seguente.

### <a name="inactivity-policy-for-free-search-resources"></a>Criteri di inattività per le risorse di ricerca gratuita

Se non si usa una risorsa QnA Maker, è necessario rimuovere tutte le risorse. Se non si rimuovono le risorse inutilizzate, la Knowledge base smetterà di funzionare se è stata creata una risorsa di ricerca gratuita.

Le risorse di ricerca gratuite vengono eliminate dopo 90 giorni senza ricevere una chiamata API.

---

## <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

Se si elimina una delle risorse di Azure usate per le knowledge base di QnA Maker, le knowledge base non funzioneranno più. Prima di eliminare qualsiasi risorsa, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul servizio [app](../../../app-service/index.yml) e il [servizio di ricerca](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Informazioni su come creare con altri utenti](../index.yml)
