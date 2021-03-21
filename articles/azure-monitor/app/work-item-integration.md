---
title: Integrazione di elementi di lavoro (anteprima)-Application Insights
description: Informazioni su come creare elementi di lavoro in GitHub o Azure DevOps con Application Insights dati incorporati.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731919"
---
# <a name="work-item-integration-preview"></a>Integrazione degli elementi di lavoro (anteprima)

La funzionalità di integrazione degli elementi di lavoro consente di creare facilmente elementi di lavoro in GitHub o in Azure DevOps che contengono dati Application Insights rilevanti incorporati.

> [!IMPORTANT]
> L'integrazione degli elementi di lavoro è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Creare e configurare un modello di elemento di lavoro

1. Per creare un modello di elemento di lavoro, passare alla risorsa Application Insights e, a sinistra, in *Configura* selezione **elementi di lavoro** , nella parte superiore selezionare **Crea un nuovo modello**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Screenshot della scheda elementi di lavoro con crea un nuovo modello selezionato." lightbox="./media/work-item-integration/create-work-item-template.png":::

    È inoltre possibile creare un modello di elemento di lavoro dalla scheda Dettagli transazione end-to-end, se attualmente non esiste alcun modello. Selezionare un evento e a destra selezionare **Crea elemento di lavoro**, quindi **iniziare con un modello di cartella** di lavoro.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Screenshot della scheda Dettagli transazione end-to-end con creare un elemento di lavoro, iniziare con un modello di cartella di lavoro selezionato." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Dopo aver selezionato **Crea un nuovo modello**, è possibile scegliere i sistemi di rilevamento, denominare la cartella di lavoro, collegarsi al sistema di rilevamento selezionato e scegliere un'area per l'archiviazione del modello (il valore predefinito è l'area in cui si trova la risorsa Application Insights). I parametri URL sono l'URL predefinito per il repository, ad esempio `https://github.com/myusername/reponame` o `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Screenshot della creazione di un nuovo modello di cartella di lavoro elemento di lavoro.":::

## <a name="create-a-work-item"></a>Creare un elemento di lavoro

 È possibile accedere al nuovo modello da qualsiasi dettaglio di transazione end-to-end a cui è possibile accedere da prestazioni, errori, disponibilità o altre schede.

1. Per creare un elemento di lavoro, passare a dettagli transazione end-to-end, selezionare un evento, quindi selezionare **Crea elemento di lavoro** e scegliere il modello di elemento di lavoro.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Screenshot della scheda Dettagli transazione end-to-end con l'opzione Crea elemento di lavoro selezionata." lightbox="./media/work-item-integration/create-work-item.png":::

1. Una nuova scheda del browser si aprirà al sistema di rilevamento selezionato. In Azure DevOps è possibile creare un bug o un'attività e in GitHub è possibile creare un nuovo problema nel repository. Un nuovo elemento di lavoro viene creato automaticamente con le informazioni contestuali fornite da Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Screenshot del problema di GitHub creato automaticamente" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Screenshot della creazione automatica di un bug in Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Modificare un modello

Per modificare il modello, passare alla scheda **elementi di lavoro** in *Configura* e selezionare l'icona della matita accanto alla cartella di lavoro che si desidera aggiornare.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Screenshot della scheda elemento di lavoro con l'icona modifica matita selezionata.":::

Selezionare modifica ![ icona modifica ](./media/work-item-integration/edit-icon.png) nella parte superiore per iniziare a modificare il modello. I modelli di elemento di lavoro sono basati sulle cartelle di lavoro di [monitoraggio di Azure](../visualize/workbooks-overview.md). Le informazioni sugli elementi di lavoro vengono generate utilizzando il linguaggio di query delle parole chiave. È possibile modificare le query per aggiungere altro contesto essenziale al team. Al termine della modifica, salvare la cartella di lavoro selezionando l'icona Salva icona ![ Salva ](./media/work-item-integration/save-icon.png) sulla barra degli strumenti superiore.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Screenshot della cartella di lavoro del modello di elemento di lavoro in modalità di modifica." lightbox="./media/work-item-integration/edit-workbook.png":::

È possibile creare più di una configurazione di elemento di lavoro e avere una cartella di lavoro personalizzata per soddisfare ogni scenario. Le cartelle di lavoro possono anche essere distribuite Azure Resource Manager garantendo implementazioni standard in tutti gli ambienti.