---
title: includere file
description: includere file
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: a021b7c5b26e1bca1e3f623ad762c2f3ee8d6020
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97820477"
---
Creare l'app Pizza.

1. Selezionare [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) per aprire la pagina di GitHub per il file `pizza-app-for-luis.json`.
1. Toccare e tenere premuto a lungo o fare clic con il pulsante destro del mouse sul pulsante **Raw** e selezionare **Salva collegamento come** per salvare `pizza-app-for-luis.json` nel computer.
1. Accedere al [portale LUIS](https://www.luis.ai).
1. Selezionare [App personali](https://www.luis.ai/applications).
1. Nella pagina **App personali**, selezionare **+ Nuova app di conversazione**.
1. Selezionare **Importa come JSON**.
1. Nella finestra di dialogo **Importa nuova app** selezionare il pulsante **Scegli file**.
1. Selezionare il file `pizza-app-for-luis.json` scaricato e quindi selezionare **Apri**.
1. Nel campo **Nome** della finestra di dialogo **Importa nuova app** immettere un nome per l'app Pizza, quindi selezionare il pulsante **Fatto**.

L'app verrà importata.

Se viene visualizzata una finestra di dialogo **Come creare un'app di LUIS efficace**, chiudere la finestra di dialogo.

## <a name="train-and-publish-the-pizza-app"></a>Eseguire il training dell’app Pizza e pubblicarla

Verrà visualizzata la pagina **Finalità** con un elenco delle finalità dell'app pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Aggiungere una risorsa di creazione all'app Pizza

1. Selezionare **GESTISCI**.
1. Selezionare **Risorse di Azure**.
1. Selezionare **Risorsa Creazione**.
1. Selezionare **Change authoring resource** (Modifica risorsa Creazione).

Se è disponibile una risorsa Creazione, immettere un valore nei campi **Nome del tenant**, **Nome sottoscrizione** e **LUIS resource name** (Nome risorsa LUIS) della risorsa Creazione.

Se non è disponibile alcuna risorsa Creazione:

1. Selezionare **Crea nuova risorsa**.
1. Immettere un valore in **Nome del tenant**, **Nome della risorsa**, **Nome sottoscrizione** e **Nome gruppo di risorse di Azure**.

L'app Pizza è ora pronta per essere usata.

## <a name="record-the-access-values-for-your-pizza-app"></a>Prendere nota dei valori di accesso per l'app Pizza

Per usare la nuova app Pizza, sono necessari l'ID app, la chiave di creazione e l'endpoint di creazione dell'app Pizza. Per ottenere stime, l'endpoint di stima e la chiave di stima dovranno essere separati.

Per trovare questi valori:

1. Nella pagina **Finalità**, selezionare **GESTISCi**.
1. Nella pagina **Impostazioni applicazione**, registrare l’**ID app**.
1. Selezionare **Risorse di Azure**.
1. Selezionare **Risorsa Creazione**.
1. Nelle schede **Risorsa di creazione** e **Risorse di stima**, annotare la **Chiave primaria**. Questo valore corrisponde alla chiave di creazione.
1. Registrare l’**URL dell'endpoint**. Questo valore corrisponde all'endpoint di creazione.
