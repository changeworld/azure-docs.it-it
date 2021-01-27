---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896126"
---
## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

> [!Note] 
> La procedura e gli screenshot seguenti illustrano il processo generale di gestione delle analisi tra diversi tipi di origini dati. Le opzioni disponibili potrebbero variare leggermente in base ai tipi di origini dati in uso.

Per creare ed eseguire una nuova analisi, procedere come segue:

1. Passare a **Origini**

1. Selezionare l'origine dati registrata.

1. Selezionare **+ Nuova analisi**

1. Selezionare le credenziali per connettersi all'origine dati. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Configurare l'analisi":::

1. È possibile definire l'ambito dell'analisi includendo solo specifiche parti dell'origine dati, ad esempio cartelle, raccolte o schemi, selezionando gli elementi appropriati nell'elenco.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Definire l'ambito dell'analisi":::

1. Quindi selezionare un set di regole per l'analisi. È possibile scegliere tra l'impostazione predefinita di sistema e le regole personalizzate esistenti oppure crearne una nuova inline.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Set di regole per l'analisi":::

1. Scegliere il trigger dell'analisi. È possibile configurare una pianificazione oppure eseguire l'analisi una sola volta.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="trigger":::

1. Esaminare l'analisi, quindi selezionare **Salva ed esegui**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizzazione delle analisi e delle relative esecuzioni

Per visualizzare le analisi esistenti, procedere come segue:

1. Passare al centro di gestione. Selezionare **Origini dati** nella sezione **Sources and scanning** (Origini e analisi). 

2. Selezionare l'origine dati desiderata. Verrà visualizzato un elenco delle analisi esistenti per tale origine dati.

3. Selezionare l'analisi di cui visualizzare i risultati.

4. Questa pagina mostra tutte le analisi eseguite in precedenza insieme alle metriche e allo stato di ognuna. Visualizza anche se l'analisi è stata pianificata o avviata manualmente, il numero di asset con classificazioni applicate, il numero totale di asset individuati, le ore di inizio e di fine dell'analisi e la durata totale.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gestire le analisi: modificare, eliminare o annullare

Per gestire o eliminare un'analisi, procedere come segue:

1. Passare al centro di gestione. Selezionare **Origini dati** nella sezione **Sources and scanning** (Origini e analisi), quindi selezionare l'origine dati desiderata.

2. Selezionare l'analisi da gestire. Per modificare l'analisi, selezionare **Modifica**.

3. Per eliminare l'analisi, selezionare **Elimina**. 
