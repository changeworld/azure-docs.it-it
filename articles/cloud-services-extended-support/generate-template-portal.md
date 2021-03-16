---
title: Generare il modello ARM per i servizi cloud (supporto esteso) usando il portale di Azure
description: Generare e scaricare il modello ARM e il file di parametri per i servizi cloud (supporto esteso) usando il portale di Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555629"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Generare il modello ARM per i servizi cloud (supporto esteso) usando il portale di Azure

Questo articolo illustra come ottenere il modello ARM e il file di parametri dal [portale di Azure](https://portal.azure.com) dopo la distribuzione del servizio cloud (supporto esteso). Il modello ARM e il file dei parametri possono essere usati nelle distribuzioni future per aggiornare o aggiornare un servizio cloud (supporto esteso)

## <a name="get-arm-template-via-portal"></a>Ottenere il modello ARM tramite il portale

  1. Passare al gruppo di risorse e selezionare distribuzioni.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="Immagine mostra la selezione di distribuzioni in gruppo di risorse nel portale di Azure.":::
  
  2. Selezionare il servizio cloud (supporto esteso) e fare clic su modello.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="Immagine mostra la selezione di un modello in servizio cloud (supporto esteso) nella portale di Azure.":::
  
  3. Scaricare il modello e i file dei parametri. Questi possono essere usati per distribuzioni future tramite PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="Immagine che mostra il download del file modello nel portale di Azure.":::
  
## <a name="next-steps"></a>Passaggi successivi 
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md)
  
