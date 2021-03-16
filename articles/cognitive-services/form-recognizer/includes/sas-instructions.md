---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467205"
---
Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, passare alla risorsa di archiviazione nel portale di Azure e selezionare la scheda **Storage Explorer**. Passare al contenitore, fare clic con il pulsante destro del mouse e scegliere **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Verificare che le autorizzazioni **lettura**, **scrittura**, **eliminazione** ed **elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL** in una posizione temporanea. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
