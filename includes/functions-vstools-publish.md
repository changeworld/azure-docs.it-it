---
title: includere file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100529661"
---
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **pubblica** e in **destinazione**, selezionare **Azure** e quindi **Avanti**.

1. Per la **destinazione specifica**, scegliere **app per le funzioni di Azure (Windows)**, che consente di creare un'app per le funzioni che viene eseguita in Windows.

1. In **istanza della funzione** scegliere **Crea una nuova funzione di Azure...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Creare una nuova istanza dell'app per le funzioni":::

1. Creare una nuova istanza di utilizzando i valori specificati nella tabella seguente:

    | Impostazione      | Valore  | Descrizione                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente univoco | Nome che identifica in modo univoco la nuova app per le funzioni. Accettare questo nome o immetterne uno nuovo. I caratteri validi sono `a-z`, `0-9` e `-`. |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione di Azure da usare. Accettare questa sottoscrizione o selezionarne una nuova dall'elenco a discesa. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/management/overview.md)** | Nome del gruppo di risorse |  Gruppo di risorse in cui creare l'app per le funzioni. Selezionare un gruppo di risorse esistente dall'elenco a discesa oppure scegliere **Nuovo** per creare un nuovo gruppo di risorse.|
    | **[Tipo di piano](../articles/azure-functions/functions-scale.md)** | Consumo | Quando si pubblica il progetto in un'app per le funzioni eseguita in un [piano a consumo](../articles/azure-functions/consumption-plan.md), vengono addebitati solo i costi relativi alle esecuzioni dell'app per le funzioni. Altri piani di hosting comportano costi più elevati. |
    | **Posizione** | La località del piano di servizio app | Scegliere una **località** in un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |
    | **[Archiviazione di Azure](../articles/azure-functions/storage-considerations.md)** | Account di archiviazione per utilizzo generico | Per il runtime di Funzioni è richiesto un account di archiviazione di Azure. Selezionare **Nuovo** per configurare un account di archiviazione per utilizzo generico. È anche possibile scegliere un account esistente che soddisfi i [requisiti dell'account di archiviazione](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![Finestra di dialogo Crea servizio app](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Selezionare **Crea** per creare un'app per le funzioni e le relative risorse in Azure. Lo stato della creazione delle risorse viene visualizzato nella parte inferiore sinistra della finestra. 

1. Tornare all' **istanza di funzioni**, assicurarsi che l'opzione **Esegui dal file del pacchetto** sia selezionata. L'app per le funzioni viene distribuita usando la [distribuzione ZIP](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) con la modalità [esecuzione da pacchetto](../articles/azure-functions/run-functions-from-deployment-package.md) abilitata. Questo è il metodo di distribuzione consigliato per il progetto di funzioni, perché offre prestazioni più elevate. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Completare la creazione del profilo":::

1. Selezionare **Fine** e quindi, nella pagina Pubblica, selezionare **Pubblica** per distribuire il pacchetto contenente i file del progetto nella nuova app per le funzioni in Azure. 

    Al termine della distribuzione, l'URL radice dell'app per le funzioni in Azure viene visualizzato nella scheda **Pubblica**. 
    
1.  Nella scheda Pubblica scegliere **Gestisci in Cloud Explorer**. Verrà visualizzata la nuova risorsa dell'app per le funzioni di Azure in Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Messaggio di esito positivo della pubblicazione":::
    
    Cloud Explorer consente di usare Visual Studio per visualizzare il contenuto del sito, avviare e arrestare l'app per le funzioni e passare direttamente alle risorse dell'app per le funzioni in Azure e nella portale di Azure. 
