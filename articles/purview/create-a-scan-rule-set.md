---
title: Creare un set di regole di analisi
description: Consente di creare un set di regole di analisi in Azure per analizzare rapidamente le origini dati nell'organizzazione.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554741"
---
# <a name="create-a-scan-rule-set"></a>Creare un set di regole di analisi

In un catalogo di Azure per le competenze è possibile creare set di regole di analisi che consentono di analizzare rapidamente le origini dati nell'organizzazione.

Un set di regole di analisi è un contenitore che consente di raggruppare insieme un set di regole di analisi, in modo che sia possibile associarle facilmente a un'analisi. Ad esempio, è possibile creare un set di regole di analisi predefinite per ogni tipo di origine dati e quindi usare questi set di regole di analisi per impostazione predefinita per tutte le analisi all'interno dell'azienda. Potrebbe inoltre essere necessario che gli utenti dispongano delle autorizzazioni appropriate per creare altri set di regole di analisi con configurazioni diverse in base alle esigenze aziendali.

## <a name="steps-to-create-a-scan-rule-set"></a>Passaggi per la creazione di un set di regole di analisi

Per creare un set di regole di analisi:

1. Dal catalogo di competenze di Azure selezionare **centro di gestione**.

1. Selezionare **Scan set di regole** dal riquadro sinistro, quindi selezionare **nuovo**.

1. Nella pagina **nuovo set di regole di analisi** selezionare le origini dati supportate dallo scanner del catalogo dall'elenco a discesa **tipo di origine** . È possibile creare un set di regole di analisi per ogni tipo di origine dati che si desidera analizzare.

1. Assegnare un **nome** al set di regole di analisi. La lunghezza massima è di 63 caratteri, senza spazi consentiti. Facoltativamente, immettere una **Descrizione**. La lunghezza massima è 256 caratteri.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Screenshot che mostra la pagina del set di regole di analisi." border="true":::

1. Selezionare **Continua**.

   Verrà visualizzata la pagina **Selezione tipi di file** . Si noti che le opzioni relative al tipo di file in questa pagina variano in base al tipo di origine dati scelto nella pagina precedente. Tutti i tipi di file sono abilitati per impostazione predefinita.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Screenshot che mostra la pagina Selezione tipi di file.":::

   La selezione dei **tipi di file di documento** in questa pagina consente di includere o escludere i tipi di file di Office seguenti:. doc,. docm,. docx,. dot,. ODP,. ods,. odt,. pdf,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. xlc,. xls,. xlsb,. xlsm,. xlsx e. xlt.

1. Abilitare o disabilitare un riquadro di tipo file selezionando o deselezionando la relativa casella di controllo. Se si sceglie un'origine dati di tipo Data Lake, ad esempio Azure Data Lake Storage Gen2 o BLOB di Azure, abilitare i tipi di file per cui si desidera che lo schema sia estratto e classificato.

1. Per determinati tipi di origini dati, è anche possibile [creare un tipo di file personalizzato](#create-a-custom-file-type).

1. Selezionare **Continua**.

   Verrà visualizzata la pagina **Selezione regole di classificazione** . In questa pagina vengono visualizzate le regole di **sistema** e le **regole personalizzate** selezionate e il numero totale di regole di classificazione selezionate. Per impostazione predefinita, sono selezionate tutte le caselle di controllo **delle regole di sistema**

1. Per le regole che si desidera includere o escludere, è possibile selezionare o deselezionare le caselle di controllo regola di classificazione **regole di sistema** a livello globale per categoria.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Screenshot che mostra la pagina Selezione regole di classificazione.":::

1. È possibile espandere il nodo Category e selezionare o deselezionare le singole caselle di controllo. Se ad esempio la regola per l' **Argentina. DNI numero** ha falsi positivi elevati, è possibile deselezionare la casella di controllo specifica.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Screenshot che illustra come selezionare le regole di sistema.":::

1. Selezionare **Crea** per completare la creazione del set di regole di analisi.

### <a name="create-a-custom-file-type"></a>Creare un tipo di file personalizzato

Azure competenza supporta l'aggiunta di un'estensione personalizzata e la definizione di un delimitatore di colonna personalizzato in un set di regole di analisi.

Per creare un tipo di file personalizzato:

1. Eseguire i passaggi da 1 a 5 nella [procedura per creare un set di regole di analisi](#steps-to-create-a-scan-rule-set) o modificare un set di regole di analisi esistente.

1. Nella pagina **Selezione tipi di file** selezionare **nuovo tipo** di file per creare un nuovo tipo di file personalizzato.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Screenshot che illustra come selezionare un nuovo tipo di file nella pagina Selezione tipi di file.":::

1. Immettere un' **estensione di file** e una **Descrizione** facoltativa.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Screenshot che mostra la nuova pagina tipo di file personalizzato." border="true":::

1. Effettuare una delle selezioni seguenti per il **contenuto** del file in per specificare il tipo di contenuto del file nel file:

   - Selezionare **delimitatore personalizzato** e immettere un **delimitatore personalizzato** (solo carattere singolo).

   - Selezionare **tipo di file di sistema** e scegliere un tipo di file di sistema, ad esempio XML, dall'elenco a discesa **tipo file** di sistema.

1. Selezionare **Crea** per salvare il file personalizzato.

   Il sistema torna alla pagina **Selezione tipi di file** e inserisce il nuovo tipo di file personalizzato come nuovo riquadro.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Screenshot che mostra il nuovo riquadro tipo di file personalizzato nella pagina Selezione tipi di file.":::

1. Selezionare **modifica** nel riquadro nuovo tipo di file se si desidera modificarlo o eliminarlo.

1. Selezionare **continua** per completare la configurazione del set di regole di analisi.

## <a name="system-scan-rule-sets"></a>Set di regole di analisi del sistema

I set di regole di analisi del sistema sono set di regole di analisi definiti da Microsoft che vengono creati automaticamente per ogni catalogo di competenze di Azure. Ogni set di regole di analisi del sistema è associato a un tipo di origine dati specifico. Quando si crea un'analisi, è possibile associarla a un set di regole di analisi del sistema. Ogni volta che Microsoft esegue un aggiornamento a questi set di regole di sistema, è possibile aggiornarli nel catalogo e applicare l'aggiornamento a tutte le analisi associate.

1. Per visualizzare l'elenco dei set di regole di analisi del sistema, selezionare **Analizza set di regole** nel **centro di gestione** e scegliere la scheda **sistema** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Screenshot che mostra l'elenco dei set di regole di analisi del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Ogni set di regole di analisi del sistema ha un **nome**, un **tipo di origine** e una **versione**. Se si seleziona il numero di versione di un set di regole di analisi nella colonna **versione** , vengono visualizzate le regole associate alla versione corrente e le versioni precedenti (se presenti).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Screenshot che mostra una pagina del set di regole di analisi del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Se è disponibile un aggiornamento per un set di regole di analisi del sistema, è possibile selezionare **Aggiorna** nella colonna **versione** . Nella pagina regola di analisi del sistema scegliere una versione dall'elenco **a discesa selezionare una nuova versione da aggiornare** . Nella pagina è disponibile un elenco di regole di classificazione del sistema associate alla nuova versione e alla versione corrente.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Screenshot che illustra come modificare la versione di un set di regole di analisi del sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Associare un'analisi a un set di regole di analisi del sistema

Quando si [Crea un'analisi](tutorial-scan-data.md#scan-data-into-the-catalog), è possibile scegliere di associarla a un set di regole di analisi del sistema come indicato di seguito:

1. Nella pagina **selezionare un set di regole di analisi** selezionare il set di regole di analisi del sistema.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Screenshot che illustra come selezionare un set di regole di analisi del sistema per un'analisi." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Selezionare **continua**, quindi selezionare **Salva ed Esegui**.
