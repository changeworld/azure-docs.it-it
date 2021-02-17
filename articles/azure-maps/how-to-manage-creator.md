---
title: Gestire Microsoft Azure Maps Creator (anteprima)
description: In questo articolo si apprenderà come gestire Microsoft Azure Maps Creator (anteprima).
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559185"
---
# <a name="manage-azure-maps-creator-preview"></a>Gestire Azure Maps Creator (anteprima) 

> [!IMPORTANT]
> I servizi Creator di Mappe di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Creator di Mappe di Azure consente di creare dati della pianta di interni privati. Usando l'API di Mappe di Azure e il modulo Piante di interni, è possibile sviluppare applicazioni Web interattive e dinamiche per le piante di interni. Attualmente, Creator è disponibile solo negli Stati Uniti usando il piano tariffario S1.

Questo articolo illustra i passaggi per creare ed eliminare una risorsa Creator in un account di Mappe di Azure.

## <a name="create-creator-preview-resource"></a>Crea risorsa creatore (anteprima)

1. Accedere al [portale di Azure](https://portal.azure.com)

2. Selezionare il proprio account di Mappe di Azure. Se non è possibile visualizzare il proprio account di Mappe di Azure in **Risorse recenti**, passare al menu del portale di Azure. Selezionare **Tutte le risorse**. Individuare e selezionare il proprio account di Mappe di Azure.

    ![Home page del portale di Mappe di Azure](./media/how-to-manage-creator/select-maps-account.png)

3. Quando si è nella pagina dell'account di Mappe di Azure, passare all'opzione **Panoramica** in **Creator**. Selezionare  **Crea**  per creare una risorsa di Azure Maps Creator.

    ![Pagina per creare Creator di Mappe di Azure](./media/how-to-manage-creator/creator-blade-settings.png)

4. Immettere il nome e la posizione per la risorsa Creator. Attualmente, Creator è supportato solo negli Stati Uniti. Selezionare **Rivedi e crea**.

   ![Pagina per immettere le informazioni sull'account Creator](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Verificare le impostazioni e selezionare **Crea**.

    ![Pagina per confermare le impostazioni dell'account Creator](./media/how-to-manage-creator/creator-create-dialog.png)

6. Al termine della distribuzione, verrà visualizzata una pagina con un messaggio di esito positivo o negativo.

   ![Pagina dello stato di distribuzione delle risorse](./media/how-to-manage-creator/creator-resource-created.png)

7. Selezionare **Vai alla risorsa**. La pagina di visualizzazione delle risorse Creator mostra lo stato della risorsa Creator e l'area geografica scelta.

    ![Pagina relativa allo stato di Creator](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Dalla pagina della risorsa Creator è possibile tornare all'account Azure Maps a cui appartiene selezionando l'account Azure maps.

## <a name="delete-creator-preview-resource"></a>Elimina risorsa creatore (anteprima)

Per eliminare la risorsa Creator, passare all'account di Mappe di Azure. Selezionare **Panoramica** in **Creator**. Selezionare il pulsante **Elimina**.

>[!WARNING]
>Quando si elimina la risorsa Creator dell'account di Mappe di Azure, si eliminano anche i set di dati, i set di tessere e i set di stati della funzionalità creati con i servizi Creator.

![Pagina di Creator con il pulsante Elimina](./media/how-to-manage-creator/creator-delete.png)

Selezionare il pulsante **Elimina** e digitare il nome dell'autore per confermare l'eliminazione. Una volta eliminata la risorsa, viene visualizzata una pagina di conferma, come nell'immagine seguente:

![Pagina di Creator con la conferma dell'eliminazione](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Authentication

Creator (Preview) eredita le impostazioni di controllo di accesso (IAM) di Azure maps. Tutte le chiamate API per l'accesso ai dati devono essere inviate con regole di autenticazione e autorizzazione.

I dati di utilizzo di Creator sono incorporati nei grafici di utilizzo e nei log attività di Mappe di Azure.  Per altre informazioni, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Accesso ai servizi Creator

I servizi Creator (anteprima) e i servizi che usano i dati ospitati in Creator, ad esempio il servizio di rendering, sono accessibili da un URL geografico. L'URL geografico è determinato dalla posizione selezionata durante la creazione. Se, ad esempio, l'autore viene creato nella posizione geografica Stati Uniti, tutte le chiamate al servizio di conversione devono essere inviate a `us.atlas.microsoft.com/conversion/convert` .

Inoltre, tutti i dati importati in Creator devono essere caricati nella stessa località geografica della risorsa Creator. Se, ad esempio, il provisioning di Creator viene eseguito negli Stati Uniti, tutti i dati non elaborati devono essere caricati tramite `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Passaggi successivi

Introduzione ai servizi Creator (anteprima) per il mapping indoor:

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversione dati](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Set di dati](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Set di tessere](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Set di stati della funzionalità](creator-indoor-maps.md#feature-statesets)

Informazioni su come usare i servizi Creator (anteprima) per eseguire il rendering delle mappe interne nell'applicazione:

> [!div class="nextstepaction"]
> [Esercitazione di Creator di Mappe di Azure](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Stili dinamici per le piante di interni](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Usare il modulo Piante di interni](how-to-use-indoor-module.md)