---
title: Cartelle di lavoro di monitoraggio di Azure Bring your own storage
description: Informazioni su come proteggere la cartella di lavoro salvando il contenuto della cartella di lavoro nella risorsa di archiviazione
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100618946"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Portare il proprio spazio di archiviazione per salvare le cartelle di lavoro

In alcuni casi è possibile che si disponga di una query o di una logica di business che si desidera proteggere. Nelle cartelle di lavoro di è disponibile un'opzione per proteggere le cartelle di lavoro salvando il contenuto delle cartelle di lavoro nell'archivio. L'account di archiviazione può quindi essere crittografato con le chiavi gestite da Microsoft oppure è possibile gestire la crittografia fornendo chiavi personalizzate. [Vedere la documentazione di Azure in crittografia del servizio di archiviazione.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Salvataggio della cartella di lavoro con identità gestite

1. Prima di poter salvare la cartella di lavoro nello spazio di archiviazione, è necessario creare un'identità gestita (tutti i servizi-> identità gestite) e concedere `Storage Blob Data Contributor` l'accesso all'account di archiviazione. [Vedere la documentazione di Azure sulle identità gestite.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Screenshot che illustra l'aggiunta di un'assegnazione di ruolo](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Creare una nuova cartella di lavoro.
3. Selezionare il pulsante **Salva** per salvare la cartella di lavoro.
4. È possibile `Save content to an Azure Storage Account` selezionare la casella di controllo per salvarla in un account di archiviazione di Azure.

    ![Screenshot che mostra una finestra di dialogo salvata](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Selezionare il contenitore e l'account di archiviazione desiderati. L'elenco di account di archiviazione si trova nella sottoscrizione selezionata in precedenza.

    ![Screenshot che mostra una finestra di dialogo Salva con l'opzione di archiviazione](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Selezionare quindi **Cambia** per selezionare un'identità gestita creata in precedenza.

    [![Screenshot che mostra la finestra di dialogo Cambia identità](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Dopo aver selezionato le opzioni di archiviazione, fare clic su **Salva** per salvare la cartella di lavoro.

## <a name="limitations"></a>Limitazioni

- Quando si salva nell'archiviazione personalizzata, non è possibile aggiungere singole parti della cartella di lavoro a un dashboard perché i singoli pin contengono informazioni protette nel dashboard stesso. Quando si usa l'archiviazione personalizzata, è possibile aggiungere solo i collegamenti alla cartella di lavoro ai dashboard.
- Una volta salvata una cartella di lavoro nell'archiviazione personalizzata, questa viene sempre salvata nell'archiviazione personalizzata e non può essere disattivata. Per salvare altrove, è possibile usare "Salva con nome" e scegliere di non salvare la copia nell'archiviazione personalizzata.
- Le cartelle di lavoro nella risorsa Application Insights sono cartelle di lavoro "Legacy" e non supportano l'archiviazione personalizzata. Le cartelle di lavoro più recenti in Application Insights risorsa sono le "... Altro "selezione. Le cartelle di lavoro legacy non dispongono di opzioni di sottoscrizione durante il salvataggio.

   ![Screenshot che mostra la cartella di lavoro legacy](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare una visualizzazione [mappa](workbooks-map-visualizations.md) nelle cartelle di lavoro di.
- Informazioni su come usare i [gruppi nelle cartelle di lavoro di](../visualize/workbooks-groups.md).