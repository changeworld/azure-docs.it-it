---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: b602cbfde22cc87b42a32b007c19b626814d1660
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554764"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Creare una risorsa di Servizi di comunicazione di Azure

Per creare una risorsa di Servizi di comunicazione di Azure, accedere innanzitutto al [portale di Azure](https://portal.azure.com). Nell'angolo superiore sinistro della pagina selezionare **+ Crea una risorsa**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Screenshot che illustra il pulsante Crea una risorsa nel portale di Azure.":::

Immettere **Comunicazione** in **Cerca nel Marketplace** o nella barra di ricerca nella parte superiore del portale.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Screenshot che illustra una ricerca di servizi di comunicazione nella barra di ricerca.":::

Selezionare **servizi di comunicazione** nei risultati, quindi selezionare **Crea**.

:::image type="content" source="../media/create-a-communication-resource/create-communication-portal.png" alt-text="Screenshot che mostra il pannello Servizi di comunicazione, evidenziando il pulsante Crea.":::

È ora possibile configurare la risorsa di Servizi di comunicazione. Nella prima pagina del processo di creazione verrà richiesto di specificare:

* La sottoscrizione
* Il gruppo di risorse (è possibile creare un nuovo gruppo di risorse o sceglierne uno esistente).
* Il nome della risorsa di Servizi di comunicazione
* La geografia a cui verrà associata la risorsa

Nel passaggio successivo è possibile assegnare tag alla risorsa. È possibile usare i tag per organizzare le risorse di Azure. Per altre informazioni sui tag, vedere la [documentazione relativa all'assegnazione di tag a una risorsa](../../../azure-resource-manager/management/tag-resources.md).

Infine, è possibile esaminare la configurazione e **creare** la risorsa. Si noti che il completamento della distribuzione richiederà alcuni minuti.

## <a name="manage-your-communication-services-resource"></a>Gestire la risorsa di Servizi di comunicazione

Per gestire la risorsa di Servizi di comunicazione, passare al [portale di Azure](https://portal.azure.com), cercare e selezionare **Servizi di comunicazione di Azure**.

Nella pagina **Servizi di comunicazione** selezionare il nome della risorsa.

La pagina **Panoramica** per la risorsa contiene le opzioni per la gestione di base, ad esempio Sfoglia, Arresta, Avvia, Riavvia ed Elimina. È possibile trovare altre opzioni di configurazione nel menu a sinistra della pagina della risorsa.
