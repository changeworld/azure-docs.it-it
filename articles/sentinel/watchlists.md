---
title: Usare le watchlist di Azure Sentinel
description: Questo articolo descrive come usare Azure Sentinel watchlists per esaminare le minacce, importare dati aziendali, creare elenchi Consenti e arricchire i dati degli eventi.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99555649"
---
# <a name="use-azure-sentinel-watchlists"></a>Usare le watchlist di Azure Sentinel

> [!IMPORTANT]
> La funzionalità watchlists è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Azure Sentinel watchlists consente la raccolta di dati da origini dati esterne per la correlazione con gli eventi nell'ambiente Sentinel di Azure. Una volta creato, è possibile usare watchlists nei PlayBook di ricerca, regole di rilevamento, Hunting delle minacce e risposta. Watchlists vengono archiviati nell'area di lavoro di Azure Sentinel come coppie nome-valore e vengono memorizzati nella cache per ottimizzare le prestazioni delle query e bassa latenza.

Gli scenari comuni per l'uso di watchlists includono:

- **Analisi delle minacce** e risposta tempestiva agli eventi imprevisti con l'importazione rapida di indirizzi IP, hash di file e altri dati da file CSV. Una volta importato, è possibile usare le coppie nome/valore di Watch List per join e filtri in regole di avviso, caccia di minacce, cartelle di lavoro, notebook e query generali.

- **Importazione di dati aziendali** come un controllo. Importare, ad esempio, gli elenchi di utenti con privilegi di accesso al sistema o i dipendenti terminati, quindi utilizzare l'elenco di controllo per creare elenchi Consenti e nega utilizzati per rilevare o impedire a tali utenti di accedere alla rete.

- **Riduzione della fatica** per gli avvisi. Creare gli elenchi Consenti per eliminare gli avvisi da un gruppo di utenti, ad esempio gli utenti di indirizzi IP autorizzati che eseguono attività che normalmente generano l'avviso e impediscono che eventi benigni diventino avvisi.

- **Arricchimento dei dati degli eventi**. Usare watchlists per arricchire i dati degli eventi con combinazioni nome-valore derivate da origini dati esterne.

## <a name="create-a-new-watchlist"></a>Crea un nuovo controllo

1. Dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Watch** list e quindi selezionare **Aggiungi nuovo**.

    > [!div class="mx-imgBorder"]
    > ![nuovo controllo](./media/watchlists/sentinel-watchlist-new.png)

1. Nella pagina **generale** specificare il nome, la descrizione e l'alias per l'oggetto Watch List e quindi fare clic su **Next (avanti**).

    > [!div class="mx-imgBorder"]
    > ![pagina generale Watch List](./media/watchlists/sentinel-watchlist-general.png)

1. Nella pagina **origine** selezionare il tipo di set di dati, caricare un file e quindi fare clic su **Avanti**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > I caricamenti di file sono attualmente limitati a file con dimensioni massime di 3,8 MB.

1. Esaminare le informazioni, verificare che siano corrette e quindi selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![pagina Revisione lista di controllo](./media/watchlists/sentinel-watchlist-review.png)

    Una notifica viene visualizzata dopo la creazione dell'oggetto Watch List.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="notifica di creazione di controllo completata" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Usare watchlists nelle query

1. Dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Watch** list, selezionare il controllo che si vuole usare e quindi selezionare **View in log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="usare watchlists nelle query" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Gli elementi nell'oggetto Watch List vengono estratti automaticamente per la query e verranno visualizzati nella scheda **risultati** . Nell'esempio seguente vengono illustrati i risultati dell'estrazione dei campi **ServerName** e **IPAddress** .

    > [!NOTE]
    > Il timestamp sulle query verrà ignorato sia nell'interfaccia utente della query che negli avvisi pianificati.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="query con campi di controllo" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. È possibile eseguire query sui dati di una tabella in base ai dati di un oggetto watching gestendo l'oggetto Watch List come tabella per i join e le ricerche.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="query sull'oggetto Watch List come Lookup":::

## <a name="use-watchlists-in-analytics-rules"></a>Usare watchlists nelle regole di analisi

Per usare watchlists nelle regole di analisi, dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Analytics** e creare una regola usando la `_GetWatchlist('<watchlist>')` funzione nella query.

1. In questo esempio, creare un controllo denominato "ipwatchlist" con i valori seguenti:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="elenco di quattro elementi per l'elenco di controllo":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="creazione di un controllo con quattro elementi":::

1. Successivamente, creare la regola di analisi.  In questo esempio vengono inclusi solo gli eventi degli indirizzi IP nell'oggetto Watch List:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="usare watchlists nelle regole di analisi":::

## <a name="view-list-of-watchlists-aliases"></a>Visualizza l'elenco degli alias watchlists

Per ottenere un elenco di alias di controllo, dalla portale di Azure, passare a **Azure Sentinel**  >    >  **log** generale ed eseguire la query seguente: `_GetWatchlistAlias` . È possibile visualizzare l'elenco di alias nella scheda **risultati** .

> [!div class="mx-imgBorder"]
> ![elencare watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare watchlists in Sentinel di Azure per arricchire i dati e migliorare le indagini. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
