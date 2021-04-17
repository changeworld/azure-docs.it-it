---
title: 'Procedura: eseguire una ricerca nel Data Catalog'
description: Questo articolo offre una panoramica di come eseguire ricerche in un catalogo dati.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564914"
---
# <a name="search-the-azure-purview-data-catalog"></a>Eseguire ricerche in Azure Purview Data Catalog

L'individuazione dei dati è il primo passaggio per un carico di lavoro di analisi dei dati o governance dei dati per i consumer di dati. L'individuazione dei dati può richiedere molto tempo perché è possibile che non si sappia dove trovare i dati desiderati. Anche dopo aver trovato i dati, è possibile avere dubbi sul fatto che sia possibile considerare attendibili i dati e assumere una dipendenza da esso.

L'obiettivo della ricerca in Azure Purview è velocizzare il processo di individuazione dei dati per trovare rapidamente i dati importanti. Questo articolo descrive come eseguire ricerche nel catalogo dati di Azure Purview per trovare rapidamente i dati che si stanno cercando.

## <a name="search-the-catalog-for-assets"></a>Cercare asset nel catalogo

In Azure Purview la barra di ricerca si trova nella parte superiore dell'esperienza utente di Purview Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Screenshot che mostra la posizione della barra di ricerca di Azure Purview" border="true":::

Quando si fa clic sulla barra di ricerca, è possibile visualizzare la cronologia di ricerca recente e gli asset a cui si è avuto accesso di recente. Selezionare "Visualizza tutto" per visualizzare tutti gli asset visualizzati di recente.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Screenshot che mostra la barra di ricerca prima dell'immissione di parole chiave" border="true":::

Immettere le parole chiave che consentono di identificare l'asset, ad esempio il nome, il tipo di dati, le classificazioni e i termini di glossario. Quando si immettono parole chiave relative all'asset desiderato, Azure Purview visualizza suggerimenti sugli elementi da cercare e sulle potenziali corrispondenze degli asset. Per completare la ricerca, fare clic su "Visualizza risultati della ricerca" o premere "INVIO".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Screenshot che mostra la barra di ricerca quando un utente immette parole chiave" border="true":::

La pagina dei risultati della ricerca mostra un elenco di asset che corrispondono alle parole chiave fornite in ordine di pertinenza. Esistono diversi fattori che possono influire sul punteggio di pertinenza di un asset. È possibile filtrare più in basso l'elenco selezionando archivi dati, classificazioni, contatti, etichette e termini di glossario specifici applicabili all'asset che si sta cercando.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot che mostra i risultati di una ricerca" border="true":::

 Fare clic sull'asset desiderato per visualizzare la pagina dei dettagli dell'asset in cui è possibile visualizzare le proprietà, tra cui schema, derivazione e proprietari di asset.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Screenshot che mostra la pagina dei dettagli dell'asset" border="true":::

## <a name="search-query-syntax"></a>Sintassi di query di ricerca

Tutte le query di ricerca sono costituite da parole chiave e operatori. Una parola chiave è un elemento che fa parte delle proprietà di un asset. Le parole chiave potenziali possono essere una classificazione, un termine di glossario, una descrizione dell'asset o un nome di asset. Una parola chiave può essere solo una parte della proprietà con cui si sta cercando di trovare una corrispondenza. Usare le parole chiave e gli operatori elencati di seguito per assicurarsi che Azure Purview restituisca gli asset cercati. 

Di seguito sono riportati gli operatori che possono essere usati per comporre una query di ricerca. Gli operatori possono essere combinati il numero di volte necessario in una singola query.

| Operatore | Definizione | Esempio |
| -------- | ---------- | ------- |
| OR | Specifica che un asset deve avere almeno una delle due parole chiave. Deve essere in maiuscolo. Uno spazio vuoto è anche un operatore OR.  | La query `hive OR database` restituisce asset che contengono 'hive' o 'database' o entrambi. |
| AND | Specifica che un asset deve avere entrambe le parole chiave. Deve essere in maiuscolo | La query `hive AND database` restituisce asset che contengono sia 'hive' che 'database'. |
| NOT | Specifica che un asset non può contenere la parola chiave a destra della clausola NOT | La query `hive NOT database` restituisce asset che contengono 'hive', ma non 'database'. |
| () | Raggruppa un set di parole chiave e operatori. Quando si combinano più operatori, le parentesi specificano l'ordine delle operazioni. | La query `hive AND (database OR warehouse)` restituisce asset che contengono 'hive' e 'database' o 'warehouse' o entrambi. |
| "" | Specifica il contenuto esatto di una frase a cui deve corrispondere la query. | La query `"hive database"` restituisce asset che contengono la frase "hive database" nelle relative proprietà |
| * | Carattere jolly che corrisponde a uno o più caratteri. Non può essere il primo carattere in una parola chiave. | La query `dat*` restituisce asset con proprietà che iniziano con 'dat', ad esempio 'data' o 'database'. |
| ? | Carattere jolly che corrisponde a un singolo carattere. Non può essere il primo carattere in una parola chiave | La query restituisce asset con proprietà che iniziano con "dat" e sono quattro lettere, ad esempio `dat?` "date" o "data". |

> [!Note]
> Specificare sempre gli operatori booleani (**AND**, **OR**, **NOT**) in maiuscolo. In caso contrario, la distinzione tra maiuscole e minuscole non è importante né spazi aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare, importare ed esportare termini di glossario](how-to-create-import-export-glossary.md)
- [Come gestire i modelli di termini per il glossario aziendale](how-to-manage-term-templates.md)
