---
title: Pull Docker per il contenitore di integrità
titleSuffix: Azure Cognitive Services
description: Comando Docker pull per Analisi del testo per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089728"
---
Compilare e inviare il [modulo di richiesta di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso alla analisi del testo per l'anteprima pubblica dell'integrità.  Questa applicazione è valida sia per il contenitore che per l'anteprima pubblica dell'API Web ospitata.
Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo l'invio del modulo, il team di Servizi cognitivi di Azure lo esaminerà per verificare che siano soddisfatti i criteri di accesso al registro contenitori privato.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa Analisi del testo (endpoint di fatturazione e APIKEY) usata per eseguire il contenitore deve essere stata creata con l'ID sottoscrizione di Azure approvato. 
> * Controllare la posta elettronica (cartella posta in arrivo e cartelle indesiderate) per gli aggiornamenti sullo stato dell'applicazione da Microsoft.
> * Questo URL del contenitore è stato modificato. vedere gli esempi seguenti. Il contenitore non sarà disponibile per il download a `containerpreview.azurecr.io` partire dal 26 aprile 2021.


Una volta approvata, usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare l'immagine del contenitore dal registro contenitori pubblico Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
