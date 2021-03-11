---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623225"
---
Per recuperare l'URL di firma di accesso condiviso per i dati di training del modello personalizzato, passare alla risorsa di archiviazione nel portale di Azure e selezionare la scheda **Storage Explorer**. Passare al contenitore, fare clic con il pulsante destro del mouse e scegliere **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Verificare che le autorizzazioni **lettura**, **scrittura**, **eliminazione** ed **elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL** in una posizione temporanea. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
