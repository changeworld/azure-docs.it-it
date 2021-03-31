---
title: Gestire le origini dati in Azure (anteprima)
description: Informazioni su come registrare nuove origini dati, gestire raccolte di origini dati e visualizzare le origini in Azure (anteprima).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694462"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Gestire le origini dati in Azure (anteprima)

Questo articolo illustra come registrare nuove origini dati, gestire raccolte di origini dati e visualizzare le origini in Azure (anteprima)

## <a name="register-a-new-source"></a>Registrare una nuova origine

Per registrare una nuova origine, attenersi alla procedura seguente.

1. Aprire competenza studio e selezionare il riquadro **registra origini** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure competenze Studio":::

1. Selezionare **Register**, quindi selezionare un tipo di origine. Questo esempio usa l'archiviazione BLOB di Azure. Selezionare **Continua**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Selezionare un tipo di origine dati nella pagina Registra origini":::

1. Compilare il modulo nella pagina **registra origini** . Selezionare un nome per l'origine e immettere le informazioni rilevanti. Se si sceglie la **sottoscrizione di Azure** come metodo di selezione dell'account, le origini nella sottoscrizione vengono visualizzate in un elenco a discesa. In alternativa, è possibile immettere manualmente le informazioni sull'origine.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Modulo per le informazioni sull'origine dati":::

1. Selezionare **Fine**.

## <a name="view-sources"></a>Visualizza origini

È possibile visualizzare tutte le origini registrate nella scheda **origini** di Azure competenza Studio. Sono disponibili due tipi di visualizzazione: vista mappa e visualizzazione elenco.

### <a name="map-view"></a>Vista mappa

Nella vista mappa è possibile visualizzare tutte le origini e tutte le raccolte. Nell'immagine seguente è presente un'origine di archiviazione BLOB di Azure. Da ogni riquadro di origine è possibile modificare l'origine, avviare una nuova analisi o visualizzare i dettagli dell'origine.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Visualizzazione mappa dell'origine dati di Azure.":::

### <a name="list-view"></a>Visualizzazione elenco

In visualizzazione elenco è possibile visualizzare un elenco ordinabile di origini. Passare il mouse sull'origine per le opzioni da modificare, iniziare una nuova analisi o eliminare.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Visualizzazione elenco delle origini dati di Azure.":::

## <a name="manage-collections"></a>Gestire le raccolte

È possibile raggruppare le origini dati in raccolte. Per creare una nuova raccolta, selezionare **+ nuova raccolta** nella pagina *origini* di Azure competenza Studio. Assegnare un nome alla raccolta e selezionare *None* come elemento padre. La nuova raccolta verrà visualizzata nella vista mappa.

Per aggiungere origini a una raccolta, selezionare la **casella** di testo modifica matita nell'origine e scegliere una raccolta dal menu a discesa **Seleziona raccolta** .

Per creare una gerarchia di raccolte, assegnare raccolte di livello superiore come padre a raccolte di livello inferiore. Nell'immagine seguente *Fabrikam* è un elemento padre della raccolta *Finance* , che contiene un'origine dati di archiviazione BLOB di Azure. È possibile comprimere o espandere le raccolte facendo clic sul cerchio collegato alla freccia tra i livelli.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Una gerarchia di raccolte in Azure competenza Studio":::

È possibile rimuovere origini da una gerarchia selezionando *Nessuna* per l'elemento padre. Le origini senza padre vengono raggruppate in una casella punteggiata nella visualizzazione mappa senza frecce che li collegano agli elementi padre.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare e analizzare varie origini dati:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Tenant Power BI](register-scan-power-bi-tenant.md)
* [Database SQL di Azure](register-scan-azure-sql-database.md)
