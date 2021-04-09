---
title: Creare una regola di classificazione e classificazione personalizzata (anteprima)
description: Informazioni su come creare classificazioni personalizzate per definire i tipi di dati nell'area dati che sono univoci per l'organizzazione in Azure.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 2966618619aa40ed60c2f3d0fb2c8e080d34a016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617047"
---
# <a name="custom-classifications-in-azure-purview"></a>Classificazioni personalizzate in Azure competenza

In questo articolo viene descritto come creare classificazioni personalizzate per definire i tipi di dati nel proprio patrimonio di dati che sono univoci per l'organizzazione. Viene inoltre descritta la creazione di regole di classificazione personalizzate che consentono di trovare i dati specificati in tutti i dati.

## <a name="default-classifications"></a>Classificazioni predefinite

Il Data Catalog di competenza di Azure fornisce un ampio set di classificazioni predefinite che rappresentano i tipi di dati personali tipici che potrebbero essere presenti nel proprio data.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="Seleziona classificazione" border="true":::

È anche possibile creare classificazioni personalizzate, se una delle classificazioni predefinite non soddisfa le proprie esigenze.

## <a name="steps-to-create-a-custom-classification"></a>Passaggi per la creazione di una classificazione personalizzata

Per creare una classificazione personalizzata, procedere come segue:

1. Dal catalogo selezionare **Management Center** dal menu a sinistra.

2. Selezionare **classificazioni** in **Gestione metadati**.

3. Seleziona **+ nuovo**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nuova classificazione" border="true":::

Viene visualizzato il riquadro **Aggiungi nuova classificazione** , in cui è possibile assegnare un nome alla classificazione e una descrizione. È consigliabile usare una convenzione di spazi dei nomi, ad esempio `your company name.classification name` .

Le classificazioni di sistema Microsoft sono raggruppate nello `MICROSOFT.` spazio dei nomi riservato. Un esempio è **Microsoft. Governo. Noi. \_ \_ numero di previdenza sociale**.

Il nome della classificazione deve iniziare con una lettera seguita da una sequenza di lettere, numeri e punto (.) o caratteri di sottolineatura. Non sono consentiti spazi. Durante la digitazione, l'esperienza utente genera automaticamente un nome descrittivo. Questo nome descrittivo è ciò che gli utenti vedono quando lo si applica a un asset nel catalogo.

Per evitare il nome breve, il sistema crea il nome descrittivo in base alla logica seguente:

- Vengono eliminati tutti gli ultimi due segmenti dello spazio dei nomi.

- Le maiuscole e minuscole vengono modificate in modo che la prima lettera di ogni parola sia maiuscola. Tutte le altre lettere vengono convertite in lettere minuscole.

- Tutti i caratteri di sottolineatura ( \_ ) vengono sostituiti con spazi.

Ad esempio, se la classificazione è stata denominata **contoso.hr. \_ID dipendente**, il nome descrittivo viene archiviato nel sistema come **ID HR. Employee**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Selezionare **OK**. la nuova classificazione verrà aggiunta all'elenco classificazione.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Classificazione personalizzata" border="true":::

Selezionando la classificazione nell'elenco verrà visualizzata la pagina Dettagli classificazione. Qui è possibile trovare tutti i dettagli della classificazione.

Questi dettagli includono il conteggio del numero di istanze, il nome formale, le regole di classificazione associate (se presenti) e il nome del proprietario.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Seleziona classificazione" border="true":::

## <a name="custom-classification-rules"></a>Regole di classificazione personalizzate

Il servizio Catalogo fornisce un set di regole di classificazione predefinite, che vengono utilizzate dallo scanner per rilevare automaticamente determinati tipi di dati. È anche possibile aggiungere regole di classificazione personalizzate per rilevare altri tipi di dati che potrebbero essere interessati a trovare tra i dati. Questa funzionalità può essere molto potente quando si \' tenta di trovare i dati all'interno del proprio data.

Ad esempio, supponiamo \' che una società denominata Contoso disponga di ID dipendente standardizzati nell'intera azienda con il termine \" Employee \" seguito da un GUID per la creazione del dipendente {GUID}. Ad esempio, un'istanza di un ID dipendente ha un aspetto simile a `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

Contoso può configurare il sistema di analisi per individuare le istanze di questi ID creando una regola di classificazione personalizzata. Possono fornire un'espressione regolare che corrisponda al modello di dati, in questo caso `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Facoltativamente, se i dati si trova in genere in una colonna di cui si conosce il nome, ad esempio Employee \_ ID o EmployeeID, è possibile aggiungere un'espressione regolare del criterio di colonna per rendere l'analisi ancora più accurata. Un esempio di Regex è Employee \_ ID \| EmployeeID.

Il sistema di analisi può quindi utilizzare questa regola per esaminare i dati effettivi nella colonna e il nome della colonna per tentare di identificare ogni istanza di in cui viene trovato il modello di ID dipendente.

## <a name="steps-to-create-a-custom-classification-rule"></a>Procedura per creare una regola di classificazione personalizzata

Per creare una regola di classificazione personalizzata:

1. Creare una classificazione personalizzata seguendo le istruzioni riportate nella sezione precedente. Questa classificazione personalizzata verrà aggiunta nella configurazione della regola di classificazione in modo che venga applicata dal sistema quando viene trovata una corrispondenza nella colonna.

2. Selezionare l'icona del **centro di gestione** .

3. Selezionare la sezione **regole Classificazioni** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Riquadro regole di classificazione" border="true":::

4. Selezionare **Nuovo**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Aggiungi nuova regola di classificazione" border="true":::

5. Verrà visualizzata la finestra di dialogo **nuova regola di classificazione** . Compilare i campi e decidere se creare una regola di **espressione regolare** o una **regola del dizionario**.

   |Campo     |Descrizione  |
   |---------|---------|
   |Nome   |    Obbligatorio. Il valore massimo è 100 caratteri.    |
   |Descrizione      |Facoltativa. Il valore massimo è 256 caratteri.    |
   |Nome classificazione    | Obbligatorio. Selezionare il nome della classificazione dall'elenco a discesa per indicare allo scanner di applicarlo se viene trovata una corrispondenza.        |
   |State   |  Obbligatorio. Le opzioni sono abilitate o disabilitate. Enabled è il valore predefinito.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Crea nuova regola di classificazione" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Creazione di una regola di espressione regolare

1. Se si crea una regola di espressione regolare, viene visualizzata la schermata seguente. Facoltativamente, è possibile caricare un file che verrà usato per **generare i modelli Regex suggeriti** per la regola.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Crea nuova regola regex" border="true":::

1. Se si decide di generare un modello Regex suggerito, dopo il caricamento di un file selezionare uno dei modelli suggeriti e fare clic su **Aggiungi a modelli** per utilizzare i modelli di dati e di colonna suggeriti. È possibile modificare i modelli suggeriti o anche digitare i propri modelli senza caricare un file.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Genera Regex suggerito" border="true":::

   |Campo     |Descrizione  |
   |---------|---------|
   |Modello di dati    |facoltativo. Espressione regolare che rappresenta i dati archiviati nel campo dati. Il limite è molto grande. Nell'esempio precedente, i modelli di dati verificano l'ID di un dipendente che è letteralmente la parola `Employee{GUID}` .  |
   |Modello di colonna    |facoltativo. Espressione regolare che rappresenta i nomi di colonna per i quali si desidera trovare una corrispondenza. Il limite è molto grande. |

1. Nel **modello di dati** sono disponibili due soglie che è possibile impostare:

   - **Soglia di corrispondenza Distinct**: numero totale di valori di dati distinti che devono essere trovati in una colonna prima che lo scanner esegua il modello di dati. Il valore suggerito è 8. Questo valore può essere regolato manualmente in un intervallo compreso tra 2 e 32. Il sistema richiede questo valore per assicurarsi che la colonna contenga dati sufficienti affinché lo scanner lo classifichi in modo accurato. Una colonna contenente più righe che contengono tutti il valore 1, ad esempio, non sarà classificata. Anche le colonne che contengono una riga con un valore e il resto delle righe hanno valori null non vengono classificate. Se si specificano più modelli, questo valore viene applicato a ognuno di essi.

   - **Soglia di corrispondenza minima**: è possibile usare questa impostazione per impostare la percentuale minima delle corrispondenze del valore dei dati distinti in una colonna che deve essere trovata dallo scanner per la classificazione da applicare. Il valore suggerito è 60%. È necessario prestare attenzione a questa impostazione. Se si riduce il livello inferiore al 60%, è possibile introdurre classificazioni false positive nel catalogo. Se si specificano più modelli di dati, questa impostazione è disabilitata e il valore è fisso al 60%.

1. È ora possibile verificare la regola e **crearla** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Verificare la regola prima della creazione" border="true":::

### <a name="creating-a-dictionary-rule"></a>Creazione di una regola del dizionario

1. Se si crea una regola del dizionario, viene visualizzata la schermata seguente. Caricare un file contenente tutti i valori possibili per la classificazione che si sta creando in un'unica colonna.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Crea regola del dizionario" border="true":::

1. Dopo la generazione del dizionario, è possibile modificare le soglie di corrispondenza e di corrispondenza minime e inviare la regola.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Regola del dizionario di competenza-regolazione della soglia di corrispondenza DISTINCT e della soglia di corrispondenza minima" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Crea una regola del dizionario con segno di spunta generato dal dizionario." border="true":::

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata creata la regola di classificazione, è possibile aggiungerla a un set di regole di analisi in modo che l'analisi usi la regola durante l'analisi. Per altre informazioni, vedere [creare un set di regole di analisi](create-a-scan-rule-set.md).
