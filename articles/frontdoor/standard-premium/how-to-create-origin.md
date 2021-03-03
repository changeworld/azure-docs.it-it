---
title: Configurare un'origine di Azure front door standard/Premium (anteprima)
description: Questo articolo illustra come configurare un'origine con Gestione endpoint.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741889"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Configurare un'origine di Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra come creare un'origine standard/Premium di Azure front door in un gruppo di origine esistente. 

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un'origine standard/Premium di Azure front door, è necessario avere creato almeno un gruppo di origine.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Crea una nuova origine standard/Premium di Azure front door

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al profilo standard/Premium di Azure front door.

1. Selezionare **gruppo di origine**. Quindi selezionare **+ Aggiungi** per creare un nuovo gruppo di origine.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Screenshot della pagina di destinazione del gruppo di origine.":::

1. Nella pagina **Aggiungi un gruppo di origine** immettere un **nome** univoco per il nuovo gruppo di origine.

1. Quindi selezionare **+ Aggiungi un'origine** per aggiungere una nuova origine a questo gruppo di origine. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Screenshot della pagina Aggiungi un'origine.":::
  
    | Impostazione | Valore |
    | --- | --- |
    | Nome | Immettere un nome univoco per la nuova origine della porta di Azure. |   
    | Tipo di origine | Tipo di risorsa che si desidera aggiungere. Azure front door standard/Premium supporta l'individuazione automatica dell'origine dell'app dal servizio app, dal servizio cloud o dall'archiviazione. Se si vuole una risorsa diversa in Azure o in un back-end non di Azure, selezionare **host personalizzato**. |
    | Nome host  | Se non è stato selezionato **host personalizzato** per tipo di host di origine, selezionare il back-end scegliendo il nome host di origine nell'elenco a discesa. |
    | Intestazione host di origine | Immettere il valore dell'intestazione host da inviare al back-end per ogni richiesta. Per altre informazioni, vedere [intestazione host di origine](concept-origin.md#hostheader). |
    | Porta HTTP | Immettere il valore per la porta supportata dall'origine per il protocollo HTTP. |
    | Porta HTTPS | Immettere il valore per la porta supportata dall'origine per il protocollo HTTPS. |
    | Priorità | Assegnare le priorità alle diverse origini quando si vuole usare un'origine del servizio primario per tutto il traffico. Inoltre, fornire backup se l'origine primaria o di backup non è disponibile. Per altre informazioni, vedere [Priority](concept-origin.md#priority). |
    | Peso | Assegnare pesi alle origini diverse per distribuire il traffico in un set di origini, in modo uniforme o in base ai coefficienti di peso. Per ulteriori informazioni, vedere [weights](concept-origin.md#weighted). |
    | Stato | Selezionare questa opzione per abilitare l'origine. |
    | Regola | Selezionare i set di regole che verranno applicati a questa route. Per altre informazioni su come configurare le regole, vedere [configurare un set di regole per il front-end di Azure](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Durante la configurazione, le API non vengono convalidate se l'origine non è accessibile dagli ambienti di porte anteriori. Assicurarsi che la porta anteriore possa raggiungere l'origine.

1. Selezionare **Aggiungi** per creare la nuova origine. L'origine creata dovrebbe essere visualizzata nell'elenco di origine con il gruppo.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Screenshot dell'origine nella visualizzazione elenco.":::

1. Selezionare **Aggiungi** per aggiungere il gruppo di origine all'endpoint corrente. Il gruppo di origine dovrebbe essere visualizzato all'interno del pannello gruppo di origine.

## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare un gruppo di origine quando non è più necessario, fare clic su **...** e quindi selezionare **Elimina** dall'elenco a discesa.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Screenshot dell'eliminazione di un gruppo di origine.":::

Per eliminare un'origine quando non è più necessaria, fare clic su **...** e quindi selezionare **Elimina** dall'elenco a discesa. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Screenshot su come eliminare un'origine.":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui domini personalizzati, vedere [aggiunta di un dominio personalizzato](how-to-add-custom-domain.md) all'endpoint standard/Premium di Azure front door.
