---
title: "Procedura: eseguire una ricerca nell'Data Catalog"
description: Questo articolo fornisce una panoramica di come eseguire la ricerca in un catalogo dati.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588439"
---
# <a name="search-the-azure-purview-data-catalog"></a>Eseguire ricerche in Azure Purview Data Catalog

L'individuazione dati è il primo passaggio per un carico di lavoro di analisi dei dati o governance dei dati per i consumer di dati L'individuazione dei dati può richiedere molto tempo perché potrebbe non essere possibile sapere dove trovare i dati desiderati. Anche dopo aver trovato i dati, è possibile che si verifichino dubbi su come sia possibile considerare attendibili i dati e assumere una dipendenza.

L'obiettivo della ricerca in Azure è quello di velocizzare il processo di individuazione dei dati per individuare rapidamente i dati più importanti. Questo articolo illustra come eseguire una ricerca nel Catalogo dati di Azure per individuare rapidamente i dati che si stanno cercando.

## <a name="search-the-catalog-for-assets"></a>Cerca asset nel catalogo

In Azure, la barra di ricerca si trova nella parte superiore dell'esperienza utente di competenza Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Screenshot che mostra la posizione della barra di ricerca di Azure per le competenze" border="true":::

Quando si fa clic sulla barra di ricerca, è possibile visualizzare la cronologia di ricerca recente e gli asset a cui si accede di recente. Selezionare "Visualizza tutto" per visualizzare tutte le risorse visualizzate di recente.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Screenshot che mostra la barra di ricerca prima che siano state immesse parole chiave" border="true":::

Immettere le parole chiave che consentono di identificare l'asset, ad esempio il nome, il tipo di dati, le classificazioni e i termini di glossario. Quando si immettono le parole chiave relative all'asset desiderato, Azure ambito Visualizza suggerimenti sugli elementi da ricercare e sulle potenziali corrispondenze di asset. Per completare la ricerca, fare clic su "Visualizza risultati ricerca" oppure premere "invio".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Screenshot che mostra la barra di ricerca quando un utente immette le parole chiave" border="true":::

La pagina dei risultati della ricerca Mostra un elenco di asset che corrispondono alle parole chiave specificate in ordine di pertinenza. Esistono vari fattori che possono influire sul punteggio di pertinenza di un asset. È possibile filtrare ulteriormente l'elenco selezionando archivi dati, classificazioni, contatti, etichette e termini di glossario specifici che si applicano all'asset che si sta cercando.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot che mostra i risultati di una ricerca" border="true":::

 Fare clic sull'asset desiderato per visualizzare la pagina dei dettagli dell'asset in cui è possibile visualizzare le proprietà, inclusi lo schema, la derivazione e i proprietari di asset.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot che mostra la pagina dei dettagli dell'asset" border="true":::

## <a name="search-query-syntax"></a>Sintassi di query di ricerca

Tutte le query di ricerca sono costituite da parole chiave e operatori. Una parola chiave è un elemento che fa parte delle proprietà di un asset. Le parole chiave potenziali possono essere una classificazione, un termine di glossario, una descrizione dell'asset o un nome di asset. Una parola chiave può essere solo una parte della proprietà a cui si sta cercando di trovare la corrispondenza. Usare le parole chiave e gli operatori elencati di seguito per assicurarsi che Azure competenza restituisca gli asset che si sta cercando. 

Di seguito sono riportati gli operatori che possono essere usati per comporre una query di ricerca. Gli operatori possono essere combinati il numero di volte necessario in una singola query.

| Operatore | Definizione | Esempio |
| -------- | ---------- | ------- |
| OR | Specifica che un asset deve avere almeno una delle due parole chiave. Deve essere presente in tutte le maiuscole. Uno spazio vuoto è anche un operatore OR.  | La query `hive OR database` restituisce gli asset che contengono ' hive ' o ' database ' o entrambi. |
| AND | Specifica che un asset deve avere entrambe le parole chiave. Deve essere presente in tutte le maiuscole | La query `hive AND database` restituisce asset che contengono sia "hive" che "database". |
| NOT | Specifica che un asset non può contenere la parola chiave a destra della clausola NOT | La query `hive NOT database` restituisce gli asset che contengono ' hive ', ma non ' database '. |
| () | Raggruppa un set di parole chiave e operatori insieme. Quando si combinano più operatori, le parentesi specificano l'ordine delle operazioni. | La query `hive AND (database OR warehouse)` restituisce gli asset che contengono "hive" e "database" o "Warehouse" o entrambi. |
| "" | Specifica il contenuto esatto di una frase a cui la query deve corrispondere. | La query `"hive database"` restituisce gli asset che contengono la frase "hive database" nelle rispettive proprietà |
| * | Carattere jolly che corrisponde a uno-a-molti caratteri. Non può essere il primo carattere di una parola chiave. | La query `hiv\` * restituisce asset con proprietà che iniziano con "HIV", ad esempio "hive" o "hive-Table". |
| ? | Carattere jolly che corrisponde a un singolo carattere. Non può essere il primo carattere di una parola chiave | La query `hiv?` restituisce asset con proprietà che iniziano con ' HIV ' e sono costituite da quattro lettere, ad esempio ' hive ' o ' ". |

> [!Note]
> Specificare sempre gli operatori booleani (**and**, **or** e **not**) in tutte le maiuscole. In caso contrario, case non è rilevante, né spazi aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare, importare ed esportare i termini di glossario](how-to-create-import-export-glossary.md)
- [Come gestire i modelli di termini per il glossario aziendale](how-to-manage-term-templates.md)
