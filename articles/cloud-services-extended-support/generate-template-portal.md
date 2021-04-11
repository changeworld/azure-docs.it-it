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
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077108"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Generare il modello ARM per i servizi cloud (supporto esteso) usando il portale di Azure

Questo articolo illustra come scaricare il modello ARM e il file dei parametri dal [portale di Azure](https://portal.azure.com) per il servizio cloud. Il modello ARM e il file dei parametri possono essere usati nelle distribuzioni tramite PowerShell per creare o aggiornare un servizio cloud

## <a name="get-arm-template-via-portal"></a>Ottenere il modello ARM tramite il portale

  1. Passare alla portale di Azure e [creare un nuovo servizio cloud](deploy-portal.md). Aggiungere i file di configurazione, di pacchetto e di definizione del servizio cloud. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Image Mostra la sezione upload della scheda nozioni di base durante la creazione.":::
  
  2. Una volta completati tutti i campi, passare alla scheda Revisione e creazione per convalidare la configurazione della distribuzione e fare clic su **Scarica modello per automatizzare** il servizio cloud (supporto esteso).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Immagine mostra il download del modello nel servizio cloud (supporto esteso) nel portale di Azure.":::
  
  3. Scaricare il modello e i file dei parametri. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Immagine che mostra il download del file modello nel portale di Azure.":::
  
  4. Copiare l'URI di firma di accesso condiviso del pacchetto e l'URI di firma di accesso condiviso dalla scheda verifica e crea e aggiungerli al parameter.jssu file. Questi file possono ora essere usati per creare un nuovo servizio cloud tramite PowerShell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Immagine mostra i parametri dell'URI di firma di accesso condiviso e dell'URI della firma di accesso condiviso nel portale di Azure.":::
  
## <a name="next-steps"></a>Passaggi successivi 
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md)
  
