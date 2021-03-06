---
title: Data Factory copia guidata di Azure
description: Informazioni su come usare Copia guidata di Azure Data Factory per copiare i dati da origini dati supportate nei sink.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393378"
---
# <a name="azure-data-factory-copy-wizard"></a>Copia guidata di Azure Data Factory

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. 

La copia guidata di Azure Data Factory semplifica l'inserimento di dati, che rappresenta in genere il primo passaggio in uno scenario di integrazione dati end-to-end. Quando si esegue la copia guidata di Azure Data Factory, non è necessario comprendere le definizioni JSON per i set di dati, le pipeline e i servizi collegati. La procedura guidata crea automaticamente una pipeline per copiare i dati dall'origine dati selezionata alla destinazione scelta. In più, la copia guidata consente di convalidare i dati inseriti al momento della creazione, risparmiando tempo soprattutto quando si inseriscono i dati per la prima volta dall'origine dati. Per avviare Copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia guidata](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Pensata per i Big Data
Questa procedura guidata consente di spostare facilmente i dati da un'ampia gamma di origini alle destinazioni in pochi minuti. Al termine della procedura guidata viene creata automaticamente una pipeline con un'attività di copia insieme a entità di Data Factory dipendenti (set di dati e servizi collegati). Per creare la pipeline non occorre eseguire altri passaggi.   

![Selezionare l'origine dati](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Leggere l'articolo sull'[esercitazione Copia guidata](data-factory-copy-data-wizard-tutorial.md) per istruzioni dettagliate sulla creazione di una pipeline di esempio per copiare i dati da un BLOB Azure a una tabella del database SQL di Azure.

La procedura guidata è stata concepita fin da subito per l'uso con i Big Data, offrendo il supporto a tipi differenti di oggetti e dati. È possibile creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle. La procedura guidata supporta l'anteprima automatica dei dati, il mapping e l'acquisizione dello schema, oltre al filtro dei dati.

## <a name="automatic-data-preview"></a>Anteprima automatica dei dati
È possibile visualizzare l'anteprima di una parte dei dati dall'origine dati selezionata per confermare che si tratti dei dati che si vuole copiare. in più, se l'origine dati è in un file di testo, la copia guidata analizza il file di testo per apprendere automaticamente lo schema e i delimitatori di riga e colonna.

![Impostazioni sul formato del file](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Acquisizione dello schema e mapping
In alcuni casi è possibile che lo schema di dati di input non corrisponda allo schema dei dati di output. Se succede, è necessario eseguire il mapping delle colonne che appartengono allo schema di origine con le colonne dello schema di destinazione.

> [!TIP]
> Quando si copiano dati da SQL Server o da un database SQL di Azure in Azure sinapsi Analytics, se la tabella non esiste nell'archivio di destinazione, Data Factory supporta la creazione automatica della tabella usando lo schema dell'origine. Per altre informazioni [, vedere spostare dati da e verso Azure sinapsi Analytics usando Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Usare un elenco a discesa per selezionare una colonna dallo schema di origine per eseguire il mapping a una colonna nello schema di destinazione. La copia guidata tenta di comprendere il modello dell'utente per il mapping di colonna, applicandone uno uguale alle colonne rimanenti, senza la necessità di selezionarle singolarmente per completare il mapping dello schema. Se si preferisce, è possibile eseguire l'override di questi mapping usando gli elenchi a discesa per eseguire il mapping singolo di ciascuna colonna. Il modello diventa più preciso quando si esegue il mapping di più colonne. La copia guidata aggiorna costantemente il modello e, alla fine, delinea il modello corretto per il mapping della colonna che si vuole ottenere.     

![Mapping dello schema](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtro dei dati
È possibile filtrare i dati di origine per selezionare soltanto quelli da copiare nell'archivio dati sink. L'uso del filtro consente di ridurre il volume di dati da copiare nell'archivio dati sink, aumentando l'efficacia dell'operazione di copia. Offre un modo flessibile di filtrare i dati in un database relazionale tramiteil linguaggio query SQL o i file in una cartella BLOB di Azure usando [le variabili e le funzioni di Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtro dei dati in un database
La schermata seguente mostra una query SQL usando la funzione `Text.Format` e la variabile `WindowStart`.

![Espressione di convalida](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtro dei dati in una cartella BLOB di Azure
È possibile usare le variabili nel percorso della cartella per copiare dati da una cartella determinata in fase di esecuzione in base alle [variabili di sistema](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** e **{custom}**. Ad esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Fare clic sul pulsante **Esplora** per **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo dovrebbe essere visualizzato `2016/03/01/02`. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** con **{day}** e **02** con **{hour}**, quindi premere il tasto **Tab**. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il formato per queste quattro variabili:

![Uso di variabili di sistema](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Come mostra lo screenshot seguente, è anche possibile usare una variabile **personalizzata** e qualsiasi [stringa di formato supportato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Per selezionare una cartella con tale struttura, usare prima di tutto il pulsante **Esplora** . Sostituire un valore con **{custom}** e quindi premere il tasto **Tab** per visualizzare la casella di testo in cui è possibile digitare la stringa di formato.     

![Uso di variabili personalizzate](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Opzioni di pianificazione
È possibile eseguire l'operazione di copia una sola volta oppure in base a una pianificazione, con cadenza oraria, giornaliera e così via. Entrambe queste opzioni possono essere usate per tutti i vari connettori tra gli ambienti, incluse le copie in locale, nel cloud e nei computer desktop locali.

Un'operazione di copia eseguita una sola volta permette di spostare dati da un'origine a una destinazione una sola volta. Si applica ai dati di qualsiasi dimensione e in qualsiasi formato supportato. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Per configurare la copia pianificata sono disponibili impostazioni avanzate, come la ripetizione dei tentativi, il timeout, gli avvisi e così via.

![Proprietà di pianificazione](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

In questa sezione vengono illustrati i metodi di risoluzione dei problemi comuni per la copia guidata di Azure Data Factory.

> [!NOTE] 
> Questi suggerimenti per la risoluzione dei problemi si applicano alla copia guidata nella versione 1 del Data Factory. Per Data Factory V2, vedere la guida alla risoluzione dei problemi in [risoluzione dei problemi Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Codice di errore: non è possibile convalidare nella copia guidata

- **Sintomi**: nel primo passaggio della copia guidata viene visualizzato il messaggio di avviso "Impossibile convalidare".
- **Cause**: questa situazione può verificarsi quando tutti i cookie di terze parti sono disabilitati.
- **Risoluzione**: 
    - Usare Internet Explorer o il browser Microsoft Edge.
    - Se si usa il browser Chrome, seguire le istruzioni riportate di seguito per aggiungere l'eccezione dei cookie per *microsoftonline.com* e *Windows.NET*.
        1.  Aprire il browser Chrome.
        2.  Fare clic sulla chiave o su tre righe a destra (personalizzare e controllare Google Chrome).
        3.  Fare clic su **Impostazioni**.
        4.  Cerca i **cookie** o passa alla **privacy** in impostazioni avanzate.
        5.  Selezionare **impostazioni contenuto**.    
        6.  I cookie devono essere impostati in modo da **consentire l'impostazione dei dati locali (scelta consigliata)**.
        7.  Fare clic su **Gestisci eccezioni**. In **schema hostname** immettere quanto segue e verificare che **Consenti** sia il set di comportamenti.
            - login.microsoftonline.com
            - login.windows.net
        8.  Chiudere il browser e riavviarlo.
    - Se si usa il browser Firefox, seguire le istruzioni riportate di seguito per aggiungere l'eccezione dei cookie.
        1. Dal menu Firefox passare a **strumenti**  >  **Opzioni**.
        2. In   >  **cronologia** privacy, è possibile che l'impostazione corrente **usi impostazioni personalizzate per la cronologia**.
        3. In **accetta cookie di terze parti**, l'impostazione corrente potrebbe **non essere mai**, quindi è necessario fare clic su **eccezioni** a destra per aggiungere i siti seguenti.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Chiudere il browser e riavviarlo. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Codice di errore: Impossibile aprire la pagina di accesso e immettere la password

- **Sintomi**: la copia guidata reindirizza l'utente alla pagina di accesso, ma la pagina di accesso non viene visualizzata correttamente.
- **Cause**: questo problema può verificarsi se l'ambiente di rete è stato modificato dalla rete di Office alla rete domestica. Sono presenti alcune cache nei browser. 
- **Risoluzione**: 
    1.  Chiudere il browser e riprovare. Se il problema persiste, procedere al passaggio successivo.   
    2.  Se si utilizza il browser Internet Explorer, provare ad aprirlo in modalità privata (premere "Ctrl" + "Maiusc" + "P"). Se si usa il browser Chrome, provare ad aprirlo in modalità in incognito (premere "Ctrl" + "Maiusc" + "N"). Se il problema persiste, procedere al passaggio successivo. 
    3.  Provare a usare un altro browser. 


## <a name="next-steps"></a>Passaggi successivi
Per una procedura dettagliata sull'uso di Copia guidata di Data Factory per creare una pipeline con l'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).