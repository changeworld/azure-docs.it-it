---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807808"
---
Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, passare alla risorsa di archiviazione nel portale di Azure e selezionare la scheda **Storage Explorer**. Passare al contenitore, fare clic con il pulsante destro del mouse e scegliere **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL** in una posizione temporanea. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.