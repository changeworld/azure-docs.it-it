---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505325"
---
Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, passare alla risorsa di archiviazione nel portale di Azure e selezionare la scheda **Storage Explorer**. Passare al contenitore, fare clic con il pulsante destro del mouse e scegliere **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL** in una posizione temporanea. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![testo alternativo](../media/quickstarts/get-sas-url.png)