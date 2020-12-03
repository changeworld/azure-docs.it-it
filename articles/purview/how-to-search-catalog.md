---
title: "Procedura: eseguire una ricerca nell'Data Catalog"
description: Questo articolo fornisce una panoramica di come eseguire la ricerca in un catalogo dati.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554031"
---
# <a name="search-the-azure-purview-data-catalog"></a>Cerca nel Data Catalog di competenza di Azure

Questo articolo descrive come usare le varie funzionalità di ricerca nel Data Catalog di competenza di Azure.

## <a name="search-the-catalog-for-assets"></a>Cerca asset nel catalogo

I passaggi per eseguire una ricerca di asset sono:

1. [Aprire la finestra di dialogo ricerca asset](#open-the-asset-search-dialog) selezionando **Cerca catalogo**.
1. [Immettere i termini di ricerca](#enter-search-terms) per trovare gli asset con caratteristiche corrispondenti ai termini.
1. [Impostare i filtri rapidi](#set-quick-filters) per limitare la ricerca.
1. [Avviare la ricerca](#start-the-search) e passare ai risultati della ricerca.

Non importa se si impostano i filtri rapidi prima o dopo aver immesso i termini di ricerca.

Se non sono presenti termini di ricerca e nessun filtro, i risultati della ricerca includono tutti gli asset.

### <a name="open-the-asset-search-dialog"></a>Aprire la finestra di dialogo ricerca asset

Aprire la finestra di dialogo ricerca asset selezionando **Cerca catalogo**.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Sotto ' Cerca catalogo ' c'è un riquadro sinistro con i filtri di ricerca e un riquadro destro con le ricerche recenti." border="true":::

Nella finestra di dialogo di ricerca vengono visualizzati i filtri rapidi, la cronologia di ricerca e un elenco degli asset utilizzati di recente.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="L'elenco di ricerca si trova nel riquadro destro, sotto &quot;Cerca catalogo&quot;." border="true":::

### <a name="enter-search-terms"></a>Immettere i termini di ricerca

Immettere uno o più termini di ricerca nel **Catalogo di ricerca**. Durante la digitazione, i termini di ricerca corrispondenti dalle ricerche recenti sono elencati nelle **ricerche recenti**, i termini di ricerca corrispondenti suggeriti sono elencati nei suggerimenti per la **ricerca** e gli asset di dati corrispondenti vengono elencati in **suggerimenti asset**.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Screenshot che mostra i risultati di una ricerca immessa nella casella di ricerca del catalogo":::

I risultati della ricerca includono solo gli asset con una o più caratteristiche che corrispondono ai termini di ricerca. Queste caratteristiche includono il nome dell'asset, il tipo di asset, le classificazioni e i contatti.

#### <a name="types-of-search-criteria"></a>Tipi di criteri di ricerca

Azure competenza supporta i tipi di criteri di ricerca seguenti.

> [!Note]
> Specificare sempre gli operatori booleani (**and**, **or** e **not**) in tutte le maiuscole. In caso contrario, case non è rilevante, né spazi aggiuntivi.

- **hive**: trova i documenti che contengono **hive**.
- **database hive**: trova i documenti che contengono esattamente il **database hive**.
- **hive o database**: trovare documenti che contengono **hive** o **database** o entrambi.
- hive **e** database, **hive && database**: trovare documenti che contengono sia **hive** che **database**.
- **hive e (database o warehouse)**: trovare documenti che contengono **hive** e **database** o **Warehouse** o entrambi.
- **hive not database**: trovare documenti che contengono **hive**, ma non **database**.
- **HIV**: trova i documenti che contengono una parola che inizia con **HIV**. Ad esempio, **HIV**, **hive**, **hivbar** (* è un carattere jolly che corrisponde a un numero qualsiasi di caratteri).

### <a name="set-quick-filters"></a>Imposta filtri rapidi

L'elenco dei risultati della ricerca è basato sui termini di ricerca immessi nel **Catalogo di ricerca** e sui valori selezionati per i filtri rapidi.

Un filtro rapido limita l'elenco dei risultati della ricerca agli asset che hanno un valore selezionato di una caratteristica. Il filtro include un elenco a discesa e una casella di testo. L'elenco a discesa Mostra i valori della caratteristica che si trovano nei risultati della ricerca *corrente* . Accanto a ogni valore nell'elenco è presente un conteggio del numero di asset nei risultati della ricerca corrente con tale valore. Se si seleziona un valore nell'elenco, i risultati della ricerca saranno limitati agli asset con tale valore. È possibile selezionare solo un valore.

I risultati della ricerca corrente usati per formare l'elenco a discesa sono determinati da:

- Termini di ricerca immessi nel **Catalogo di ricerca**. 
- Valori selezionati nei filtri rapidi.

Di seguito è riportato un esempio del filtro rapido "tipo di asset".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Esempio di filtro rapido per il tipo di asset." border="true":::

È possibile immettere il testo nella casella di testo per limitare i valori nell'elenco a discesa ai valori corrispondenti o parzialmente corrispondenti al testo. Per esempi relativi all'uso della casella di testo, vedere [Cerca filtro rapido: Filtra per tipo di risorsa](#search-quick-filter-filter-by-asset-type)e [Cerca filtro rapido: Filtra per classificazione](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Filtro rapido di ricerca: filtrare in base al tipo di asset

Per filtrare in base al tipo di asset, usare il filtro rapido **tipo di asset** . L'elenco a discesa Mostra i tipi di asset trovati nei risultati della ricerca corrente, come determinato dai termini di ricerca e dai filtri rapidi. Per ogni tipo, viene visualizzato il numero di asset di quel tipo.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="I filtri rapidi per il tipo di asset sono evidenziati. Mostra i tipi di asset e un conteggio per ogni." border="true":::

Selezionare un tipo di asset per limitare i risultati della ricerca agli asset di quel tipo. È possibile selezionare solo un tipo.

Per visualizzare solo i tipi di asset i cui nomi corrispondono a una stringa, immettere la stringa nella casella di testo. Ad esempio, per visualizzare solo i tipi di asset con **SQL** nel nome, immettere **SQL**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Il riquadro filtri rapidi contiene SQL per &quot;asset Type&quot;. L'elenco degli asset che contengono SQL mostra tre voci." border="true":::

Selezionare un tipo di asset per limitare i risultati della ricerca agli asset di quel tipo. È possibile selezionare un solo tipo.

#### <a name="search-quick-filter-filter-by-classification"></a>Filtro rapido di ricerca: Filtra per classificazione

Per filtrare in base alla classificazione degli asset, usare il filtro rapido **classificazione** . L'elenco a discesa Mostra le classificazioni assegnate a uno o più asset nei risultati della ricerca corrente, come determinato dai termini di ricerca e dai filtri rapidi. Per ogni classificazione, viene visualizzato il numero di asset assegnati alla classificazione.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="I filtri rapidi di classificazione sono evidenziati." border="true":::

Selezionare una classificazione per limitare i risultati della ricerca agli asset assegnati a tale classificazione. È possibile selezionare solo una classificazione.

Per visualizzare solo le classificazioni i cui nomi corrispondono a una stringa, immettere la stringa nella casella di testo. Ad esempio, per visualizzare solo le classificazioni con **numero** nei rispettivi nomi, immettere **Number**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="Nel riquadro filtri rapidi, classificazione è' Bank ' e le classificazioni elencate contengono tutti tale valore." border="true":::

Selezionare una classificazione per limitare i risultati della ricerca agli asset a cui è stata assegnata tale classificazione. Non è possibile selezionare più di una classificazione.

#### <a name="search-quick-filter-filter-by-contacts"></a>Filtro rapido di ricerca: Filtra per contatti

Un *contatto* è una persona assegnata a un asset come proprietario o esperto. Quando si visualizzano i dettagli degli asset, i contatti vengono visualizzati nella scheda **contatti** .

Esistono due modi per cercare gli asset a cui è assegnato un determinato contatto.

- Immettere tutto o parte del nome del contatto nel **Catalogo di ricerca** ed eseguire una ricerca. I risultati della ricerca includeranno asset con contatti i cui nomi corrispondono ai termini di ricerca.
- Selezionare il contatto di interesse nel filtro rapido **contatto** ed eseguire una ricerca.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="Il valore di PERSON nel riquadro filtri rapidi è &quot;Darren&quot;. Nel riquadro suggerimenti sono disponibili tre suggerimenti." border="true":::

## <a name="search-example"></a>Esempio di ricerca

Si prenda in considerazione un esempio ipotetico per vedere come i termini di ricerca e i filtri rapidi interagiscono per determinare i risultati della ricerca. In particolare, verrà monitorato il numero di asset di **archiviazione BLOB di Azure**.

- Viene eseguita la prima ricerca senza termini di ricerca immessi e nessun valore selezionato nei filtri rapidi. La ricerca trova tutti gli asset nel catalogo. L'elenco dei risultati della ricerca e il filtro rapido per il **tipo di asset** rivelano:

    - L'elenco dei risultati della ricerca include 164.230 asset, ovvero tutti gli asset nel catalogo.
    - L'elenco a discesa **tipo di asset** contiene 43 voci. Si tratta di tutti i tipi di asset nel catalogo. Poiché ogni asset è di un solo tipo, la somma dei conteggi dei tipi di asset 43 è 164.230.
    - Il tipo di asset di **archiviazione BLOB di Azure** è la prima voce nell'elenco a discesa del filtro rapido **tipo di asset** . I valori vengono ordinati in base al conteggio, il più grande per primo, quindi l' **Archivio BLOB di Azure** è il tipo di asset più comune. Il conteggio è 118.174.

- A questo punto è possibile immettere **parquet** nel **Catalogo di ricerca** ed eseguire un'altra ricerca. I risultati della ricerca includono solo gli asset con caratteristiche che corrispondono a **parquet**. Questo consente di ridurre tutti i conteggi, come indicato di seguito:

    - L'elenco dei risultati della ricerca include 493 asset. Solo 493 degli asset 164.230 nel catalogo hanno caratteristiche che corrispondono a "parquet".
    - L'elenco a discesa **tipo di asset** include 15 voci. Ogni asset 493 è di uno di questi 15 tipi e la somma dei conteggi dei 15 tipi è 493.
    - Sono disponibili 456 risorse di tipo **archiviazione BLOB di Azure**. Gli altri asset 37 (493 meno 456) sono di uno degli altri 14 tipi.

- Viene ora esaminato l'elenco a discesa di un filtro rapido diverso, **classificazione**:

    - Sono disponibili 12 classificazioni per gli asset 493 nell'elenco dei risultati della ricerca. I conteggi per gli asset 493 non sono sommati a 493, perché è possibile assegnare un numero qualsiasi di classificazioni a un asset.
    - La classificazione dei **nomi della persona** viene assegnata a 36 asset, più di qualsiasi altra classificazione.

- Selezionare la classificazione **nome persona** . L'elenco dei risultati della ricerca scende a 36 asset, come previsto perché il numero per il **nome della persona** è 36. Nessuno di questi risultati è di tipo **archiviazione BLOB di Azure**.

Possiamo concludere che non esiste alcun asset il cui tipo è l' **Archivio BLOB di Azure** che corrisponde a **parquet** e che ha una classificazione del **nome della persona**.

## <a name="start-the-search"></a>Avvia la ricerca

Quando si esegue una ricerca, i termini di ricerca immessi nel **Catalogo di ricerca** vengono confrontati con le caratteristiche dell'asset. Queste caratteristiche includono nome, tipo, classificazione e contatti. Gli asset con caratteristiche corrispondenti vengono visualizzati nell'elenco dei risultati della ricerca, a meno che non vengano esclusi da un filtro rapido.

Dopo aver immesso i termini di ricerca e aver impostato i filtri rapidi, avviare la ricerca in uno dei modi seguenti:

- Per eseguire la ricerca in base ai termini immessi, selezionare l'icona di ricerca ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), premere **invio** o selezionare **Visualizza risultati ricerca**.
- Per eseguire la ricerca usando i termini di una ricerca precedente, selezionarli dalle **ricerche recenti**.
- Per eseguire la ricerca usando i termini suggeriti, selezionarli dai suggerimenti per la **ricerca**.

Selezionare un asset da **suggerimenti asset** per passare direttamente alla pagina dei dettagli per l'asset. Nessuna ricerca eseguita.

L'elenco dei risultati per i suggerimenti e le ricerche utente può essere leggermente diverso. I risultati dell'elenco di suggerimenti sono basati sulle corrispondenze fuzzy, mentre i risultati della ricerca avviati dall'utente sono basati sulle corrispondenze esatte.

Quando si esegue una ricerca, viene visualizzata la pagina **Risultati ricerca** in cui sono elencati gli asset trovati dalla ricerca.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot che mostra i risultati della ricerca per un valore di ricerca di contoso.":::

Per visualizzare i dettagli degli asset, selezionare un nome di asset.

Usare i controlli nella parte inferiore di una pagina dei risultati della ricerca per passare ad altre pagine dei risultati della ricerca.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Screenshot che illustra come spostarsi tra le pagine dei risultati della ricerca.":::

### <a name="sort-search-results"></a>Ordinare i risultati della ricerca

Utilizzare **Ordina** per per ordinare i risultati della ricerca in base alla **pertinenza** o al **nome**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Screenshot che illustra come ordinare i risultati della ricerca. Per questo esempio, l'elenco a discesa Ordina per è impostato su pertinenza.":::

### <a name="search-results-dynamic-filters"></a>Filtri dinamici dei risultati della ricerca

Il riquadro **filtro** nella pagina dei **Risultati della ricerca** include filtri che consentono di filtrare dinamicamente le risorse nell'elenco dei risultati della ricerca. Il filtro è dinamico in quanto i filtri aggiuntivi possono essere visualizzati in base alle selezioni del filtro.

I filtri dinamici includono una casella di controllo per ogni valore nell'elenco a discesa. Usare queste caselle di controllo per filtrare tutti i valori desiderati.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Filtro dinamico Risultati ricerca: Filtra per tipo di risorsa

Se si seleziona un tipo di asset nell'elenco a discesa **tipo di asset** , vengono visualizzati i filtri dinamici che offrono altri modi per limitare i risultati della ricerca. I filtri variano a seconda del tipo di risorsa selezionato. Ad esempio, se si seleziona il **database SQL di Azure**, vengono visualizzati i filtri dinamici per server, database e schema. I valori in questi filtri sono relativi agli asset nei risultati della ricerca del tipo selezionato.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="L'elemento filtro del database SQL di Azure è l'unico elemento &quot;tipo di asset&quot; selezionato. Viene evidenziato anche un risultato della ricerca del tipo di asset." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Filtro dinamico Risultati ricerca: Filtra per classificazione

Ogni classificazione nell'elenco **classificazione** viene applicata ad almeno un elemento nell'elenco dei risultati della ricerca. Selezionare una o più classificazioni per restringere i risultati della ricerca agli asset delle classificazioni selezionate.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Il filtro di classificazione di ' Risultati ricerca ' è evidenziato." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Modificare ed eliminare i filtri dei risultati di ricerca

Per rimuovere un filtro, deselezionare la casella di controllo accanto al nome del filtro.

### <a name="recently-accessed-assets"></a>Asset utilizzati di recente

La sezione **accesso di recente** della casella di ricerca espansa Mostra gli asset ad accesso più recente, se presenti.

- Selezionare **Visualizza tutto** nella sezione **accesso di recente** per visualizzare l'elenco completo degli asset a cui si accede di recente.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Screenshot che mostra la sezione di accesso di recente della casella di ricerca espansa.":::

   Viene visualizzato un elenco degli asset a cui si accede di recente.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Screenshot che mostra un elenco di asset a cui si accede di recente.":::

- Per filtrare in base al nome, immettere una stringa di ricerca in **Filtra in base al nome**.

- Per rimuovere gli elementi dall'elenco, selezionarli con le caselle di controllo e quindi selezionare **Rimuovi**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Screenshot che illustra come rimuovere elementi da un elenco di asset a cui si accede di recente.":::

- Per cancellare l'intero elenco, selezionare **Cancella**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Screenshot che illustra come cancellare un elenco di asset a cui si accede di recente":::

### <a name="search-assets"></a>Cerca asset

Molte pagine diverse da **Home** hanno una casella di **ricerca asset** nella parte superiore. Ad esempio, di seguito è illustrata una pagina di dettagli delle risorse con gli **asset di ricerca** evidenziati.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Screenshot che mostra una pagina Dettagli asset con gli asset di ricerca evidenziati":::

Selezionare **Cerca risorse** per avviare una casella di ricerca come quella che si ottiene dal **Catalogo di ricerca** in **casa**, con le stesse funzionalità.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Screenshot che mostra una casella risorse di ricerca espanse.":::

## <a name="next-steps"></a>Passaggi successivi

- [Come creare, importare ed esportare i termini di glossario](how-to-create-import-export-glossary.md)
- [Come gestire i modelli di termini per il glossario aziendale](how-to-manage-term-templates.md)
